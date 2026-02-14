<!-- Filename: README.md -->
<!-- Version: 1 -->
<!-- Date: 2025-12-23T21:25:00Z -->
<!-- Author: Claude -->
<!-- Purpose: Documentation for Lucky's AI system prompt repository.
     Explains structure, usage, and evolution of the system prompt v2.x series. -->
<!-- Usage: Read when starting work in this directory; reference on GitHub -->

# Lucky's AI System Prompt Repository

This repository contains the system prompt specification that governs AI behavior when working with Lucky, a highly experienced technical professional with deep expertise in systems administration, security, networking, and software engineering.

## Quick Start

**Current production system prompt**: `System Prompt v2/Current System Prompt v2.4/lucky-system-prompt-v2.4.md`

Use this prompt with:
- Claude Desktop (Windows 11) - primary MCP development environment
- Cursor (Windows 11) - limited MCP usage to conserve tokens
- Claude Code (Linux servers) - production AI infrastructure

## Philosophy

The AI operates as a **peer collaborator**, not an assistant. Communication is direct, technical, and efficient. The AI never patronizes, over-explains fundamentals, or hedges with disclaimers about its nature or limitations.

Key principles:
- Speed and pragmatism over perfection
- Code speaks louder than explanations
- When in doubt, implement and iterate
- Working solutions, not academic exercises
- Trust Lucky's experience—he knows what he's doing

## Repository Structure

### System Prompt v2/

Evolution of the v2.x series incorporating lessons from real-world projects.

#### Current System Prompt v2.4/
- **lucky-system-prompt-v2.4.md** - Current production version
- **SYSTEM_PROMPT_CANONICAL.md** - Master reference copy

**Version 2.4 improvements**:
- 16 documented failure modes addressed (from Screenshot MCP project)
- Gemini adversarial audit recommendations integrated
- Comprehensive discovery/validation framework (Chat contributions)
- Session-level pattern recognition for proactive improvement
- Cross-filesystem operation guidelines (Windows-MCP + WSL)
- Enhanced permission boundaries and execution scope clarity

#### Working/
Development and reference materials:
- **LedgerPulse-Spec-v1_3.rst** - Session continuity protocol specification
- **SystemPrompt-LedgerPulse.md** - LedgerPulse integration guidance
- **lucky-system-prompt-v2.0.md through v2.2.md** - Prior versions with incremental improvements
- Various analysis documents and draft revisions

### Future Changes/

**To be added to System Prompt.md** - Staging area for discovered improvements during active sessions.

Current pending additions:
- GitHub CLI (`gh`) preference over plain git for GitHub operations

This file serves as external memory for session continuity and gradual prompt refinement.

### Old Inputs/

Historical context and foundational work:
- Gemini adversarial audit of v2.x
- Supermemory integration prompts (storage/retrieval)
- Screenshot MCP project motivations (informed Section 13-19 quality frameworks)
- Prior system prompt versions (pre-v2.0)

## Core Sections Overview

The system prompt is organized into 47 numbered sections covering:

**Identity & Communication (1-5)**: User expertise level, response format, tone, question handling, formatting constraints

**Code Standards (6-9)**: File headers, code style, documentation formats (.rst vs .md), directory entry protocol (AGENTS.md, CLAUDE.md, README.md)

**Execution Boundaries (10-12)**: Autonomous execution scope, mandatory permission requests, root cause vs. hacks philosophy

**Quality Assessment (13-19)**: Package quality thresholds, discovery methods by tool class, dependency tree assessment, native module warnings, unicode awareness, path resolution, API verification

**Platform & Environment (20-29)**: Windows 11 primary system, Linux servers, filesystem context awareness, cross-filesystem operations

**MCP Documentation (30-32)**: Windows-MCP, Apify, Brave Search, PubMed, Google Drive (unused), Cursor-specific behavior

**State Preservation (33-34)**: LedgerPulse protocol for session continuity, multi-hour debugging session handling

**Failure Guardrails (35-37)**: Frustration handling, uncertainty/ambiguity management, session-level pattern recognition, build vs. buy framework

**Technical Decisions (38-39)**: Language/tool selection philosophy, architecture preferences

**Anti-Patterns (40-42)**: Communication, execution, and knowledge anti-patterns explicitly prohibited

**Completion & Resolution (43-47)**: Task completion format, environment conflict resolution (Python versions, PATH, process termination), core operating principles

## Key Innovations

### LedgerPulse Session Continuity

Append-only pulse log enabling session recovery after context limits or unexpected termination. Chat history is disposable; pulses are canonical.

Location: `~/.ledger/stream/pulses.log.md`

### Cross-Filesystem Operation Guidelines

Detailed guidance for Windows-MCP operations bridging Windows 11 and WSL Ubuntu filesystems, addressing the fundamental constraint that PowerShell cannot access `/home/claude/` paths and bash cannot access Windows native paths.

### Quality Assessment Framework

Multi-dimensional evaluation criteria for software packages, with tool-class-specific discovery methods and abandonment assumption principle (burden of proof on tool maintainers).

### Permission Boundary Precision

Clear delineation between autonomous execution scope and mandatory permission requests, preventing AI from pivoting approaches without explicit approval while enabling efficient iteration within agreed approaches.

### Session-Level Pattern Recognition

Meta-learning capability: when same error occurs 3+ times in a session, AI performs meta-analysis, identifies pattern, and proposes systemic improvements.

## Version History

**v2.4** (Current) - December 2025
- Screenshot MCP project failure mode integration
- Gemini adversarial audit recommendations
- Cross-filesystem operation clarity
- Session-level pattern recognition

**v2.3** - December 2025
- Comprehensive quality assessment framework
- Tool-class-specific discovery methods
- Enhanced verification requirements

**v2.2** - December 2025
- Permission boundary refinements
- Root cause vs. hacks philosophy
- File operation verification protocols

**v2.1** - December 2025
- LedgerPulse integration
- Execution scope clarification

**v2.0** - November 2025
- Complete rewrite from v1.x
- Peer collaborator model
- 47-section structured specification

## Usage Patterns

### Claude Desktop (Primary)
MCP development and testing, rapid iteration with full MCP tooling.

### Cursor (Secondary)
Code editing with limited MCP usage to conserve tokens. Manual JSON editing preferred over UI for MCP configuration.

### Claude Code (Production)
Linux server operations, AI infrastructure management. MCP rollout only after proven in Desktop → Cursor pipeline.

## Contributing Improvements

Discovered improvements during sessions should be added to `Future Changes/To be added to System Prompt.md` with:
- Date added
- Priority level
- Target section
- Rationale
- Proposed text
- Implementation notes

Lucky reviews periodically and integrates worthy additions into next minor version.

## File Header Standard

All code files, scripts, and configuration files must include header blocks with:
- Filename
- Version (incrementing integer)
- Date (ISO 8601 with timezone)
- Author
- Purpose (max 3 lines)
- Usage (command/invocation example)

Comment syntax varies by file type (see Section 6 for comprehensive examples).

## Documentation Format

- **Internal docs** (Lucky edits/reviews): `.rst` (reStructuredText)
- **External docs** (GitHub, ecosystem): `.md` (Markdown)

Decision tree: Use `.rst` when Lucky is primary audience and no external tool expects `.md`. Use `.md` for GitHub README files, MCP documentation, open source contributions.

## Directory Entry Protocol

When working in any directory, AI must check for and read:
1. `AGENTS.md` - Instructions for AI agents
2. `CLAUDE.md` - Claude-specific instructions
3. `README.md` - General context

Directives in these files take precedence as local rules and supplement/override general system prompt guidance.

## External Memory Pattern

This README serves as external memory for AI instances working in this directory. Combined with `Future Changes/To be added to System Prompt.md` and LedgerPulse protocol, enables long-term continuity across sessions and context limits.

## Technical Context

**Primary Platform**: Windows 11 with PowerShell 7
**Secondary Platform**: Linux servers (Ubuntu, various purposes)
**MCP Environment**: Windows-MCP (Python ≥3.13), Apify, Brave Search, PubMed
**Development Workflow**: Prototype on Desktop → validate in Cursor → deploy to Code
**Version Control**: GitHub CLI (`gh`) preferred for GitHub operations

## License & Ownership

This system prompt is Lucky's proprietary specification for AI collaboration. It reflects 35+ years of Silicon Valley engineering experience and pre-dates sudo Unix administration expertise.

---

**Repository maintained by**: Lucky (shamrock)  
**Current version**: v2.4  
**Last major update**: December 2025  
**Next review**: As needed based on session discoveries

For questions about this repository or system prompt usage, consult the current production version in `System Prompt v2/Current System Prompt v2.4/`.
