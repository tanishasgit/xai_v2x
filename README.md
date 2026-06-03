# XAI V2X Decision System

**What happens when a self-driving car has to choose between hitting a pedestrian or getting rear-ended?**

That's not a hypothetical. That's Scenario 5 in this project. And the answer isn't obvious.

This project is a Python simulation of an Explainable AI (XAI) safety system for autonomous vehicles — built to handle the edge cases that most tutorials never touch. Every decision the system makes comes with a full explanation of *why*. Not just what the car did. Why it did it.

---

## The problem with AI in cars today

Most AI models are black boxes. You feed in sensor data, a decision comes out, and nobody — not the engineer, not the regulator, not the insurance company — can tell you what happened in between.

That's fine for recommending Netflix shows. It's not fine when the system is deciding whether to brake at 95 km/h on a flooded road.

**Explainable AI (XAI)** is the field trying to fix this. The idea is simple: every decision should have a traceable reason. Not a probability. Not a confidence score buried in a neural network. A reason — in plain English, auditable by a human.

This project demonstrates that idea from scratch.

---

## What this system actually does

It takes live vehicle telemetry — speed, distances, traffic signals, road conditions, V2X network alerts — and runs it through a rule engine that mirrors how a real autonomous vehicle safety layer works.

Three data sources feed in simultaneously:

- **On-board sensors** — radar distance readings and confidence scores
- **V2X mesh network** — real-time alerts from infrastructure (traffic lights, emergency vehicles, road geometry warnings)
- **Environmental layer** — road surface conditions, weather hazards

Every scenario produces three outputs: the decision, the exact rule that fired, and a plain-English explanation.

---

## The 5 scenarios — and why they're hard

### Scenario 1 — Pedestrian Emergency
The baseline. Pedestrian at 2.1 metres. System fires R1, emergency brake, done. This one is easy. It's here so you can see what a clean, unambiguous decision looks like before things get complicated.

### Scenario 2 — The Ghost Brake Problem
It's raining heavily. Radar detects something 4.5 metres ahead. Sensor confidence: 35%.

Here's the thing — radar in heavy rain picks up water spray as solid objects. A naive system emergency-brakes. On a motorway. At 60 km/h. That causes a pile-up.

This system detects the low confidence score and switches to cautious deceleration instead of slamming the brakes. It flags the reading for human confirmation rather than acting on bad data. This is called sensor fusion fault tolerance — and it's one of the most underrated problems in real AV engineering.

### Scenario 3 — The Invisible Ambulance
Green light. Clear road ahead. All sensors nominal. The car should just drive, right?

Except the V2X mesh network is broadcasting something the camera can't see: an ambulance is crossing the intersection from a blind angle. The system overrides the green light and stops. Infrastructure data beats local sensor data.

This is V2X preemption — and it only works if the system is designed to trust the network over its own eyes when the signal is credible.

### Scenario 4 — Physics vs Speed
95 km/h. Flooded road. Sharp curve ahead.

The sensors show no obstacle. The traffic light is green. A standard system keeps going.

But the physics say no. At that speed on a flooded surface, the tyres lose contact with the road before the curve. The system calculates that the vehicle's velocity exceeds safe friction thresholds and initiates heavy deceleration — before the car reaches the curve, not after.

Prevention, not reaction.

### Scenario 5 — The Kinematic Dilemma
This one doesn't have a clean answer.

Pedestrian at 3.2 metres ahead. A vehicle is tailgating closely from behind. A hard brake stops the car — and the truck behind drives straight into it. No brake means the car hits the pedestrian.

The system doesn't pick a winner. It does something more nuanced: progressive braking combined with a slight lateral lane shift — slowing down enough to reduce impact severity at the front while reducing the rear collision velocity at the same time.

It's not a perfect solution. There isn't one. But it's a reasoned, explainable trade-off — which is exactly what XAI is supposed to produce.

---

## Run it yourself

No installations. No dependencies. Just Python 3.

```bash
git clone https://github.com/tanishasgit/xai-v2x
cd xai-v2x
python xai_v2x_advanced.py
```

Each scenario streams to the terminal with colour-coded decisions and a full explanation trace.

Want to add your own scenario? Go to the `scenarios` list and add:

```python
{
    "id": "SCEN-006",
    "name": "Your scenario name",
    "ego_speed": 55,
    "vehicle_ahead_dist": 12.0,
    "pedestrian_dist": 25.0,
    "traffic_light": "YELLOW",
    "road_condition": "WET",
    "v2x_alert": "NONE",
    "sensor_confidence": 0.85
}
```

The system will evaluate it and tell you exactly what it would do and why.

---

## Concepts behind this project

| Term | What it means in plain English |
|---|---|
| **XAI — Explainable AI** | AI that tells you why it made a decision, not just what it decided |
| **V2X** | Vehicles talking to everything — other cars, traffic lights, road infrastructure, emergency services |
| **Sensor fusion** | Combining data from multiple sensors (radar, camera, lidar) to get a more reliable picture than any one sensor alone |
| **Neuro-Symbolic AI** | Combining neural networks (which learn) with symbolic rules (which reason) — the approach this system is inspired by |
| **Digital Twin** | A real-time virtual replica of a physical environment, used to simulate and test AI decisions before deploying them in the real world |
| **Kinematic dilemma** | A scenario where every possible action causes some harm — the system has to reason about which trade-off is least bad |

---

## Why rule-based and not a neural network?

Fair question.

Neural networks would perform better on raw accuracy in most of these scenarios. But in safety-critical systems, "probably right" isn't good enough. A neural network that brakes 99.7% of the time correctly will still cause crashes — and you'll have no idea why.

Rule-based systems are slower and less flexible. But every decision is auditable. You can read the code and understand exactly what the car will do in any situation. You can update a rule when the law changes. You can explain it to a judge.

This project is the symbolic half of Neuro-Symbolic AI — the part that reasons, explains, and enforces constraints. In a full production system, a neural network would handle perception (detecting the pedestrian, reading the road), and this layer would handle the safety decisions on top.

---

## What's next

- [ ] Add a confidence scoring layer so rules can fire with partial weights, not just true/false
- [ ] Visualise the decision trace as a flowchart in the terminal
- [ ] Build a simple neural perception stub that feeds sensor readings into the rule engine
- [ ] Log all decisions to a CSV for post-run audit analysis
- [ ] Add a scenario editor so you can test edge cases interactively

---

## Background and references

This project was built as an independent study on Neuro-Symbolic XAI for 6G V2X systems — exploring how explainable AI can be applied to autonomous vehicle safety before full neural integration.

- Garcez & Lamb (2020) — [Neurosymbolic AI: The 3rd Wave](https://arxiv.org/pdf/2012.05876)
- IEEE 9779322 — 6G V2X Architecture and Simulation
- [LTNtorch](https://github.com/tommasocarraro/LTNtorch) — Logic Tensor Networks (the full Neuro-Symbolic implementation this is a precursor to)
- [IBM Neuro-Symbolic AI](https://ibm.github.io/neuro-symbolic-ai/)
- MIT 6.S191 — [Neurosymbolic AI lecture](https://www.youtube.com/watch?v=4PuuziOgSU4)

---

*Built by Tanisha — independent research project, June 2025*
