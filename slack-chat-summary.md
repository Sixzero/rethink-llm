# LLMv2 Rethinking: Slack Chat Summary & Actionable Tasks

## Overview
This document summarizes a Slack conversation between Timothy, SixZero, and Jan Siml discussing the future of LLM tooling in Julia, comparing PromptingTools.jl with Pydantic-AI, and planning a potential LLMv2 architecture.

## Key Participants
- **Timothy**: Working on biomedical association mining, interested in agentic workflows
- **SixZero**: Developer of TodoForAI, advocates for modular systems
- **Jan Siml**: Heavy PydanticAI user, PromptingTools contributor

## Current State Analysis

### PromptingTools.jl Limitations Identified
1. **Monolithic architecture** - not modular enough
2. **Missing Gemini streaming support**
3. **Tightly coupled with OpenAI.jl**
4. **Outdated templating system** taking up too much code
5. **No clean message type for multimodal support**
6. **Missing realtime support** (WebSocket, WebRTC)
7. **Lack of message callbacks**
8. **No execution graph/agentic loops**
9. **Inferior tool validation and execution compared to PydanticAI**

### PydanticAI Strengths
1. **Modular package separation**
2. **Model fallback cascade**
3. **Enterprise support (Vertex)**
4. **Observability features**
5. **Tool output validation**
6. **Human approval workflows**
7. **Temporal durable execution**
8. **Message callbacks**

## Proposed LLMv2 Architecture

### Three-Layer Separation
1. **Inference Layer**: Dedicated to API endpoint interactions
2. **Utility Layer**: Julia structs ↔ JSON, streaming, tools, image support
3. **Framework Layer**: Agents, workflows, RAG, advanced patterns

### Core Components Needed
- **Model Database Package**: Separate from main inference (avoid REST for model info)
- **Streaming Solutions**: Multiple implementations with fallback support
- **Message Parts Structure**: Modern multimodal support
- **ChainRules-like System**: For model pricing and metadata
- **Reasoning/Completion Endpoints**: Lightweight runner support

## Actionable Tasks

### Immediate Planning Tasks
1. **Create GitHub repository** for collaborative planning (preferred over HackMD)
2. **Design document creation** - comprehensive architecture specification
3. **LLM-assisted analysis** of existing frameworks (PydanticAI, Outlines, DSPy, AdalFlow, vLLM)
4. **Interface definition** - clear abstract types and responsibility boundaries

### Research & Analysis Tasks
5. **Comparative study** of design patterns across Python LLM frameworks
6. **Deployment story analysis** - Julia vs Python for production AI systems
7. **Performance benchmarking** - streaming solutions comparison
8. **Enterprise requirements** gathering (observability, validation, etc.)

### Development Tasks
9. **Prototype inference layer** - clean API endpoint abstraction
10. **Message type redesign** - multimodal support with parts structure
11. **Streaming abstraction** - multiple backend support (Http.jl, libCurl)
12. **Model registry system** - separate package for model metadata
13. **Tool execution framework** - validation, error handling, retry logic
14. **Observability integration** - logging, tracing, debugging support

### Advanced Features
15. **Realtime support** - WebSocket and WebRTC implementations
16. **Agentic workflow engine** - execution graphs and iterators
17. **RAG framework** - abstract types and common patterns
18. **Validation system** - input/output type checking
19. **Human-in-the-loop** - approval workflows and intervention points
20. **Bare-metal inference** - common interface for API and local models

## Key Insights & Principles

### Development Philosophy
- **SOLID, DRY, KISS principles** improve AI model performance
- **Simple ideas are most effective** with LLMs
- **Folders as universal tools** - organizing context for AI systems
- **Code as the best graph description language**

### LLM Usage Patterns
- **Model switching strategy** - Claude → GPT-5 for idea refinement
- **Ping-ponging between models** for critical review
- **LLMs poor at architecture** but good at building blocks
- **Validation/verification** more important than generation

### Technical Decisions
- **Avoid graph abstractions** - programming languages are better
- **Modular over monolithic** - clear interface boundaries
- **Native AI integration** required for modern tools
- **Observability crucial** for production systems

## Next Steps

### Phase 1: Foundation
1. Set up collaborative GitHub repository
2. Create detailed design document
3. Define clear interface boundaries
4. Prototype core inference layer

### Phase 2: Core Development
1. Implement modular architecture
2. Build streaming abstraction layer
3. Create modern message types
4. Develop tool execution framework

### Phase 3: Advanced Features
1. Add agentic workflow support
2. Implement observability features
3. Build validation systems
4. Add enterprise features

## Constraints & Considerations
- **Backward compatibility** with PromptingTools.jl
- **Maintenance burden** of multiple packages
- **Clear responsibility boundaries** to avoid tangled dependencies
- **Production deployment** story for Julia ecosystem
- **Community adoption** and migration path

## Resources & References
- [HackMD Design Doc](https://hackmd.io/@tecosaur/SkVhwtY9le)
- TodoForAI - production Julia AI system example
- PydanticAI documentation and design patterns
- EasyContext for RAG functionality reference

---
*Generated from Slack conversation on LLMv2 architecture planning*