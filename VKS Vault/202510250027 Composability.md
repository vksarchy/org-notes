---
date: 2025-10-25T00:27
tags: []
source: 
cssclass:
---
What is composability (short, brutal definition)

Composability = designing pieces so they snap together predictably. Small, well-defined components + clear interfaces + minimal hidden side-effects = you can combine them in new ways with little friction.


Think Lego bricks, not a pile of melted plastic.

Core principles (the tactical checklist)

Encapsulation — each component owns its state and exposes only what’s needed.

Clear contracts — APIs (interfaces) must be explicit, versioned, and small.

Loose coupling — components depend on interfaces, not implementations.

High cohesion — each piece does one focused job well.

Compositional guarantees — idempotence, retries, error semantics.

Discoverability — catalogue components, inputs, outputs, and constraints.

Observability — telemetry tied to component boundaries.

Combinatorial safety — make unsafe compositions either impossible or explicit

### Up 

### Down
[[Real Life Scenarios]]