---
title: "Quality Attributes: The Architecture Decisions Nobody Writes Down"
date: 2026-04-04T10:00:00+07:00
draft: false
author: VictorNM
tags: ["architecture"]
---

You can specify "99.9% availability" and "< 200ms latency" perfectly in a requirements doc. But a spec doesn't tell you *how* to build it, or what you'll sacrifice to get there.
<!--more-->

That gap — between quality requirements and architectural decisions — is where most systems silently go wrong. Teams argue about priorities without a shared vocabulary. Architects make ad-hoc trade-offs based on intuition. And when something breaks in production, nobody can explain *why* the system was built this way.

Quality attributes give you a structured way to close that gap.

## Functional vs. Quality: A Critical Distinction

Functional requirements describe *what* the system does. Quality attributes describe *how well* it does it — performance, security, availability, modifiability.

The important difference isn't definitional. It's architectural. Functional requirements are usually local: add a feature, change a behavior, wire up a new endpoint. Many quality attributes — especially availability, performance, and security — cut across the entire system. They're deeply shaped by the architecture's structure.

Can you retrofit them? Sometimes. Netflix evolved from a monolith to a highly available microservices architecture incrementally. Teams add caching layers and CDNs to existing systems all the time. But retrofitting is expensive, constrained by existing structure, and the further you are from the original design intent, the more it costs. It's the difference between earthquake-proofing a building during construction versus reinforcing it after it's standing — possible, but far cheaper to get right the first time.

## Making "-ilities" Concrete with Scenarios

"The system should be scalable" is the kind of requirement that sounds useful and means nothing. Scalable how? Under what conditions? Measured by what?

A quality attribute scenario forces you to be specific. It has six parts:

- **Source**: Who or what triggers the event (1,000 concurrent users)
- **Stimulus**: The event itself (submit checkout requests)
- **Artifact**: The component affected (order service)
- **Environment**: The system state (Black Friday, 10x normal load)
- **Response**: What the system does (process all orders)
- **Response Measure**: The quantifiable outcome (p95 latency < 500ms)

Now you have something you can actually test. You can design for it, benchmark against it, and when someone pushes back, you have data instead of opinions. "The system should be scalable" becomes "the order service handles 10x load spikes with p95 latency under 500ms." That's a requirement an architect can actually work with.

## Tactics: The Building Blocks of Architecture

Once you have concrete scenarios, you need design primitives to achieve them. These are *tactics* — more fundamental than patterns, and often overlooked.

Every pattern you know is composed of tactics. A circuit breaker pattern combines *exception detection* (counting failures to decide a dependency is unhealthy) with *removal from service* (stopping requests to it while it recovers), plus state logic for the half-open probe. Understanding tactics means you can reason about *why* a pattern works, not just *when* to apply it.

Some examples for availability: heartbeats between components, redundancy for critical paths, automatic failover, rollback to known-good states. For performance: caching, concurrency, connection pooling, load balancing, request prioritization.

The value of thinking in tactics is that you're not locked into pre-packaged patterns. You pick the specific building blocks your scenario demands.

## Trade-offs: The Real Work

This is where it gets tricky: quality attributes inherently conflict, and every decision is a trade-off.

| Improving... | Often hurts... | Because |
|---|---|---|
| Security | Performance | Encryption and validation add overhead |
| Security | Usability | MFA and strong passwords add friction |
| Performance | Modifiability | Optimizations couple code tightly |
| Availability | Cost | Redundancy means more infrastructure |

If you've ever been in a design review where every quality attribute is marked "critical" — you've probably noticed that doesn't actually help anyone make decisions.

When I hit a slow component, my instinct is to exhaust the simple, trade-off-free options first. Optimize the SQL query. Tune the connection pool. These are cheap and don't introduce new complexity. Caching? That brings invalidation logic and eventual consistency — a real trade-off. I'd rather reach for it only when the simpler options aren't enough.

A practical framework for navigating trade-offs:

1. **Rank explicitly.** Force-rank your quality attributes for each component. If everything is critical, nothing is.
2. **Define "good enough."** Secondary attributes don't need to be maximized. Setting a threshold frees your focus for what actually matters.
3. **Start simple.** Exhaust low-trade-off options before introducing complexity. You can always add a cache later; removing one that's causing stale data bugs is harder.
4. **Document the why.** Future maintainers need to understand why you chose performance over modifiability *in this component*. The reasoning is impossible to reconstruct later.
5. **Revisit periodically.** A startup's priorities at 100 users look nothing like its priorities at 10 million.

## Architecturally Significant Requirements

Not all quality attributes deserve equal attention. Some are *architecturally significant* — they fundamentally shape the system's structure:

- "99.99% availability" → forces multi-region, active-active deployment
- "< 10ms response time" → constrains your database choices and caching strategy
- "10M concurrent users" → demands horizontal scaling from day one

These are the requirements that are high business value *and* technically challenging. They affect multiple components. They're expensive to change after the fact.

The key is recognizing early whether your system has ASRs that demand deliberate design — or whether best practices and a modern stack will carry you. Most of the time, they will. But when they won't, you need to know before you're too deep to change course.

## The Takeaway

Next time someone says "the system should be reliable" or "we need good performance," don't nod along. Ask for a scenario. What load? What response time? Under what conditions? What's acceptable to sacrifice?

The answers will either clarify the architecture — or reveal that nobody has actually thought it through yet. Both outcomes are valuable.
