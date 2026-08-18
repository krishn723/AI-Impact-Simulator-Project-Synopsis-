# AI Impact Simulation Platform

**A Multi-Agent Virtual Society for Real-World Behaviour and Safety Evaluation of AI Models**
---

## Overview

This project creates a persistent, rule-based digital society where AI models act as autonomous citizens/agents — managing their own virtual jobs, money, health, relationships, and decisions. Instead of scripted behaviour, each agent observes its environment and decides its own next action via an LLM API call. The platform tracks every action in real time and presents live metrics and outcomes through a web dashboard.

This is **not** a 3D game or visual simulation — it is a real-time monitoring and analytics dashboard built on top of a headless, data-driven simulation engine.

## Research Question

> *If an AI model is given real-world-like freedom, resources, responsibilities, and a social environment, how will it behave — and what impact will that behaviour have on the surrounding society?*

## Core Idea

An AI agent's decisions ripple through a shared, interconnected system — economy, employment, health, law, and social trust are all linked, so a single decision by one agent can cascade into measurable effects on the whole simulated society.


## How It Works

1. **Observe** — the system reads an agent's current state (money, health, job, location, goals) and relevant nearby world state.
2. **Reason** — this state is sent to an AI model (Claude/GPT API) as a structured prompt; the model returns a decision.
3. **Act** — the decision is validated against world rules and applied to the shared world state.
4. **Log** — the event (agent, action, outcome, timestamp) is recorded in the event database.
5. **Repeat** — the loop runs for every agent, every simulated "day," continuously.

Because all agents read and write to the same shared world state, one agent's actions (e.g. opening a shop) become visible to other agents in future ticks — creating organic, emergent interactions (trade, employment, conflict, cooperation) rather than scripted events.

## Architecture

| Layer | Responsibility | Tech |
|---|---|---|
| Simulation Engine | Runs the tick loop, calls AI agents, updates world state | Node.js / Python |
| AI Agents | Decide actions via LLM API calls with structured (JSON) output | Claude / GPT API |
| Event Database | Stores world state + full historical event log | SQLite / PostgreSQL |
| Live Streaming | Pushes new events to the dashboard in real time | WebSocket (Socket.io) |
| Dashboard | Live event feed, metric graphs, agent comparison, final report | React + Chart.js / Recharts |

## What the Dashboard Shows

- **Live event feed** — real-time stream of agent actions as they happen
- **Metric graphs** — employment rate, crime rate, economic output, trust score over time
- **Agent comparison** — side-by-side stats across multiple AI models tested under identical starting conditions
- **Final report** — auto-generated summary of an AI model's overall safety and stability profile at simulation end

## Metrics & Formulas

| Metric | Formula |
|---|---|
| Employment rate | `employed_agents / total_agents × 100` |
| Crime rate | `total_crimes / total_population × 1000` |
| Income inequality (Gini) | `Σ|income_i - income_j| / (2 × n² × mean_income)` |
| Trust score | `(positive_interactions - negative_interactions) / total_interactions` |
| Composite safety index | `w1·(1 - crime_rate) + w2·employment_rate + w3·trust_score + w4·stability_score` |

## Scope (MVP)

- 5–10 AI agents with distinct personalities/professions
- 3–4 core systems: economy/jobs, health, social/conflict, governance
- Simulated day-based ticks
- Real-time dashboard with live feed, key metric graphs, and a comparison view
- Auto-generated end-of-simulation report

## Why This Project

Combines AI/LLMs, multi-agent systems, software engineering, databases, web development, data analytics, and AI safety research into a single, evaluable platform — creating a controlled, measurable environment for discovering risky AI behaviour patterns before real-world deployment.

## References

- Emergence AI — [Emergence World: A Laboratory for Evaluating Long-Horizon Agent Autonomy](https://github.com/EmergenceAI/Emergence-World)
- [AI Impact on Modern Society, MDPI](https://www.mdpi.com/3452324)

