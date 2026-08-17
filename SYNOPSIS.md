# Project Synopsis

## AI Real-World Simulation Platform
### A Multi-Agent Virtual Society for Real-World Behaviour and Safety Evaluation of AI Models

---

## 1. Abstract

Large Language Models (LLMs) are increasingly deployed as autonomous decision-makers, yet most benchmarks evaluate them in isolated, single-turn tasks that fail to capture how these models behave when placed in a persistent, evolving environment with real consequences. This project proposes the **AI Real-World Simulation Platform**, a headless, multi-agent virtual society in which each AI-driven "citizen" observes a shared world state, reasons about it through an LLM API call, and acts — with every action permanently altering the world for every other agent. Instead of a playable 3D game, the platform is built around a **real-time monitoring dashboard**, similar in spirit to a "mission control" system, that streams live agent decisions, computed social metrics, and comparative agent-model performance to the user. The goal is to reveal emergent, longitudinal behaviour patterns — cooperation, conflict, economic drift, trust erosion — that no single-prompt benchmark can expose.

---

## 2. Introduction and Motivation

Current AI evaluation largely measures *task accuracy* — can a model answer a question correctly, solve a coding problem, or pass a reasoning test. What is missing is an evaluation of **sustained behaviour**: how does an AI model act over time when it has memory of consequences, competing goals, limited resources, and other agents around it? This is precisely the gap highlighted in recent research on emergent multi-agent evaluation (see References), where a persistent "world" is used to expose behaviours that short benchmarks cannot — deception, cooperation, risk-taking, and social drift.

This project is inspired by that line of research. It is **not** a game and **not** a 3D simulation for entertainment. It is a research and monitoring tool: a controlled digital society in which AI models are the citizens, and the researcher (or evaluator) watches, measures, and compares their behaviour from a dashboard.

---

## 3. Problem Statement

There is no lightweight, self-hostable platform that allows a student or researcher to:

1. Place multiple AI models into a shared, persistent world.
2. Let each model make autonomous day-to-day decisions based only on what it can currently observe.
3. Log every decision permanently for later analysis.
4. Watch the social and economic consequences of those decisions unfold **live**, on a dashboard, with quantified metrics.
5. Compare how different AI models (e.g., different personalities or even different underlying LLMs) behave under identical starting conditions.

This project builds exactly that platform, scoped realistically for a final-year academic project.

---

## 4. Objectives

- To design a simulation engine that models a small society of AI-driven agents over a sequence of simulated "days" (ticks).
- To define an agent as a structured, quality-driven entity whose decisions are generated through LLM API calls rather than hard-coded rules.
- To separate world data into two clear categories — transient environmental data and permanent raw event data — for clean, analyzable data architecture.
- To compute quantitative social and economic metrics (employment, crime, inequality, trust, etc.) from the logged data.
- To build a real-time dashboard that visualizes the live event feed, metric graphs, and agent comparisons, and to generate a final summary report at the end of a simulation run.

---

## 5. What the Project Actually Is

At its core, this project has **three layers**:

### 5.1 Simulation Engine (Backend — the brain)
A headless (no visuals) system that maintains a **world state** — a structured record of every citizen, their job, money, health, relationships, and recent events. On every simulated "tick" (representing one day), the engine sends each agent its current observable state and asks it to decide what it will do that day. The agent's decision is then applied back onto the world state, which changes permanently for the next tick.

### 5.2 Data & Event Logging Layer
Every single decision an agent makes is recorded as a permanent, timestamped event in a database. This log becomes the **raw dataset** from which every statistic and every graph on the dashboard is later derived — nothing is calculated from live memory alone; everything traces back to a stored record.

### 5.3 Real-Time Dashboard (Frontend — what the user actually watches)
A browser-based interface that shows:
- A live, scrolling **event feed** ("Citizen 4 applied for a job", "Conflict reported between Citizen 2 and Citizen 7") updated instantly via WebSocket.
- **Live metric graphs** (employment rate, crime rate, wealth distribution, trust score) that redraw as the simulation progresses.
- An **agent/model comparison view**, placing different AI models or personality archetypes side by side.
- A **final report** generated automatically once the simulation ends, summarizing the entire run.

---

## 6. What Is an "Agent" in This Project

An agent in this simulation is **not** a 3D character or a graphical avatar. An agent is best understood as **a structured collection of qualities** — a data profile — combined with an AI model that reasons over that profile at every tick.

Concretely, each agent is defined by:

| Quality Type | Examples |
|---|---|
| Identity | name, age, profession |
| Personality traits | cautious, ambitious, impulsive, social, risk-taking |
| Economic state | current money, job status, assets |
| Goals | a primary life objective (e.g., "start a business", "stay financially safe") |
| Social state | relationships, trust levels, reputation |
| Physical/wellbeing state | health, stress level |

These qualities together form the agent's **profile**, which is passed as context to the AI model whenever a decision is required. The AI model does not control the agent's *identity* — it controls the agent's *choices*, given that identity. In this sense, the agent is the personality; the LLM is the "mind" that acts on behalf of that personality.

Agents are generated using a **hybrid archetype + AI-generation approach**:
1. A small number of core archetypes (e.g., ambitious worker, cautious shopkeeper, impulsive risk-taker, helpful professional, curious learner) are defined manually — this fixes the experimental design.
2. For each archetype, the AI model itself generates 2–3 unique variations (different names, exact numbers, and minor personality shading) in structured JSON format.

This keeps the simulation's design controlled and explainable in the project report, while still producing natural, non-repetitive diversity among the citizens.

---

## 7. How the System Works (Decision Loop)

Every simulated tick follows a fixed four-step loop for every agent:

```
OBSERVE  →  REASON  →  ACT  →  UPDATE
```

1. **Observe** — The engine assembles the current, relevant state of the world *as visible to that specific agent* (its own stats plus nearby events) and sends it to the AI model as a prompt.
2. **Reason** — An API call is made to the AI model (Claude/GPT or similar), which reasons over the agent's profile and the current observation, and decides on an action for that day (e.g., "apply for job", "spend money", "report a conflict", "help another citizen").
3. **Act** — The AI model returns its decision in a **structured JSON format** (action type, target, reasoning) rather than free text, so the engine can parse and apply it programmatically.
4. **Update** — The world state is modified according to the action's outcome (money changes, job status changes, relationship changes), and the action is permanently logged as an event.

This loop repeats for every agent, every tick, for the duration of the simulation (e.g., 10 agents × 30 days = 300 total decision/API calls for a full run).

---

## 8. Data Collection Model: Environment Data vs Permanent Data

A key design decision in this project is separating collected data into **two distinct categories**:

### 8.1 Environmental (Tick-Level) Data — Transient
This is the **snapshot** of the world that is generated fresh at every tick and handed to an agent purely to make its next decision. It includes things like current job openings, recent nearby events, prices, and the agent's own live stats at that moment. This data is *contextual* — it exists to give the AI model something to reason about *right now*, and is recalculated every tick rather than being the primary long-term record.

### 8.2 Permanent (Raw Event) Data — Persistent
This is the **immutable historical ledger** — every action, by every agent, on every tick, stored permanently in the database with a timestamp, agent ID, action type, and outcome. Unlike environmental data, this is never overwritten or discarded. It is the true source of truth for the project: every metric, graph, and report the dashboard shows is computed *only* from this permanent log, never from live memory. This also makes the simulation fully reproducible and auditable — a researcher can always trace a graph on the dashboard back to the exact raw events that produced it.

This separation keeps the system clean: environment data answers "what does the agent see right now?", while permanent data answers "what actually happened, historically, across the whole society?"

---

## 9. Metrics and Formulas

All dashboard metrics are derived mathematically from the permanent event log at each tick:

**Employment Rate**
```
Employment Rate (%) = (Employed Agents / Total Agents) × 100
```

**Crime / Conflict Rate**
```
Crime Rate (%) = (Reported Incidents in Period / Total Agents) × 100
```

**Wealth Inequality (Gini Coefficient)**
```
G = (Σ Σ |x_i - x_j|) / (2 × n² × mean(x))
```
where `x_i`, `x_j` are individual agent wealth values and `n` is the number of agents. A value near 0 indicates equal wealth distribution; a value near 1 indicates high inequality.

**Economic Growth Rate**
```
Growth Rate (%) = ((Total Wealth_t − Total Wealth_t-1) / Total Wealth_t-1) × 100
```

**Social Trust / Cohesion Score**
```
Trust Score = ((Positive Interactions − Negative Interactions) / Total Interactions) × 100
```

**Behavioural Consistency Score** (used for agent/model comparison)
```
Consistency (%) = (Actions Aligned With Stated Goal / Total Actions) × 100
```

These formulas are recalculated at the end of every tick and appended to a time-series, which is what powers the live graphs on the dashboard.

---

## 10. Final Prediction and Dashboard Output

At the end of a simulation run (a fixed number of ticks/days), the platform produces:

1. **Live Event Feed** — a chronological, human-readable stream of every decision made, updated instantly as the simulation runs.
2. **Live Metric Graphs** — line/bar charts for employment, crime, inequality, growth, and trust, updating tick-by-tick.
3. **Agent/Model Comparison Table** — side-by-side statistics for each agent (or, in advanced runs, each underlying AI model), allowing direct comparison of behaviour under identical conditions.
4. **Final Summary Report** — an auto-generated end-of-run report (exportable) summarizing overall society health, dominant behavioural trends, notable events (e.g., peak crime day, wealthiest agent), and a final "society score" combining the key metrics above.
5. **Prediction Layer (optional extension)** — using the historical time-series, a simple trend projection (e.g., linear regression on employment/crime trends) can forecast how the society is likely to evolve if the simulation were continued, giving the dashboard a predictive, not just descriptive, dimension.

---

## 11. System Architecture and Technology Stack

| Layer | Technology | Purpose |
|---|---|---|
| Backend | Node.js (Express) / Python (FastAPI) | Runs the simulation loop and AI API calls |
| AI Agents | Claude / GPT API, structured JSON output | Generates each agent's decision per tick |
| Database | SQLite / PostgreSQL | Stores permanent event log and world state |
| Real-Time Layer | WebSocket (Socket.io) | Pushes live updates to the dashboard without refresh |
| Frontend Dashboard | React + Chart.js / Recharts | Displays live feed, graphs, and comparisons |
| Hosting | Backend on Render/Railway, frontend on GitHub Pages/Vercel | Deployment |

---

## 12. Scope of the Project

To keep the project realistic and achievable within a final-year timeframe:

- **10 AI agents**, each representing one simulated citizen.
- **3–4 core systems**: economy/jobs, health, social/conflict, governance.
- **Simulated days as ticks** — each day, every agent makes one AI-driven decision.
- **Dashboard** with a live event log, 3–4 key metric graphs, and an agent comparison table.
- **Auto-generated summary report** at the end of each run.

---

## 13. Expected Outcome

By the end of this project, a working platform will exist where any number of AI models can be dropped into a shared simulated society and observed making autonomous, consequence-bearing decisions over simulated time. The dashboard will provide a live, quantified, and comparative view of their behaviour — offering a small-scale but genuine demonstration of emergent multi-agent AI evaluation, going beyond what static, single-turn benchmarks can show.

---

## 14. References

1. MDPI Research Paper — *Emergent Intelligence Evaluation Framework*: https://www.mdpi.com/2673-2688/6/8/190
2. Emergence AI — *Emergence World* (GitHub): https://github.com/EmergenceAI/Emergence-World
3. Full Project Explanation and Source Code (this repository): `<add-your-repo-link-here>`

> A QR code linking to a consolidated references page (containing all three links above) is included on the final page of this synopsis for quick access.

---
