## 🖥️ NexOS Scheduler — CPU Process Simulator

> _An interactive, browser-based CPU scheduling simulator that lets you explore, compare, and master operating system scheduling algorithms in real time._

---

## Inspiration

CPU scheduling is one of the most fundamental — and notoriously hard to visualise — concepts in operating systems. Most textbooks describe FCFS, Round Robin, and Priority Scheduling with static diagrams and dry tables. We wanted to build something you could actually **feel**: watch a Gantt chart animate tick by tick, see starvation warnings fire in real time, and experiment with an adaptive algorithm that switches strategies on the fly. NexOS Scheduler was born from that frustration with passive learning.

---

## What It Does

NexOS Scheduler is a fully self-contained, single-file web app that simulates CPU process scheduling from first principles. Here's what you can do with it:

**Core Simulation**
- Add up to 16 processes with custom arrival time, burst time, and priority
- Choose from **8 scheduling algorithms**: FCFS, SJF, SRTF, Priority (preemptive & non-preemptive), Round Robin, MLFQ, and the custom **Adaptive Hybrid** mode
- Step through the simulation tick-by-tick or run it at variable speed
- Watch a live **Gantt chart**, per-process state cards, and a full statistics table update in real time

**Analytics & Insights**
- 6-metric dashboard: avg wait time, turnaround time, response time, throughput, CPU utilisation, and context switches
- **Starvation detection** with inline warnings when processes are starved beyond a threshold
- **Anomaly detection** panel that flags scheduling inefficiencies mid-run
- **ML-style algorithm recommender** that scores each algorithm (A–D) against your current workload and explains its reasoning
- **What-if analysis**: tweak quantum size and see projected metric changes before committing

**Advanced Features (v4)**
- **Adaptive Hybrid Algorithm** — dynamically switches between FCFS, SRTF, and RR based on live workload signals (queue depth, average remaining burst, max wait time), with a decision log
- **Energy & Thermal simulation** — tracks CPU temperature, total energy consumption, throttle events, and cgroup quota enforcement
- **MLFQ queue visualisation** — see processes move between priority queues in real time
- **Heatmap view** — per-process CPU time heatmap across the simulation timeline
- **Batch runner** — run all algorithms against your process set in one click for instant comparison
- **Run History** with tagging, notes, side-by-side comparison of saved runs, and a detailed replay modal
- **CSV / perf-trace import** — drag and drop a trace file to seed the simulator with real workload data
- **Container / cgroup simulation** — set CPU quotas and watch throttle behaviour
- **Challenges & Achievements** — guided optimisation challenges (e.g. "keep avg wait < 5ms") and an achievement system that unlocks as you explore

**Learn Mode**
- In-app reference covering all 8 algorithms with complexity, starvation risk, and use-case breakdowns
- Real-world mapping (Linux CFS, Windows scheduler, database I/O scheduling, game engines)
- Guided lab exercises with objectives and hints

---

## How We Built It

NexOS Scheduler is built as a **single, dependency-light HTML file** — no build step, no server, no framework. The key technology decisions:

- **Vanilla JS + CSS custom properties** for the entire simulation engine and UI, keeping the bundle at zero
- **Chart.js** (CDN) for the trade-off radar and bar charts on the Compare and Tradeoff pages
- **CSS animations & transitions** throughout — the Gantt bars, process cards, and achievement toasts are all pure CSS
- The simulation core uses a **tick-based state machine** where each `simStep()` call advances time by one unit, evaluates the selected scheduling policy, updates process states (new → ready → running → waiting → done), and re-renders the view
- Algorithm selection is handled by a strategy pattern — each algorithm is a pure function that picks the next process from the ready queue, making it easy to extend
- The **Adaptive Hybrid** algorithm wraps the core `simStep()` and inspects live queue metrics each tick to decide which base algorithm to delegate to, logging every switch decision
- `localStorage` is used for run history, achievement state, and algorithm usage tracking — no backend required

---

## Challenges We Ran Into

- **Preemptive vs. non-preemptive correctness**: Getting SRTF and preemptive Priority to interrupt mid-burst correctly while keeping the Gantt chart contiguous took careful state management — idle slots, context switch penalties, and partial burst tracking all had to compose cleanly.
- **MLFQ implementation**: Multi-Level Feedback Queue requires aging, promotion/demotion logic, and per-queue quantum settings. Balancing visual clarity with algorithmic correctness across 3 queues was tricky.
- **Single-file architecture at scale**: At ~3,600 lines the file is large. Keeping CSS variables consistent, avoiding layout collisions between 9 pages, and ensuring all feature extensions (v4 wraps the original functions) don't break earlier behaviour required discipline.
- **Energy/thermal model**: Simulating CPU temperature and throttling in a meaningful but not misleading way — the model is intentionally simplified but visually responsive so learners can connect scheduling decisions to power behaviour.

---

## Accomplishments We're Proud Of

- A simulator that genuinely **teaches through interaction** — the step-by-step explainer, starvation badges, and ML recommender together form a feedback loop that helps users build intuition, not just watch numbers change
- The **Adaptive Hybrid algorithm** — it's not just a demo trick; it implements a real heuristic (queue depth + wait time + remaining burst) that mirrors how production schedulers blend policies
- **Zero-dependency, single-file deployment** — anyone can open it offline, fork it, embed it in a course, or extend it without a build toolchain
- A complete **gamification layer** (challenges, achievements, labs) that encourages systematic exploration of all 8 algorithms

---

## What We Learned

- How subtle the performance differences between scheduling policies are — SRTF is theoretically optimal for average wait time but its overhead and starvation risk make it impractical in many real workloads
- How much **visualisation design** matters for learning tools: the same simulation with and without the Gantt chart, state cards, and anomaly panel feels like a completely different learning experience
- That a well-structured **single-file app** can scale surprisingly far before needing a framework — but naming discipline and CSS custom properties are non-negotiable at this scale

---

## What's Next

- **Export to PDF/PNG** — generate a shareable report card for a simulation run
- **Real Linux perf trace import** — parse `perf sched` output directly so users can compare simulated vs. kernel behaviour on the same workload
- **Collaborative mode** — let two users configure competing process sets and race their schedulers
- **Priority inversion demo** — a dedicated scenario showing the classic Mars Pathfinder bug and how priority inheritance resolves it
- **Mobile-responsive layout** — the current UI assumes a wide screen; a simplified mobile view would open it to more learners
- **Pluggable algorithm API** — let users write and inject their own scheduling function in a sandboxed editor, making NexOS a platform rather than a fixed simulator
