# A new generation of LLMs in Julia

## Motivation
- Learn from PT’s limitations (monolith, tight OpenAI.jl coupling, weak validation/observability, no realtime, no message-parts) and Python’s strengths (Pydantic-AI’s modularity, fallbacks, observability).
- Provide a modular, testable, and portable stack with clear interfaces and minimal maintenance friction.
- Unify API and local (bare‑metal) inference behind one interface.
- Make reliability-first features (observability, validation, constraints, human-in-the-loop) first-class.
- Keep PT alive by delegating to shared packages; offer a smooth migration path.

## Prior Art
- PromptingTools.jl: broad features, monolithic, older abstractions (templating, no message-parts), tied to OpenAI.jl, limited streaming/realtime.
- Pydantic-AI: modular separation, model fallbacks, observability, validation, toolsets, enterprise support (Vertex), but slower at new providers and tricky imports.
- Outlines / DSPy / AdalFlow: patterns for constraints, optimization, and workflow composition.
- vLLM and provider ecosystems: emphasize performance, provider variance, and API heterogeneity.

## Design considerations
- Clear 3-layer split: inference APIs; utilities (types/streaming/tools); frameworks (agents/workflows).
- Prefer packages over services for model metadata; ship a model registry/pricing as data.
- Message-parts and callbacks for multimodal + structured tool I/O.
- Pluggable streaming with multiple backends (Http.jl, libcurl) and a consistent event surface.
- First-class observability (events/spans, redaction) and validation; add human approvals where needed.
- Fallback cascades and graceful degradation across providers.
- Realtime support (WS first; WebRTC later).
- Back-compat adapters so PT can load the new packages without breaking users.
- Code over graph DSLs for control flow; only introduce graphs if necessary.
- “Folders as workbench”: repos hold context, scripts, diffs; LLMs operate over files.
- Keep interfaces small and stable; isolate provider quirks in adapters.
- Deployment pragmatism: acknowledge Julia vs Python trade-offs; plan for Vertex/GCP auth when needed.

## Architecture
- Layers
  - Inference layer (LLMCore): provider clients (API and local), request/response types, retries/timeouts, fallbacks; unified interface for completion, reasoning, tools, streaming.
  - Utilities layer (LLMTypes): message and part types (text/image/tool_call/tool_result), JSON round‑trip, tool spec + minimal executor, validation hooks, observability events, streaming facade.
  - Framework layer (LLMFramework): agents, workflows/iterators, RAG abstractions, human-in-the-loop, higher-level policies. Optional and independent.

- Supporting packages
  - LLMRegistry: static model metadata (names, modalities, pricing, limits).
  - LLMStreaming: pluggable backends (Http.jl, libcurl) with a uniform callback/event API.
  - (Names tentative; fewer packages if possible—optimize for clarity + maintenance.)

- Minimal interfaces (sketch)
  ```julia
  abstract type LLMProvider end
  @kwdef struct LLMModel; name; provider::LLMProvider; family; modality; price; limits; end

  @kwdef struct Msg; role; parts; end
  abstract type Part end
  @kwdef struct TextPart <: Part; text; end
  @kwdef struct ImagePart <: Part; url; mime; end
  @kwdef struct ToolCallPart <: Part; name; args; id; end
  @kwdef struct ToolResultPart <: Part; id; ok; result; end

  complete(model::LLMModel, messages; tools=nothing, config=NamedTuple()) = # response
  stream(model::LLMModel, messages; tools=nothing, config=NamedTuple(), on_event=nothing) = # yields events

  # Streaming backend facade
  stream_request(backend, req; on_event) = backend(req; on_event)
  ```

- Observability and validation
  - Emit structured events (request_started, token, tool_called, retry, fallback_used, request_finished).
  - Redaction hooks for sensitive data.
  - Validation hooks for inputs/outputs and tool results; optional human approval.

- Realtime scope
  - Phase 1: WebSocket streaming.
  - Phase 2: WebRTC data channels.

- Migration for PT
  - PT loads LLMTypes/LLMCore/LLMStreaming/LLMRegistry and re-exports existing APIs where feasible.
  - Shims for message conversion; opt-in to new features without breaking users.

- MVP scope
  - 2 providers (e.g., OpenAI-compatible + Gemini/Groq), 2 streaming backends (Http.jl + libcurl).
  - Message-parts + callbacks, basic tool execution, simple fallback cascade.
  - Minimal observability (events/spans) and output validation.

- Non-goals (initially)
  - Complex graph DSLs, Temporal integration, full enterprise matrix—defer until interfaces stabilize.

- Open questions
  - Exact boundary Utilities vs Framework (where do tools and validation live).
  - Observability spec (event taxonomy, span context propagation).
  - Realtime API details and payload formats.
  - Enterprise priorities (Vertex, auth models, SLAs) and when to tackle them.
  - PT migration milestones and end-state compatibility.