# Iphi?

**A decentralized, community-powered campus transit platform.**

CrowdBus eliminates commute uncertainty by putting real-time bus tracking in the hands of students themselves. Instead of relying on expensive fleet hardware, the system crowdsources live location data from riders—creating a self-sustaining transit network that scales with the student population.

## How It Works

Students "mine" utility tokens by contributing high-fidelity transit data as they ride. Contributions are verified through a **Proof-of-Contribution** model that combines:

- **Geofenced stop proximity** – Confirms you're at a valid bus stop
- **On-bus QR check-ins** – Validates you're actually on the bus

In exchange, contributors earn tokens that unlock premium features like real-time ETA projections and proactive route notifications.

## Why It Works

By decoupling tracking infrastructure from the physical fleet, CrowdBus delivers:

- **High availability** – More riders = more data points
- **Low overhead** – No costly GPS hardware on buses
- **Organic scaling** – The network grows with its users

## Architecture

Built on a modular microservices architecture with a mobile-first approach.

```
apps/
  mobile/       # React Native app
packages/
  shared/       # Shared utilities and types
services/       # Backend microservices
```

## Branching Strategy

This project follows a Git Flow branching model:

| Branch | Purpose |
|--------|---------|
| `prod` | Production-ready code. Only receives merges from `release` or `hotfix`. |
| `develop` | Main development branch. All features are integrated here. |
| `feature/*` | New features. Branch from `develop`, merge back to `develop`. |
| `hotfix/*` | Urgent production fixes. Branch from `prod`, merge to both `prod` and `develop`. |
| `release/*` | Release preparation. Branch from `develop`, merge to `prod` and `develop`. |

### Workflow

```
feature/* ──┐
            ├──► develop ──► release/* ──► prod
hotfix/* ───────────────────────────────────┘
```