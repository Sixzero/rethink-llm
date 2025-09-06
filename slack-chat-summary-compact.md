# LLMv2 Rethink — Concise Summary & Nuanced Actionables

## TL;DR
- Split the stack into 3 layers: inference APIs, utilities (types/streaming/tools), and frameworks (agents/workflows).
- Prioritise observability, validation/constraints, and clear interfaces over features.
- Keep PT alive by delegating to shared packages; design for portability and minimal maintenance friction.

## What went wrong (LLMv1/PT)
- Monolith + tight coupling (OpenAI.jl); hard to evolve and test alternatives.
- No modern message-parts; limited multimodal and callbacks.
- Missing realtime (WS/WebRTC), flaky streaming (e.g., Gemini, Cerebras with StreamCallbacks.jl).
- Overweight templating; underweight validation and observability.
- No execution graphs/agentic loops; tool validation/sets/human approvals immature.
- Model registry/pricing embedded; not a separate, reusable module.

## Design bets for LLMv2
- Clear 3-layer split with abstract interfaces and stable responsibility boundaries.
- Separate model registry/pricing metadata (no live REST dependency).
- Pluggable streaming (Http.jl, libcurl) with consistent surface and fallback.
- Unified message-parts type for multimodal + message callbacks.
- First-class observability + validation; human-in-the-loop where needed.
- API and bare-metal inference behind the same interface.
- Back-compat adapter so PT can consume new packages without breaking users.

## Non-goals / stances
- Avoid graph DSLs for control flow; use code (graphs only if necessary).
- Prefer packages over services for model metadata (no remote lookup).
- Don’t chase every provider day-1; focus on interface stability.

## Nuances captured from chat
- “Folders as workbench” for LLM context + scripts + diffs (repo over docs).
- Deployment trade-offs: Python packaging pain vs Julia deploy story; be pragmatic.
- Streaming libs behave inconsistently across providers; need multiple backends.
- Constraints/validation/observability matter more than raw generation.
- LLMs are weak at architecture; strong at small composable blocks.
- Maintenance cost scales with package count unless interfaces are crisp.
- Enterprise asks: fallbacks, Vertex support, observability; Gemini support is painful.

## Immediate next actions
- Create a repo for design + experiments; keep a distilled plan (hack.md) in-repo.
- Draft interface sketches for the 3 layers (abstract types + minimal methods).
- Carve out a model-registry/pricing package.
- Prototype inference layer with 2 providers + 2 streaming backends (Http.jl, libcurl).
- Define message-parts type + callbacks; adapt a minimal tool-exec with validation.

## Open questions
- Exact boundary between utilities vs framework (where do tools live?).
- Minimum observability spec (events, spans, payload redaction).
- Migration plan for PT users (feature/time matrix, shims).
- Realtime scope (WS first? WebRTC later?).
- Enterprise priorities (fallback cascades, Vertex/GCP auth).