Escape from Tarkov – was da wirklich läuft

Engine

Unity (stark modifiziert)
Ja, wirklich. Dieselbe Engine, die auch Handyspiele antreibt. Nur halt bis zur Schmerzgrenze verbogen.

Rendering

Forward+ / Deferred Hybrid

Custom Shader (HLSL)

Aggressive LOD-Gruppen

Occlusion Culling bis zum Anschlag

Kein Nanite, kein Lumen, kein Magieknopf

Terrain

Unity Terrain

Aufgeteilt in Sub-Terrains

Manuelles Streaming je Map (keine Open World im Sinne von nahtlos)

Level-Design

Große Maps, aber:

Stark segmentiert

Viele Sichtblocker

Vertikalität statt Weitsicht

Netzwerk

Server-authoritative

Tickrate niedrig, aber deterministisch

Viel State-Reconciliation

Daher auch das berühmte „ich war schon in Deckung“-Gefühl

AI

Behavior Trees + Utility AI

Keine fancy ML-Sachen

Bots cheaten Wissen, nicht Skills

Physik

Unity PhysX

Stark vereinfacht

Kugeln sind Raycasts mit Zusatzlogik, keine echte Ballistik-Simulation

Warum es funktioniert

Fokus auf Detailtiefe, nicht Weltgröße

Jede Map ist handgebaut

Brutal optimiert, brutal unflexibel

DayZ – das alte Kriegspferd

Engine

Enfusion Engine (Bohemia Interactive)

Eigenentwicklung

Nachfolger der Arma-Engines

Rendering

Eigenes Deferred Rendering

Massive Sichtweiten

Vegetation mit GPU-Instancing

Sehr aggressives LOD-Switching (man sieht es, aber es funktioniert)

Terrain

Heightmap-basiert

Riesige Maps (Chernarus ≈ 230 km²)

Terrain ist statisch

Kein Voxel, keine echte Zerstörung am Boden

World Streaming

Grid/Tile-System

Zellen werden geladen/entladen

Objekte separat vom Terrain

Netzwerk

Server-authoritative

Sehr konservativ

Simulation läuft primär serverseitig

Desync ist ein Feature, kein Bug

AI

FSMs + Skripte

Sehr simpel

Zombies sind mehr Druckmittel als Gegner

Physik

Eigene Physiklösung + Teile von Bullet

Fahrzeuge notorious janky

Aber Infanterie stabil

Warum es funktioniert

Ein riesiger Spielplatz

Systeme sind simpel

Emergenz kommt aus Interaktion, nicht aus Komplexität

Direktvergleich (ohne Marketing-Blabla)
Bereich	Tarkov	DayZ
Engine	Unity (Custom Fork)	Enfusion (Custom)
Welt	Große Maps	Riesige Open World
Terrain	Unity Terrain	Heightmap Mesh
Streaming	Manuell	Grid-basiert
Zerstörung	Minimal	Minimal
Fokus	Taktik & Detail	Survival & Emergenz
Solo-Dev-tauglich?	❌	❌
Die unbequeme Wahrheit für dich

Wenn du DayZ 2.0 als Solo-Dev bauen willst:

Du willst nicht deren Technologien

Du willst deren Reduktion

Beide Spiele:

verzichten auf echte Zerstörung

nutzen simple AI

nutzen Heightmaps

simulieren weniger, als sie vorgaukeln

Wenn du willst, mache ich dir:

eine Solo-Dev-Technologie-Matrix

oder eine „80 % DayZ-Gefühl mit 20 % Aufwand“-Architektur

oder ein Unity/Unreal Minimal-Stack, der dich nicht zerstört

Sag nur, wie viel Realität du erträgst.ö