Zielbild für VAREK

Core Loop (MVP):
Spawn → Scavenge (Loot) → Craft/Repair → Survive (Hunger/Temp/Health) → Move (POIs) → Extract/Build stash → Repeat
World: unendlich via Chunks (Streaming)
Persistence: Weltzustand + Playerzustand + Lootzustand (streamingfähig)

Architekturprinzipien (damit’s nicht explodiert)

Data-driven: Items, LootTables, Rezepte, POIs als Daten (JSON/ScriptableObjects), nicht als Code.

Systems > Scenes: Scenes sind nur Shells (Boot, Game). Alles Gameplay läuft über Services/Systems.

Save ist ein First-Class Citizen: Jede Gameplay-Änderung hat sofort Save/Load-Implikationen.

Feature-Gates: Alles bekommt Flags (DevConfig), damit du Dinge deaktivieren kannst.

Singleplayer-first, MP-friendly: Autorität und Simulation so bauen, dass “Server wäre möglich”, aber nicht jetzt.

High-Level Komponenten
1) Boot & Composition Root

GameBootstrapper initialisiert alles in definierter Reihenfolge.

ServiceLocator oder DI-Light (Zenject optional). Für Solo reicht oft ein sauberer “Composition Root”.

2) Domain Layer (Engine-agnostisch so weit möglich)

Item/Inventory (Stacks, Durability, Weight, Tags)

Crafting

Vitals (Health, Hunger, Thirst, Temperature, Stamina)

World Model (Chunks, POIs, Spawns)

Persistence Model (Save Records, IDs)

3) Runtime Systems (Unity)

WorldStreamer (Chunk loading/unloading)

LootSystem (spawn, respawn, container rules)

AI/SpawnSystem (später)

InteractionSystem (Raycast/Use, doors, containers)

Build/StashSystem (platzierbare Objekte)

TimeWeatherSystem (später, aber Interfaces jetzt)

4) Data Layer

ScriptableObjects/JSON:

ItemDef

RecipeDef

LootTableDef

POIDef

BiomeDef

IdRegistry (stabile GUIDs, niemals “Index in Liste”)

5) Persistence Layer (Streaming Save)

Chunk-basierte Saves: world/chunks/x_y.save

Globaler Save: world/global.save

Player Save: players/<profile>.save

Container/Entities haben stable IDs + “Chunk Owner”

Ordnerstruktur (bewährt, langweilig, gut)
/Assets/Varek
  /Bootstrap
  /Core
    /Domain
    /Services
    /Events
    /Utility
  /World
    /Streaming
    /Generation
    /POI
  /Gameplay
    /Inventory
    /Items
    /Crafting
    /Vitals
    /Interaction
    /Combat (optional)
  /AI (später)
  /UI
  /Data
    /Items
    /Recipes
    /LootTables
    /POI
    /Biomes
  /Persistence
  /Debug
  /ThirdParty

Key-Design: IDs und Save (das ist der Unterschied zwischen “Game” und “Techdemo”)
Stable IDs

Jeder Entity-Typ:

WorldObjectId (ulong oder GUID)

OwnerChunk (int2)

“Loot in Kiste” ist nicht “zufällig immer wieder neu”, sondern:

Container-ID → enthält Liste von ItemInstances

Rule: Wenn etwas existiert, hat es ID. Ohne ID gibt’s keine Persistenz.

Chunk Save Format (minimal, schnell)

ChunkSave:

ChunkCoord

Seed

List<WorldObjectRecord> (placed objects, doors states, containers, corpses)

LastVisitedTime

WorldObjectRecord:

Id

PrefabId (aus Registry)

Pos/Rot

StateBlob (kleines JSON/byte[] je Typ)

World: unendlich via Chunks

Chunk-Größe (Start):

256m x 256m (Unity Units: 1 = 1m)

Active Radius: 2 (also 5x5 Chunks = 25)

Simulation Radius: 1 (3x3 Chunks)

Warum: Streaming bleibt überschaubar, du kannst später auf 512 erhöhen, aber 256 ist fürs Debuggen stabil.

Generator:

BiomeMap (Noise) → POI-Spawns → Foliage → Loot seeds

Alles deterministisch: worldSeed + chunkCoord → reproduzierbar

Loot: “spawn once, then persist”

Du brauchst drei Loot-Arten:

Static containers (Häuser, Kisten, Spinde)

Haben Container-ID, Loot wird initial generiert und dann gespeichert.

Dynamic ground loot (selten, optional)

wird schnell unübersichtlich, MVP: weglassen oder stark begrenzen.

Event loot (AI drops, quests)

server-friendly, weil deterministisch oder direkt gespeichert.

Respawn-Regel (Early Access freundlich):

Container respawnt nur, wenn:

LastVisited > X Stunden UND

Container “leer” oder “unter Threshold”

Wichtig: verhindert Loot-Farming.

UI/UX Architektur (weil sonst UI zum Boss wird)

UI redet nicht direkt mit Gameplay-Klassen.

Nutze:

ViewModel-Pattern light (C# classes)

EventBus (GameEvents)

Beispiel Events:

InventoryChanged

VitalsChanged

InteractionPromptChanged

Fahrplan in Phasen (mit klaren Deliverables)
Phase 0: Pre-Prod (1–2 Wochen)

Ziel: Projektbasis, Tech-Loop steht.

Repo + CI (Build, Run tests)

GameBootstrapper

Logging + Debug Overlay (FPS, chunk coord, mem)

Data Registry (Items/Prefabs)

Save/Load Skeleton (leere Dateien)

Exit Criteria: Spiel startet, erstellt Welt, lädt wieder.

Phase 1: MVP Core Loop (4–8 Wochen)

Ziel: Spielbar, aber hässlich.

Character Controller (laufen, springen, stamina)

Interactions: Pick up, Use, Open container

Inventory (Stacks, weight), ItemInstances (durability optional)

Loot: Static containers + LootTables

Vitals: Hunger/Thirst/Health + Damage basics

Simple POIs: 3 Gebäudetypen

Chunk streaming + chunk save

Exit Criteria: 30 Minuten spielen ohne Save-Bugs.

Phase 2: Survival Depth (4–10 Wochen)

Ziel: “Ich will noch eine Runde” Effekt.

Crafting + Repair

Temperatur / Wetness (einfach, nicht Simulationsterror)

Day/Night + rudimentäres Weather

Enemies minimal (1–2 Typen) oder Wildlife

Stash/Build: platzierbare Kiste, Lagerfeuer

Exit Criteria: Spieler kann eine Basis-artige Stash-Route aufbauen.

Phase 3: Content & Systems Expansion (laufend)

Ziel: Early Access tragfähig.

Mehr POIs + Biome Varianten

Quest/Contract System (optional)

Weapons/Combat ausbauen

AI Spawn rules (Biome + time + threat)

Balancing Tooling (Editor windows, loot table preview)

Exit Criteria: Patch-Loop: Content nachschieben ohne Save zu brechen.

Phase 4: Co-op / Multiplayer Vorstufe (wenn SP stabil ist)

Ziel: Vorbereitung statt Big Bang.

Simulation in “Authoritative tick” kapseln

Commands/Events serialisierbar machen

Deterministic seeds beibehalten

Später: Host/Listen-Server

Exit Criteria: Du kannst Dinge als “Net Messages” ausdrücken, ohne alles umzuschreiben.

Minimaler Code-Schnitt (Interfaces die du sofort anlegen solltest)
public interface IGameService { void Initialize(); void Shutdown(); }

public interface ISaveService
{
    void SaveAll();
    void LoadAll();
    void SaveChunk(ChunkCoord coord);
    ChunkSave LoadChunk(ChunkCoord coord);
}

public interface IWorldStreamer
{
    ChunkCoord CurrentChunk { get; }
    void Tick(Vector3 playerPos);
}

public interface IInventoryService
{
    bool TryAdd(ItemInstance item);
    bool TryRemove(ItemId id, int amount);
    IReadOnlyList<ItemInstance> GetAll();
}


Damit kannst du später DI/Testing machen, ohne alles zu zerreißen.

Biggest Traps (damit du’s überlebst)

Ground Loot überall → Save explodiert.

Keine stable IDs → Save wird Schrott.

MP “direkt mitbauen” → nie fertig.

Zu früh “realistische” Systeme (Kälte, Krankheiten, Ballistik) → Content fehlt, Spiel tot.

Wenn du willst, kann ich dir als nächstes konkret liefern (ohne Nachfragen, einfach so):

ein komplettes Unity-Projekt-Skelett (Dateiliste + Klassen-Stubs)

ein ChunkSave JSON Schema + Beispiel-Dateien

ein LootTable System (ScriptableObjects + Weighted RNG + Preview im Editor)

oder eine Console-Prototyp-Architektur (C#), um World/Chunks/Loot/Save erst mal headless zu simulieren.