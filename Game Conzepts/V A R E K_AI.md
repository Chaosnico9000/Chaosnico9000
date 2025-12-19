AI machen kannst du auf drei Arten: dumm, kompliziert, oder so, dass du als Solo-Dev überlebst. Wir nehmen Option 3.

Für VAREK (Tarkov-ish, aber nicht 10 Jahre Entwicklungszeit) ist das beste Setup:

Perception (sehen/hören) → erzeugt “Stimuli”

Decision (Utility/State/BT) → wählt Ziel

Action (Nav + Combat + Animation) → führt aus

Memory → merkt sich letzte Infos und sucht weiter

Damit wirkt die AI “smart”, ohne dass du GOAP-PhD brauchst.

1) AI-Design für VAREK: Minimal, aber glaubwürdig
NPC-Typen (Start)

Scav (patrouilliert, lootet, flieht, ruft Hilfe)

Hunter (sucht Spieler anhand Geräuschen/Spuren, setzt Druck)

Wildlife (einfach: wandernd, aggressiv bei Nähe)

Was Tarkov-Gefühl macht

Nicht mehr HP.

Sondern: Sichtlinien, Geräusche, Suchen, Flanken, Retreat, und “letzte bekannte Position”.

2) Entscheidungslogik: Utility AI (mein Favorit für Solo)

State Machines sind easy, aber werden schnell unwartbar. Behavior Trees sind gut, aber Tooling-lastig.

Utility AI ist perfekt: du gibst Aktionen Scores, höchste gewinnt.

Beispiele:

Attack Score hoch, wenn: Player sichtbar + Munition ok + Mut ok

Flank Score hoch, wenn: Player sichtbar + Deckung vorhanden

InvestigateNoise Score hoch, wenn: Lärm in Reichweite + Player nicht sichtbar

Flee Score hoch, wenn: HP low oder outnumbered

Das fühlt sich dynamisch an, ohne 200 Zustände zu bauen.

3) Unity-Implementierung (solider Standard)
Core-Bausteine

NavMeshAgent für Bewegung

Sensoren (Vision + Hearing)

Blackboard als Memory

UtilityBrain für Entscheidungen

Actions als kleine, testbare Klassen

4) Konkrete Code-Struktur (copy-paste-fähig)
Blackboard
public sealed class Blackboard
{
    public Vector3? LastKnownPlayerPos;
    public Transform? VisiblePlayer;

    public float Health01 = 1f;       // 0..1
    public float Courage01 = 0.7f;    // 0..1
    public float Ammo01 = 1f;         // 0..1

    public Vector3? LastHeardNoisePos;
    public float LastHeardNoiseTime;

    public bool HasRecentNoise(float now, float maxAgeSec)
        => LastHeardNoisePos.HasValue && (now - LastHeardNoiseTime) <= maxAgeSec;
}

Hearing (Geräusche als Events, nicht dauernd Physics.Overlap)
public readonly struct NoiseEvent
{
    public readonly Vector3 Pos;
    public readonly float Loudness; // 0..1
    public readonly float Time;

    public NoiseEvent(Vector3 pos, float loudness, float time)
    {
        Pos = pos; Loudness = loudness; Time = time;
    }
}

public static class NoiseBus
{
    public static event Action<NoiseEvent>? OnNoise;

    public static void Emit(Vector3 pos, float loudness)
        => OnNoise?.Invoke(new NoiseEvent(pos, Mathf.Clamp01(loudness), Time.time));
}


NPC Hearing-Komponente:

using UnityEngine;

public sealed class HearingSensor : MonoBehaviour
{
    [SerializeField] private float hearingRadius = 35f;
    [SerializeField] private float minLoudness = 0.15f;

    public Blackboard BB { get; private set; } = null!;

    private void Awake() => BB = new Blackboard();

    private void OnEnable() => NoiseBus.OnNoise += HandleNoise;
    private void OnDisable() => NoiseBus.OnNoise -= HandleNoise;

    private void HandleNoise(NoiseEvent e)
    {
        var dist = Vector3.Distance(transform.position, e.Pos);
        if (dist > hearingRadius) return;

        // einfache Dämpfung
        var perceived = e.Loudness * (1f - (dist / hearingRadius));
        if (perceived < minLoudness) return;

        BB.LastHeardNoisePos = e.Pos;
        BB.LastHeardNoiseTime = e.Time;
    }
}


Spielerseite: beim Schießen/rennen emitten:

// Beispiel: Footstep
NoiseBus.Emit(transform.position, loudness: 0.25f);

// Beispiel: Gunshot
NoiseBus.Emit(muzzlePos, loudness: 1.0f);

Vision (FOV + Raycast)
public sealed class VisionSensor : MonoBehaviour
{
    [SerializeField] private Transform eyes = null!;
    [SerializeField] private float viewDist = 45f;
    [SerializeField] private float fovDeg = 95f;
    [SerializeField] private LayerMask obstacleMask;
    [SerializeField] private LayerMask targetMask;

    public Blackboard BB { get; private set; } = null!;

    private void Awake() => BB = new Blackboard();

    public void Tick()
    {
        BB.VisiblePlayer = null;

        var hits = Physics.OverlapSphere(eyes.position, viewDist, targetMask);
        if (hits.Length == 0) return;

        // billig: nächstes Ziel
        var target = hits[0].transform;
        var dir = (target.position - eyes.position);
        var dist = dir.magnitude;
        dir /= Mathf.Max(dist, 0.001f);

        var angle = Vector3.Angle(eyes.forward, dir);
        if (angle > fovDeg * 0.5f) return;

        // LOS check
        if (Physics.Raycast(eyes.position, dir, dist, obstacleMask)) return;

        BB.VisiblePlayer = target;
        BB.LastKnownPlayerPos = target.position;
    }
}

Utility Actions
public interface IUtilityAction
{
    string Name { get; }
    float Score(Blackboard bb, Vector3 selfPos, float now);
    void Execute(Blackboard bb);
}


Attack / Investigate / Patrol:

using UnityEngine;
using UnityEngine.AI;

public sealed class AttackAction : IUtilityAction
{
    public string Name => "Attack";
    private readonly NavMeshAgent _agent;
    private readonly Transform _self;

    public AttackAction(NavMeshAgent agent, Transform self)
    { _agent = agent; _self = self; }

    public float Score(Blackboard bb, Vector3 selfPos, float now)
    {
        if (bb.VisiblePlayer == null) return 0f;
        // wenn low hp oder no ammo: eher nicht
        var s = 1.0f;
        s *= Mathf.Lerp(0.2f, 1f, bb.Health01);
        s *= Mathf.Lerp(0.1f, 1f, bb.Ammo01);
        s *= Mathf.Lerp(0.3f, 1f, bb.Courage01);
        return s;
    }

    public void Execute(Blackboard bb)
    {
        if (bb.VisiblePlayer == null) return;
        _agent.isStopped = false;
        _agent.SetDestination(bb.VisiblePlayer.position);
        // hier würdest du Aim/Shoot triggern (separates CombatSystem)
    }
}

public sealed class InvestigateNoiseAction : IUtilityAction
{
    public string Name => "InvestigateNoise";
    private readonly NavMeshAgent _agent;

    public InvestigateNoiseAction(NavMeshAgent agent) => _agent = agent;

    public float Score(Blackboard bb, Vector3 selfPos, float now)
    {
        if (bb.VisiblePlayer != null) return 0f;
        if (!bb.HasRecentNoise(now, 12f)) return 0f;

        var dist = Vector3.Distance(selfPos, bb.LastHeardNoisePos!.Value);
        return Mathf.Clamp01(1f - dist / 40f) * 0.8f;
    }

    public void Execute(Blackboard bb)
    {
        if (!bb.LastHeardNoisePos.HasValue) return;
        _agent.isStopped = false;
        _agent.SetDestination(bb.LastHeardNoisePos.Value);
    }
}

Brain (wählt höchste Action)
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;

public sealed class UtilityBrain : MonoBehaviour
{
    [SerializeField] private float thinkInterval = 0.25f;

    private NavMeshAgent _agent = null!;
    private VisionSensor _vision = null!;
    private HearingSensor _hearing = null!;
    private Blackboard _bb = null!;

    private readonly List<IUtilityAction> _actions = new();
    private float _nextThink;

    private void Awake()
    {
        _agent = GetComponent<NavMeshAgent>();
        _vision = GetComponent<VisionSensor>();
        _hearing = GetComponent<HearingSensor>();

        // Merge BBs: in echt würdest du eine BB teilen, hier simpel:
        _bb = new Blackboard();

        _actions.Add(new AttackAction(_agent, transform));
        _actions.Add(new InvestigateNoiseAction(_agent));
        // _actions.Add(new PatrolAction(...));
        // _actions.Add(new FleeAction(...));
    }

    private void Update()
    {
        // Sensor tick
        _vision.Tick();
        CopySensorsIntoBB();

        if (Time.time < _nextThink) return;
        _nextThink = Time.time + thinkInterval;

        var best = PickBestAction();
        best?.Execute(_bb);
    }

    private void CopySensorsIntoBB()
    {
        // In sauber: Sensoren haben Referenz auf dieselbe BB.
        // Hier quick & dirty:
        if (_vision.BB.VisiblePlayer != null)
        {
            _bb.VisiblePlayer = _vision.BB.VisiblePlayer;
            _bb.LastKnownPlayerPos = _vision.BB.LastKnownPlayerPos;
        }
        if (_hearing.BB.LastHeardNoisePos.HasValue)
        {
            _bb.LastHeardNoisePos = _hearing.BB.LastHeardNoisePos;
            _bb.LastHeardNoiseTime = _hearing.BB.LastHeardNoiseTime;
        }
    }

    private IUtilityAction? PickBestAction()
    {
        IUtilityAction? best = null;
        float bestScore = 0f;

        var pos = transform.position;
        var now = Time.time;

        foreach (var a in _actions)
        {
            var s = a.Score(_bb, pos, now);
            if (s > bestScore)
            {
                bestScore = s;
                best = a;
            }
        }
        return best;
    }
}


Das ist “klein”, aber skaliert: du fügst Actions hinzu (Flank, SearchLastKnown, TakeCover, Heal, CallForHelp) und feilst an Scores.

5) Was du als Nächstes baust (Reihenfolge, die nicht weh tut)

PatrolAction (Wegpunkte/POI-Routen)

SearchLastKnownAction (wenn Player weg ist, suche in einem Radius mit mehreren Punkten)

TakeCoverAction (Cover-Punkte oder simple “move lateral”)

FleeAction (HP low → Abstand gewinnen, LoS brechen)

Group AI light (2–4 NPCs teilen “LastKnownPlayerPos” im kleinen Radius)

6) Performance-Regeln, damit 30 NPCs nicht alles töten

Sensoren nicht jede Frame für alle:

Vision tick: 4–10 Hz

Hearing ist Event-basiert (NoiseBus), super billig

LOS Raycasts nur, wenn Target in FOV + Dist

AI “sleep” außerhalb Simulation Radius deiner Chunks

Wenn du willst, kann ich dir daraus direkt ein VAREK-AI-Paket machen (als Struktur + weitere Actions):

PatrolAction, SearchAction (spiral/ring)

FlankAction (2 candidate points)

CoverPoints als ScriptableObjects oder baked points in POIs

“Threat/Attention” Integration, damit Hunter nur kommt, wenn du dich wie ein Presslufthammer verhältst