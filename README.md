# Chrono-Gravity Learning Box

An interactive **3D multi-agent learning sandbox** built as a single HTML file. Agents navigate a bounded box, gather objectives, avoid threats, use shelters, adapt to gravity anomalies, exchange learned values, and enter **counterfactual replay portals** that create alternative simulated branches.

> **Important:** “Time portals” and “4D” are learning and visualisation mechanics. They do not represent real time travel, real extra dimensions, or a physically accurate gravity simulator.

## Highlights

- **3D bounded environment** rendered with Three.js and navigable with mouse orbit/zoom controls.
- **Counterfactual replay portals** that restore an agent’s stored local state from a few seconds earlier, move it through a linked portal, adjust its simulated W-coordinate, and record a new branch.
- **Gravity anomalies** with attractive or repulsive polarity that influence agent motion and the simulated W-axis.
- **4D teaching visual**: an animated 3D projection of a 4D tesseract, plus a learnable W-coordinate that affects agent appearance and state.
- **Multi-agent learning**: each agent maintains a local Q-table, nearby agents occasionally share learned values, and a slower collective Q-table provides a shared signal.
- **Lightweight model-based action scoring**: action choice combines local Q-values, collective knowledge, and a small heuristic rollout score.
- **Interactive controls** for agent count, movement speed, exploration entropy, replay horizon, gravity strength, W-axis influence, and simulation speed.
- **Evidence and anomaly log** for portal branches, gravity events, objective completion, agent loss, recovery, and exported traces.
- **Portable experiment traces** exported as JSON for later inspection.

## Download

- [Download the standalone simulator](./ChronoGravity_Learning_Box.html)
- [Download the complete project archive](./ChronoGravity_Learning_Box.zip)

The simulator also includes a **Download simulator** button, which saves a runnable copy of the current page.

## Quick start

### Option 1 — open locally

Open `ChronoGravity_Learning_Box.html` in a modern desktop browser.

### Option 2 — use a local web server

A local server is more reliable when browsers restrict module loading from `file://` URLs:

```bash
cd path/to/project
python -m http.server 8000
```

Then open:

```text
http://localhost:8000/ChronoGravity_Learning_Box.html
```

The page loads Three.js and OrbitControls from the unpkg CDN, so an internet connection is normally required unless you replace those imports with local copies.

## Controls

| Control | What it does |
| --- | --- |
| **Pause simulation / Resume simulation** | Stops or restarts the simulation loop. |
| **Reset world** | Rebuilds the world using the current agent-count setting. |
| **Add time portal** | Adds a linked pair of replay portals. |
| **Add gravity anomaly** | Adds an attractive or repulsive gravity field. |
| **Force replay branch** | Sends the nearest agent through the first available portal, where history exists. |
| **Export learning trace** | Downloads a JSON snapshot of configuration, metrics, events, collective values, and agent summaries. |
| **Agents** | Sets how many agents are created on the next reset. |
| **Agent speed** | Scales agent movement. |
| **Exploration / entropy** | Controls the chance of choosing a random action instead of the estimated best action. |
| **Replay horizon** | Sets how much recent simulation history is retained for portal replay. |
| **Gravity field strength** | Globally scales anomaly forces. |
| **4D W-axis influence** | Scales the simulated W-coordinate coupling and tesseract display. |
| **Simulation rate** | Controls simulation speed. |

### Camera

- **Drag** to orbit the camera around the box.
- **Scroll** to zoom.

## How the simulation works

### World entities

| Entity | Role |
| --- | --- |
| **Agents** | Learn action values, move through the box, communicate locally, and pursue survival/energy goals. |
| **Objectives** | Restore energy and provide positive reward when collected. |
| **Threats** | Pursue lower-energy agents and reduce health at close range. |
| **Shelters** | Restore energy and health when agents remain nearby. |
| **Time portals** | Trigger counterfactual replay from a retained local agent state and increment a branch count. |
| **Gravity anomalies** | Apply attractive or repulsive forces and couple to the W-coordinate. |

### Agent state and actions

An agent observes discretised distance bands for objectives, threats, anomalies, portals, its energy level, and the magnitude of its W-coordinate. It selects from:

```text
SEEK_OBJECTIVE
SEEK_SHELTER
AVOID_THREAT
AVOID_GRAVITY
RIDE_GRAVITY
SEEK_PORTAL
SHIFT_W
EXPLORE
```

Action choice blends:

1. The agent’s own Q-values.
2. The global shared Q-value estimate.
3. A short heuristic action score based on nearby resources, hazards, shelters, portals, energy, and W-state.

This is intentionally lightweight and visual. It is **not** a trained neural world model or a benchmark implementation.

### Counterfactual replay

The simulation stores short snapshots of each agent’s position, velocity, energy, health, and W-coordinate. When an agent enters a portal:

1. A past local snapshot is selected from within the configured replay horizon.
2. The agent restores that earlier state approximately.
3. It reappears toward the portal’s linked partner.
4. Its W-coordinate is perturbed based on the portal displacement.
5. A new simulated branch is counted and logged.

This produces an interpretable “what could have happened from an earlier state?” mechanic without asserting real causal changes to the past.

### Gravity and the W-axis

Each gravity anomaly has a random strength and polarity:

- **Attractive** anomalies pull agents inward.
- **Repulsive** anomalies push agents away.

Anomalies also nudge each agent’s simulated W-velocity. The W-coordinate appears through colour, glow, and the agent’s state representation. An animated wireframe tesseract visualises a rotating projection of a 4D hypercube into the 3D world.

## Metrics

The dashboard displays:

- **Alive agents**
- **Frame rate**
- **Mean reward**
- **Replay branches**
- **Shared states** in the collective Q-table
- **World stability**, derived from average agent health and current anomaly/branch conditions

These are exploratory indicators for visual experiments, not formal scientific validation metrics.

## Exported learning trace

Selecting **Export learning trace** downloads JSON containing:

- Simulation configuration and box bounds
- Current metrics and branch totals
- Recent evidence/anomaly events
- A capped subset of collective Q-values
- Per-agent energy, health, W-coordinate, branch count, current action, and learned-state totals
- Links to the project’s research and workflow inspirations

Suggested uses include comparing slider settings, reviewing branch behaviour, plotting reward over repeated runs, and building a separate analysis pipeline.

## Project structure

```text
.
├── ChronoGravity_Learning_Box.html  # Standalone simulator
└── README.md                        # Project documentation
```

## Design inspirations

The interface and experiment framing draw inspiration from the following projects and research links already referenced in the simulator:

- [Supermix](https://github.com/kai9987kai/Supermix) — collective/local-first workflow inspiration.
- [AI-Dem-Lab](https://github.com/kai9987kai/AI-Dem-Lab) — research-dashboard, entropy, anomaly-log, and evidence-framing inspiration.
- [DIMA: Diffusion-Inspired Multi-Agent World Model](https://arxiv.org/abs/2505.20922)
- [GAWM: Global-Aware World Model](https://arxiv.org/abs/2501.10116)
- [MATWM: Multi-Agent Transformer World Model](https://arxiv.org/abs/2506.18537)

These resources are **inspirations**, not claims that this project reproduces their architectures, training procedures, results, or benchmarks.

## Limitations

- The learning mechanism uses compact tabular Q-learning, local value sharing, and handcrafted scoring rather than a trained neural policy or world model.
- Random seeds are not fixed, so runs are not reproducible by default.
- The simulation is browser-based and intended for visual experimentation, not high-scale reinforcement-learning training.
- Gravity fields, portal replay, the W-coordinate, and the tesseract are designed for conceptual exploration rather than physical accuracy.
- External Three.js imports are CDN-based by default.

## Ideas for further development

- Add deterministic seeded runs and replayable experiment files.
- Record time-series reward, survival, energy, and action-distribution charts.
- Add a scenario editor for placing threats, goals, portals, and anomalies.
- Add A/B experiment presets and batch evaluation.
- Replace the heuristic scorer with an explicit learned dynamics model.
- Save and reload collective Q-tables or agent policies.
- Add accessibility controls, keyboard camera navigation, and reduced-motion mode.
- Support local Three.js dependencies for fully offline use.

## License

No license file is currently included. Add a license before redistributing or accepting outside contributions.
