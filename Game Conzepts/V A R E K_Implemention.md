1) Konkrete Chunk-Größe & Zahlenbeispiele
Empfehlung: 3 Ebenen (Chunk → Region → Welt)

Warum: Du willst kleine Einheiten fürs Streaming, aber größere fürs Organisieren/Speichern.

Ebene A: Chunk (Streaming-/Simulations-Einheit)

Chunk-Größe (horizontal): 256 m × 256 m

Höhenauflösung (nahe Kamera): z. B. 257×257 Samples (16-bit Height)

Warum 256 m?

Potenz von 2 → saubere Indizes

Klein genug für Sichtweite/Streaming

Groß genug, dass du nicht 5000 Chunks pro Minute nachladen musst

Zahlen:

Chunk-Fläche: 
0,256
 km
×
0,256
 km
=
0,065536
 km
2
0,256 km×0,256 km=0,065536 km
2

Earth-Fläche grob ~510 Mio. km² → theoretisch:

510
 
000
 
000
/
0,065536
≈
7,78
510000000/0,065536≈7,78 Milliarden Chunks

Klingt absurd (ist es). Aber du generierst ja nur die paar tausend, die ein Spieler je wirklich sieht.

Ebene B: Region (Speicher-/Content-Einheit)

Region-Größe: 4 km × 4 km

Chunks pro Region: 
4
/
0,256
=
15,625
4/0,256=15,625 → 16×16 = 256 Chunks

Region-Fläche: 16 km²

Zahlen:

Earth-Regions (4×4 km): 
510
 
000
 
000
/
16
≈
31,875
510000000/16≈31,875 Millionen Regionen

Ebene C: Welt (unendlich)

Weltkoordinaten laufen in int64/double (nicht float)

Seed + Koordinate → deterministische Erzeugung

Speicherbeispiele (realistisch für Solo-Dev)

Wichtig: Du speicherst nicht “Terrain-Dateien”. Du speicherst Deltas (was der Spieler verändert hat).

Beispiel 1: Spieler erkundet 100 Regionen (4×4 km)

Regionen: 100

Chunks: 100 × 256 = 25.600 Chunks

Wenn du im Schnitt pro Chunk 4 KB an Veränderungen speicherst (geöffnete Türen, gelootete Container, platziertes Lager, zerstörte Fenster etc.):

25.600
×
4
 KB
≈
102.400
 KB
≈
100
 MB
25.600×4 KB≈102.400 KB≈100 MB

Das ist völlig okay.

Beispiel 2: Spieler erkundet 1.000 Regionen

Chunks: 256.000

Bei Ø 2 KB Delta:

~512 MB
Immer noch machbar, vor allem wenn du komprimierst und alte Regionen „einfrierst“.

Praktische Streaming-Radien (damit’s läuft)

Du streamst nicht nach „Chunks“, sondern nach „Ringen“:

Load-Radius (Regionen):

nahe: 1 Region Radius → 3×3 = 9 Regionen

komfortabel: 2 Region Radius → 5×5 = 25 Regionen

Wenn 1 Region = 4 km, dann sind 25 Regionen = 20×20 km aktiv. Das ist gigantisch für Survival, aber die meisten Details müssen dann natürlich LOD sein.

2) Implementierungsstrategie
A) Unity-Strategie (C#-freundlich, Solo-Dev-tauglich)
Kernprinzipien

Deterministische Welt: WorldSeed + (RegionX, RegionZ) -> RegionDefinition

Floating Origin: Unity-Transforms bleiben nahe (0,0,0), globale Koordinaten führst du selbst in double.

Deltas speichern: Nur Änderungen persistieren, nicht die ganze Welt.

Datenmodelle (wichtig!)

Globaler Koordinatenraum

long RegionX, RegionZ

int ChunkX, ChunkZ innerhalb der Region (0..15)

double GlobalX, GlobalZ (Meter)

Key pro Chunk

z. B. ulong key = Hash64(seed, regionX, regionZ, chunkX, chunkZ)

Streaming-Architektur

WorldStreamer

kennt Player-Position → berechnet aktuelle Region

entscheidet, welche Regionen rein/raus müssen (z. B. 5×5)

RegionLoader

erzeugt Region (async)

lädt Delta-Datei (async)

erstellt Chunk-GameObjects/Entities nur bei Bedarf

ChunkRuntime

hält nur Simulation/Colliders/Interactables im Nahbereich aktiv

weit weg: nur Mesh/Impostor (oder gar nichts)

Terrain in Unity (zwei realistische Wege)

Weg 1 (am einfachsten): Unity Terrain pro Chunk/Region

Pro Region 1 Terrain (4×4 km) mit LOD/Detail-Tuning

„Mikro“-Details (Gras, Steine) pro Chunk nachladen

Weg 2 (besser skalierbar): Mesh-Terrain

Generiere Meshes pro Chunk mit Jobs/Burst

Colliders nur in Nähe aktiv

LOD: 256m Chunk hat z. B. LOD0/LOD1/LOD2

Persistenz (nicht overengineeren)

Pro Region eine Datei: region_{x}_{z}.bin

Inhalt:

geöffnete Türen (bitset)

Container-Loot-State (IDs + timestamps)

Player-built Objects (Position, PrefabId, Rotation)

Spuren (optional)

Kompression: LZ4/Deflate (je nach Geschmack)

Floating Origin (Unity Pflicht)

Wenn Player > z. B. 2 km vom Ursprung entfernt:

verschiebe alle aktiven Objekte um -offset

globale Koordinaten bleiben in double unberührt

Damit vermeidest du Float-Jitter bei „Erdgröße und größer“.

B) Unreal-Strategie (wenn du „neue Features“ wirklich ausnutzen willst)

Unreal ist hier stark, weil UE5 dir drei Dinge quasi serviert:

Large World Coordinates (weniger Float-Schmerz)

World Partition (Streaming großer Welten)

HLOD (billiger Fernbereich)

World Partition Setup (praktisch)

Grid Cell Size z. B. 256 m oder 512 m

Loading Range: z. B. 2–4 km je nach Zielplattform

HLOD: aktivieren, damit Ferne billig wird

Prozedural Content

UE5 PCG Framework für Vegetation/Props

Region/Cell-seed basiert auf Koordinaten (deterministisch)

Buildings als Module / Spawn-Blueprints

Persistenz (DayZ-Kern!)

Unreal speichert dir nicht automatisch „welcher Schrank ist gelootet“.

Du brauchst auch hier: Delta-System pro Cell/Region

Speichere:

Interactions (door state, loot state)

Spawned Objects (player-built)

Removed Objects (destroyed)

Technisch:

USaveGame für Player + Index

Chunk/Cell-Deltas als eigene Binärdateien (performanter als alles in ein SaveGame zu pressen)