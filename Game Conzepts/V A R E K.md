1. High Concept

VAREK ist ein kompromissloses Survival-Spiel in einer leeren, post-zivilisatorischen Welt, in der Zeit, Hunger, Kälte und Fehlentscheidungen gefährlicher sind als Gegner.

Kein Held.
Keine Quests.
Kein Reset.

Du existierst, solange du es schaffst.

2. Die Fantasie (das Gefühl)

Du bist allein.

Die Welt funktioniert weiter, egal ob du lebst oder stirbst.

Alles, was du findest, ist gebraucht, beschädigt oder unvollständig.

Sicherheit ist immer temporär.

Tod ist kein Failstate, sondern ein Endpunkt.

VAREK verkauft kein Chaos.
Es verkauft Anspannung.

3. Core Pillars (unverhandelbar)
🧱 1) Konsequenz

Kein schnelles Heilen

Kein Magie-Buff

Verletzungen bleiben

Fehler summieren sich

🌫️ 2) Einsamkeit

Keine Musik im Normalspiel

Keine Marker

Keine permanente UI

Welt erklärt sich selbst

🎒 3) Wert von Dingen

Jedes Item hat Gewicht, Volumen und Zustand

Improvisation schlägt Ausrüstung

Ein Messer ist wichtiger als eine Waffe

4. Core Gameplay Loop

Spawn

Zufällige Region

Minimal-Ausrüstung

Keine Karte

Orientieren

Gelände lesen

Geräusche nutzen

Landmarken merken

Looten

Gebäude, Wracks, Fahrzeuge

RNG-basiert, aber logisch

Stabilisieren

Hunger

Durst

Temperatur

Verletzungen

Entscheiden

Weiterziehen?

Bleiben?

Risiko eingehen?

Scheitern oder Überleben

Langsam

Fair

Endgültig

5. Welt & Prozedurale Generierung
Weltstruktur

Die Welt besteht aus Zonen

Jede Zone enthält:

Gelände-Typ (Wald, Ebene, Industrie, Dorf)

Wetterprofil

Loot-Tier

Gefahren

Zonen werden procedural verbunden, nicht zufällig gewürfelt.

Gebäude

Modular aufgebaut

Innenräume variieren

Türen, Fenster, Zustand zufällig

Loot immer kontextabhängig

6. Survival-Systeme (bewusst reduziert)
Statuswerte

Hunger

Durst

Temperatur

Erschöpfung

Blutung

Infektion

Kein Micromanagement.
Aber alles greift ineinander.

Beispiel:

Nass + Wind → schneller Auskühlen → Zittern → schlechtere Aktionen

7. Loot & Inventory
Inventar

Grid-basiert

Gewicht + Volumen

Kleidung bietet Slots

Item-Zustand

Neu

Gebraucht

Beschädigt

Alles kann kaputtgehen.
Reparieren ist besser als Finden.

8. Crafting (DayZ-Style, aber reduziert)

Kein Rezeptbuch.

Du kombinierst logisch:

Stoff + Alkohol → Verband

Messer + Stock → Werkzeug

Holz + Zünder → Feuer

Das Spiel sagt nicht „geht nicht“.
Es lässt dich scheitern.

9. Gefahren (keine Gegner-Spam)
Umwelt

Kälte

Regen

Dunkelheit

Einsturz

Feuer

Lebewesen (optional, selten)

Einzelne Tiere

Sehr wenige Infizierte

Immer gefährlich

Nie Farm-Content

Die Welt ist der Gegner.

10. Tod & Persistenz (extrem wichtig)
Tod

Charakter ist weg

Keine Rückkehr

Keine Stats retten

Welt bleibt

Türen bleiben offen

Feuerstellen bleiben

Leichen bleiben

Verlassene Lager bleiben

Ein neuer Charakter kann:

deinen alten Körper finden

deine Fehler sehen

deine Geschichte fortsetzen

Das ist VAREK.

11. Progression (Meta, aber minimal)

Kein Skilltree.

Freischaltbar:

neue Startregionen

neue Welt-Modifier

neue Schwierigkeitsgrade

kosmetische Dinge

Alles optional.
Die Welt bleibt unfair.

12. Präsentation & Stil
Grafik

Realistisch, aber reduziert

Gedämpfte Farben

Nebel, Regen, Dämmerung

Kein HUD-Müll

Sound

Wind

Schritte

Stoff

Atmen

Funkstörungen (selten)

Sound ist Gameplay.

13. Warum VAREK marktfähig ist

DayZ-Gefühl ohne Multiplayer-Frust

Sehr gut streambar

Emergent Stories

Prozedural → Replayability

Hardcore-Nische ist stabil

Das Spiel braucht keine Masse.
Es braucht die richtigen Spieler.

14. Realistischer Solo-Dev Scope
v0.1 (spielbar)

3 Zonen

10 Gebäudetypen

Grund-Survival

Permadeath

Save-System

v0.5

Wetter

Crafting

Tiergefahren

Weltpersistenz

v1.0

Balancing

Polish

Steam Release

Kein Multiplayer.
Kein Scope-Selbstmord.

15. Finaler Titel

V A R E K
Subtitle optional:

VAREK: One Life

VAREK: No Second Life








///PROZEDUAL GENERIERTE KARTEN
🌍 Prozedurale Weltgenerierung & Unendliche Karte
Grundidee

VAREK nutzt keine „Map“, sondern ein weltweites Generierungssystem, das theoretisch unendlich große Spielwelten erzeugen kann – größer als die Erde, ohne Ladebildschirme und ohne feste Grenzen.

Die Welt existiert nicht komplett, sondern entsteht nur dort, wo der Spieler ist – und bleibt dort bestehen, wo er war.

Kein Trick. Kein Fake-Endless.
Ein echtes System.


🧠 Welt ≠ Map

In VAREK ist die Welt kein Level, sondern eine Funktion:

Position + Seed = Weltzustand

Das bedeutet:

Jede Koordinate ist eindeutig

Jede Region ist reproduzierbar

Jede Entscheidung hinterlässt Spuren

Die Welt wird nicht „gebaut“, sie wird berechnet.



🧩 Weltaufbau in Ebenen (wichtig für Performance)
1️⃣ Planetare Ebene (Makro)

Kontinente

Ozeane

Gebirge

Klimazonen (kalt, gemäßigt, trocken, feucht)

Diese Ebene bestimmt:

Temperatur

Vegetation

Grundressourcen

Wetterprofile

➡️ Ergebnis: Die Welt fühlt sich natürlich an, nicht zufällig.



2️⃣ Regionale Ebene (Meso)

Die Welt ist in Regionen unterteilt (z. B. 8×8 km oder größer):

Jede Region hat:

Terrain-Typen (Wald, Ebene, Industrie, Siedlung)

Dichte von Gebäuden

Loot-Tier

Gefahrenprofil

Regionen werden:

on demand generiert

deterministisch aus dem Seed

persistent gespeichert, sobald der Spieler sie betritt


3️⃣ Lokale Ebene (Mikro)

Direkt um den Spieler herum:

Gebäude-Layouts

Innenräume

Loot-Platzierung

Türen, Fenster, Schäden

Umweltdetails

Diese Ebene ist:

hochdetailliert

nur im aktiven Bereich geladen

sofort entladbar

♾️ Unendlichkeit ohne Chaos
Kein klassisches „Infinite Noise“-Problem

VAREK nutzt strukturierte Prozeduralität, keine reine Perlin-Hölle.

Das heißt:

Wälder hören logisch auf

Dörfer entstehen an Straßen

Industrie folgt Infrastruktur

Ruinen folgen Geschichte

Die Welt wirkt endlich sinnvoll, auch wenn sie mathematisch unendlich ist.

🧭 Koordinaten statt Grenzen

Es gibt keinen Kartenrand

Keine unsichtbaren Wände

Keine „You reached the end“-Momente

Der Spieler kann:

Tage lang in eine Richtung laufen

immer neue Regionen entdecken

immer weiter vom Startpunkt entfernen

Die Welt urteilt nicht, sie läuft einfach weiter.

🧠 Persistenz: Die Welt merkt sich dich
Sobald eine Region betreten wird:

Ihr Zustand wird gespeichert

Türen bleiben offen

Leichen bleiben liegen

Feuerstellen bleiben sichtbar

Loot bleibt geplündert

Unbesuchte Regionen:

existieren nur als mathematische Möglichkeit

verbrauchen keinen Speicher

kosten keine Performance

➡️ Ergebnis: Unendliche Welt mit endlichem Speicherbedarf.

🧪 Seed-System

Jeder Spielstand hat einen globalen Seed

Dieser Seed definiert:

Kontinente

Klima

Grundverteilung

Lokale Zufälle werden abgeleitet, nicht neu gewürfelt

Das ermöglicht:

reproduzierbare Welten

optionale „Shared Seeds“

Community-Diskussionen („Ich hab da was gefunden bei X:-4213 / Z:9082“)

⚙️ Streaming & Performance (Solo-Dev-realistischer Teil)

Welt wird in Chunks geladen

Chunks haben Zustände:

Unentdeckt

Generiert

Persistiert

Alte Chunks können:

eingefroren

komprimiert

ausgelagert werden

Keine gigantische RAM-Nutzung.
Keine 10-Minuten-Ladezeiten.

🎮 Spielerfahrung (das Wichtigste)

Für den Spieler fühlt sich das so an:

Die Welt ist riesig

Orientierung ist ein Skill

Entfernung bedeutet Risiko

„Zurück nach Hause“ ist eine echte Entscheidung

Verirren ist real

Das ist DayZ-DNA, aber auf Systemebene sauber gelöst.

🧨 Warum das kein Feature-Wahn ist

Du baust:

ein Terrain-System

ein Chunk-System

ein Persistenz-Modell

Und bekommst:

unendliche Welt

Replayability

emergente Geschichten

Marketing-Argument („no map limits“)

Das ist hoher ROI, kein Scope-Monster.

🔑 Zusammenfassung in einem Satz (fürs Konzept)

VAREK nutzt ein deterministisches, prozedurales Weltsystem, das theoretisch unbegrenzte Spielwelten erzeugt, während nur die tatsächlich besuchten Regionen persistent gespeichert werden.

Wenn du willst, mache ich als Nächstes:

eine konkrete Chunk-Größe & Zahlenbeispiele

eine Unity- oder Unreal-Implementierungsstrategie

oder eine vereinfachte Version, falls du merkst: „Okay, unendlich ist mir gerade zu viel“

Sag’s. Ich skalier das System, ohne es kaputtzumachen.