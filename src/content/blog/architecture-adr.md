---
title: 'ADR template'
description: 'Basics of ADR with markdown template'
pubDate: 'Jun 15 2024'
heroImage: '/blog-placeholder-4.jpg'
tags: ['architecture', 'adr', 'markdown']
---

## Origin

[pmerson/ADR-template](https://github.com/pmerson/ADR-template)

## Usage guidelines

- Each ADR is a plain text, 1-2 page document
- ADRs should be numbered
- ADRs should be stored within each software project repo
- Create a separate repo for crosscutting ADRs
- Track ADRs in the backlog
- Review ADRs
- Create ADRs for *significant* design decisions
- This template is a suggestion that you may want to adopt or adapt. In any case, establishing and sharing a template for ADRs in your team or organization is a good idea.

## ADR template

```text
# ADR N: brief decision title
Describe here the forces that influence the design decision,
including technological, cost-related, and project local.

## Decision
Describe here our response to these forces,
that is, the design decision that was made.
State the decision in full sentences, with active voice ("We will...").

## Rationale
Describe here the rationale for the design decision.
Also indicate the rationale for significant *rejected* alternatives.
This section may also indicate assumptions, constraints, requirements,
and results of evaluations and experiments.

## Status
[Proposed | Accepted | Deprecated | Superseded]
If deprecated, indicate why.
If superseded, include a link to the new ADR.

## Consequences
Describe here the resulting context, after applying the decision.
All consequences should be listed, not just the "positive" ones.
```
