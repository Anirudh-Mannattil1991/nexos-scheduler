# NexOS — Interactive CPU Scheduling Lab

> A browser-based operating system scheduling simulator, analytics platform, and interactive learning environment — built entirely from scratch in vanilla HTML, CSS, and JavaScript.

---

## What is NexOS?

NexOS is a **process scheduler simulator** — one of the most fundamental utilities in any operating system. The OS scheduler decides which process runs on the CPU at every moment. It is the engine behind application responsiveness, system fairness, and throughput. Without a well-designed scheduler, every program on your computer would feel sluggish, unresponsive, or simply broken.

NexOS replicates this core OS function entirely in the browser, letting you build custom workloads, run them through six real scheduling algorithms, and observe exactly what the OS is doing — tick by tick.

No dependencies. No server. Open the HTML file and it runs.

---

## Demo & Links

| Resource | Link |
|---|---|
| **Live Demo** | *https://anirudh-mannattil1991.github.io/nexos-scheduler/* |
| **Demo Video** | *https://vimeo.com/1185551862?share=copy&fl=sv&fe=ci* |
| **GitHub Repository** | *https://github.com/Anirudh-Mannattil1991/nexos-scheduler* |

---

## How to Run

```bash
# Clone the repository
git clone https://github.com/your-username/nexos.git
cd nexos

# Open in browser — no build step, no dependencies
open nexos_v5.html        # macOS
start nexos_v5.html       # Windows
xdg-open nexos_v5.html    # Linux
```

That's it. NexOS is a single self-contained HTML file.

---

## Features Overview

NexOS is structured around eight tabs, each serving a distinct purpose:

| Tab | Purpose |
|---|---|
| **Learn** | Homepage — explains OS scheduling concepts, all 6 algorithms, real-world context, and a quickstart guide |
| **Simulator** | Core simulation — Gantt chart, process state cards, metrics bar, step-by-step mode |
| **AI Insights** | Dynamic workload analysis — algorithm rankings, estimated metrics, anomaly detection |
| **Analytics** | Post-run deep dive — distributions, CPU utilization over time, throughput curves, waiting time heatmap |
| **Challenges** | 6 guided scheduling puzzles — auto-load preset + algorithm, switch to Simulator |
| **Labs** | 4 structured experiments — Convoy Effect, Starvation, MLFQ, Round Robin quantum tuning |
| **Compare** | Side-by-side algorithm benchmarking with overlaid Gantt charts and a What-If quantum tuner |
| **History** | Searchable log of saved runs with tag filtering and one-click reload |

---

## Judging Criteria Breakdown

### 1. Functionality & Correctness — 40%

NexOS implements six complete, correct CPU scheduling algorithms — the same algorithms taught in OS courses and used in real kernels:

**FCFS (First-Come First-Served)**
Non-preemptive. Processes run in arrival order. Correctly handles simultaneous arrivals by PID order. Demonstrates the convoy effect when a long job blocks short ones.

**SJF (Shortest Job First)**
Non-preemptive. Keeps the current running process until completion, then selects the shortest among ready processes. Produces optimal average waiting time for non-preemptive scheduling.

**SRTF (Shortest Remaining Time First)**
Preemptive SJF. Re-evaluates at every tick — if a new arrival has a shorter remaining time than the current process, it preempts immediately. Achieves theoretical minimum average waiting time.

**Round Robin**
Preemptive, time-sliced. Maintains a proper FIFO rotation queue (`rrQueue`). Processes returning from I/O re-enter at the back of the queue. Context switches are counted correctly — only when the running PID actually changes. The time quantum is configurable from 1–20ms.

**Priority Scheduling**
Non-preemptive. Lower priority number = higher urgency. Tie-breaks by arrival time. Correctly identifies and flags starvation when a low-priority process's wait time exceeds 3× its burst time.

**MLFQ (Multi-Level Feedback Queue)**
Three-level queue. New processes enter Level 0 (highest priority). A process using its full quantum at level `n` is demoted to level `n+1`. Quantum doubles per level (q, 2q, 4q, capped at 16ms). Processes completing I/O re-enter at their current level. This mirrors the scheduler in Linux, macOS, and Windows.

**Simulation engine correctness:**
- I/O bursts fire probabilistically mid-execution. Processes in I/O are removed from all queues and re-inserted on return.
- Context switches are tracked precisely — only incremented when the running PID changes across ticks (not when the same process continues).
- Starvation is detected in real time: any ready process waiting more than 3× its burst time triggers a warning banner.
- Deadline tracking: processes with set deadlines are flagged `MISSED` in the stats table if they finish late.
- All six metrics are computed correctly: average turnaround time, average waiting time, average response time, CPU utilisation, throughput, and context switch count.

**Analytics correctness:**
- Jain's Fairness Index: `(Σwᵢ)² / (n · Σwᵢ²)` — ranges 0 to 1, where 1 is perfectly fair
- Waiting time variance: `Σ(wᵢ - w̄)² / n`
- CPU utilisation over time uses a sliding window across the Gantt log, not a naive overall average
- The waiting time heatmap shows exact millisecond values, percentage of maximum, deviation from average, and a four-tier severity classification (Low / Medium / High / Critical)

---

### 2. Custom Features — 20%

NexOS includes several features that most operating systems and simulators do not expose:

**Real-Time AI Workload Advisor**

Most OS schedulers are configured once and left static. NexOS analyses your process set continuously and predicts which algorithm will perform best *before you run anything*. Every time you add, edit, or delete a process, the AI engine recomputes:

- A score (0–100) for each of the 6 algorithms based on burst variance, arrival spread, I/O ratio, priority diversity, and process count
- An estimated average waiting time for the recommended algorithm
- The top anomaly: convoy risk, starvation risk, high variance warning, or a green "balanced workload" confirmation

A compact three-insight strip lives directly in the Simulator sidebar so you always have guidance without switching tabs. The full AI Insights tab shows ranked algorithm predictions with explanations and an anomaly detection panel that flags: convoy effect risk, starvation risk from priority imbalance, high burst variance, uniform workload (FCFS is fine), and large I/O-bound workloads.

This is the equivalent of an OS giving you a recommendation engine on top of its scheduler — something no real OS exposes to users.

**What-If Quantum Simulator**

In the Compare tab, a live slider lets you adjust the Round Robin time quantum from 1ms to 20ms. Every slider movement re-runs the full RR simulation synchronously and displays the delta in waiting time, context switches, and CPU utilisation vs. the baseline quantum. A contextual note explains the tradeoff at each quantum size. This lets you empirically find the optimal quantum for your specific workload — a decision real OS engineers make through benchmarking, now interactive.

**Explicit Waiting Time Heatmap**

Traditional heatmaps rely on color intensity — perceptually ambiguous and inaccessible. NexOS's heatmap replaces intensity with explicit data: each process card shows the exact wait time in milliseconds, its percentage of the maximum, its deviation from the group average (+/− vs avg), a proportional progress bar, and a named severity tier badge. Cards are sorted from worst to best. A legend defines all four tiers with explicit percentage thresholds.

**Guided Challenge System with Auto-Load**

Six predefined scheduling challenges (e.g. "Trigger the Convoy Effect", "Observe Process Starvation") auto-load the correct preset workload and algorithm directly into the Simulator when started. This closes the loop between theory and practice without requiring the user to manually configure anything.

**Run History with Replay**

Every saved run stores the full process configuration, all metrics, tags, and notes. The Reload button in History restores the complete state — processes, algorithm, and configuration — so you can return to any past experiment exactly where you left it.

---

### 3. Code Quality — 20%

**Single-file architecture.** The entire application — 2,400+ lines — is one self-contained HTML file with zero external dependencies except Chart.js (loaded from CDN) and Google Fonts. There is no build step, no bundler, no framework, and no server.

**Clear separation of concerns.** The codebase is organised into clearly delineated sections with comment headers:

```
CONSTANTS & STATE        — global variables, named clearly
PAGE ROUTING             — tab switching, activation logic
PROCESS QUEUE MODAL      — CRUD operations on the process list
SIMULATION ENGINE        — initSim, startSim, pauseSim, resetSim
SIMULATION STEP          — single-tick logic for all 6 algorithms
RENDER: GANTT            — interactive Gantt chart with hover tooltips
RENDER: QUEUE CARDS      — per-process state cards
RENDER: STATS TABLE      — per-process metrics table
METRICS                  — computeMetrics, clearMetrics
SIDEBAR AI               — compact 3-insight live panel
AI INSIGHTS TAB          — full analysis page render
ANALYTICS TAB            — charts, heatmap, fairness metrics
CHALLENGES               — definitions + start logic
LABS                     — definitions + start logic
COMPARE                  — runSimulationSync, renderCompare, updateWhatIf
SAVE / HISTORY           — persistence via localStorage
INIT                     — startup sequence
```

**Naming conventions.** All functions use camelCase verb-noun naming (`renderGantt`, `computeMetrics`, `runSimulationSync`). DOM IDs use kebab-case and match their logical role (`ganttChart`, `starveWarn`, `pqbCount`). CSS classes follow a BEM-like hierarchy (`.hm-card`, `.hm-card.tier-high`, `.hm-bar-strip`).

**State management.** Global state is minimal and explicit: `processes` (the source of truth for the process list), `simProcs` (runtime copies created fresh on each `initSim`), `ganttLog` (append-only timeline), `simFinished` (completed processes), and `compareResults` (keyed by algorithm name). There is no implicit mutation — simulation state never bleeds into the base `processes` array.

**Simulation correctness guards.** The sync simulation engine (`runSimulationSync`) used for Compare includes a `MAX_TIME` guard (`3 × total burst + 10`) to prevent infinite loops on edge-case inputs. The live engine uses `setInterval` and can be paused, reset, or stepped at any time without leaving orphaned state.

**Reactive updates.** AI Insights and the sidebar panel use a debounced timer (`triggerAIUpdate` → 200ms) to avoid re-computing on every keystroke during process editing. Analytics charts are destroyed before re-creation to prevent Chart.js memory leaks.

---

### 4. Explanation & Documentation — 20%

**In-app Learn page.** NexOS ships with a complete reference guide as its homepage:

- Hero section explaining what NexOS is and what it does
- 6-step quickstart guide
- 12-term glossary covering all key scheduling concepts (burst time, turnaround, starvation, convoy effect, Jain's index, preemption, etc.) with clear definitions
- All 6 algorithm cards with best-use cases, weaknesses, overhead rating, and a "Try it" button that loads the right preset
- Real-world context section covering Linux CFS, Windows thread scheduler, real-time systems, and cloud/hyperscale scheduling

**Step-by-step mode.** Every tick in step-by-step mode displays a plain-English explanation of the scheduling decision: which process ran, why it was chosen (e.g. "shortest remaining time = 3ms, preemptive"), how much time it has left, and which algorithm is active.

**Starvation alerts.** When a process's waiting time exceeds 3× its burst time, a warning banner appears in the Simulator identifying the affected processes by name.

**Anomaly detection panel.** The AI Insights tab explains each detected issue in a sentence — not just flagging it but describing the cause and suggesting which algorithm would address it.

**Inline tooltips.** Every Gantt chart bar shows a hover tooltip with the process name, start time, end time, duration, and remaining burst. This makes the timeline readable without referring to the stats table.

---

## System Design

```
┌─────────────────────────────────────────────────────┐
│                    NexOS State Layer                 │
│  processes[] ──► simProcs[] ──► ganttLog[]          │
│  (source of truth)  (runtime copy)  (timeline)      │
└─────────────┬───────────────────────────────────────┘
              │ initSim()
              ▼
┌─────────────────────────────────────────────────────┐
│              Simulation Engine (simStep)             │
│                                                     │
│  1. Advance I/O timers                              │
│  2. Arrive new processes into ready queue           │
│  3. Check if all done → computeMetrics()            │
│  4. Select next process (algorithm-specific)        │
│  5. Detect context switch                           │
│  6. Execute one tick (remaining--)                  │
│  7. Check I/O burst probability                     │
│  8. Check completion → finishTime, deadline         │
│  9. Increment waitTime for all ready processes      │
│ 10. Detect starvation                               │
│ 11. Render Gantt, QCards, Stats                     │
└─────────────────────────────────────────────────────┘
              │ on complete
              ▼
┌─────────────────────────────────────────────────────┐
│              Analytics & AI Layer                    │
│                                                     │
│  computeMetrics() → avgWT, avgTAT, avgRT,           │
│                     util%, throughput, ctxSwitches  │
│                                                     │
│  renderAnalytics() → Chart.js distributions,        │
│                      utilization curve,             │
│                      throughput curve,              │
│                      waiting time heatmap           │
│                                                     │
│  renderAIInsights() → algorithm scores,             │
│                       estimated metrics,            │
│                       anomaly detection             │
└─────────────────────────────────────────────────────┘
```

**Algorithm selection is fully isolated.** Each algorithm's logic lives in a clearly labeled block within `simStep`. Adding a new algorithm means adding one block — no other code changes required.

**The sync engine (`runSimulationSync`) mirrors the live engine exactly**, running to completion in a single call. It is used by the Compare tab to benchmark all 6 algorithms instantaneously on the same process set.

**Persistence** uses `localStorage` under the key `nexos_history`. Each saved run serialises the full process configuration and all computed metrics as JSON. Runs survive page reloads and can be restored with one click.

---

## Scheduling Algorithms Implemented

| Algorithm | Type | Preemptive | Best For | NexOS Implementation Notes |
|---|---|---|---|---|
| FCFS | Queue | No | Uniform batch | Strict arrival order; ties broken by PID |
| SJF | Greedy | No | Known burst times | Selects shortest among ready; keeps current running |
| SRTF | Greedy | Yes | Mixed workloads | Re-evaluates every tick; preempts on shorter arrival |
| Round Robin | Time-sliced | Yes | Interactive systems | FIFO `rrQueue`; I/O processes re-enter at back |
| Priority | Priority queue | No | Deadline-sensitive | Lower number = higher priority; starvation detection |
| MLFQ | Feedback | Yes | Unknown/mixed | 3 levels; quantum doubles per level; I/O-aware |

---

## Project Structure

```
nexos_v5.html       — Complete application (single file)
README.md           — This document
```

---

## Technologies Used

| Technology | Role |
|---|---|
| HTML5 | Structure and layout |
| CSS3 | Styling, animations, CSS variables for theming |
| Vanilla JavaScript (ES6+) | All simulation logic, DOM manipulation, state management |
| Chart.js 4.4 | Analytics charts (bar, line) loaded from CDN |
| Google Fonts | Syne (display), Share Tech Mono (monospace), DM Sans (body) |
| localStorage | Run history persistence |

No frameworks. No build tools. No backend.

---

## What Makes NexOS Different

Most process scheduler simulators show you a Gantt chart after the fact. NexOS does the following that most do not:

1. **Predicts before you run** — AI scoring tells you which algorithm is best for your specific workload before you press Run, with reasons.
2. **Explains every decision** — Step-by-step mode narrates each tick in plain English.
3. **Quantifies fairness** — Jain's Fairness Index is computed and displayed, not just wait time averages.
4. **Heatmap with actual numbers** — The waiting time heatmap shows ms values, % of max, and deviation from average — not just colors.
5. **What-If tuning** — The quantum slider re-runs the simulation live and shows you the delta, making algorithm tuning interactive rather than theoretical.
6. **Structured learning path** — Challenges and Labs guide users from concept to observation to insight with auto-loaded configurations.

---

*NexOS — Built for the OS Utilities Hackathon*
