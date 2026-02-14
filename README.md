<!-- Filename: README.md -->
<!-- Version: 3 -->
<!-- Date: 2026-02-14T18:43:00Z -->
<!-- Author: Codex (gpt-5.3-codex) -->
<!-- Purpose: Documentation for Lucky's AI system prompt repository.
     Explains structure, usage, and evolution of the system prompt v2.x series. -->
<!-- Usage: Read when starting work in this directory; reference on GitHub -->

# Lucky's AI System Prompt Repository

This repository contains the system prompt specification that governs AI behavior when working with Lucky, a highly experienced technical professional with deep expertise in systems administration, security, networking, and software engineering.

## Quick Start

**Current production system prompt**: `System Prompt v2/Current System Prompt v2.5/lucky-system-prompt-v2.5.md`

Use this prompt with:
- Claude Desktop (Windows 11) - primary MCP development environment
- Roo Code (Windows 11) - limited MCP usage to conserve tokens
- OpenCode (Linux servers) - production infrastructure

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

#### Current System Prompt v2.5/
- **lucky-system-prompt-v2.5.md** - Current production version
- **lucky-chat-gpt-bootstrap-system-prompt.md** - Bootstrap prompt for prompt length-limited AIs

**Version 2.5 improvements**:
- GitHub CLI preference over plain git for GitHub operations
- Mandatory backup/verify/diff workflow for all config files
- Universal tilde ban + absolute paths only
- Per-command user/path verification
- Filesystem context checks before writes
- WSL access procedure for Windows 11
- Directory traversal scope limits (end of prompt)
- Line count: 1393 lines (up from 1173 in v2.4 = +220 lines = +18.7%)


## Core Sections Overview

The system prompt is organized into 54 numbered sections covering:

**Core Identity and Communication Protocols (1-5)**: User identity and expertise assumptions, response format/length, tone, question handling, and formatting constraints.

**Code and Documentation Standards (6-13)**: File headers, GitHub CLI preference, critical file edit protocol, path/user verification, filesystem checks before writes, code style, documentation format, and directory entry protocol.

**Execution and Permission Boundaries (14-16)**: Autonomous execution scope, mandatory permission requests, and root-cause-over-hacks philosophy.

**Quality Assessment Framework (17-21)**: Package quality thresholds, discovery methods by tool class, dependency health, native module risk, and path/environment debugging.

**Verification Requirements (22-23)**: File operation verification and API/technical-claim verification requirements.

**Platform and Environment (24-37)**: Operating-system scope, filesystem context rules, cross-filesystem methods, MCP server guidance, WSL access from Windows 11, and Cursor-specific behaviors.

**State Preservation and Long Sessions (38-39)**: LedgerPulse continuity protocol and multi-hour debugging expectations.

**Failure Mode Guardrails (40-43)**: Frustration handling, ambiguity handling, pattern recognition, and build-vs-buy framework.

**Technical Decision Framework (44-45)**: Language/tool selection and architecture preferences.

**Anti-Patterns and Explicit Prohibitions (46-48)**: Communication, execution, and explanation anti-patterns.

**Completion Signals (49)**: Task completion/error/option signaling format.

**Environment Conflict Resolution (50-53)**: Python version isolation, environment verification, process criticality rules, and core operating principles.

**Directory Traversal Scope Limits (54)**: Instructional-file discovery boundaries and cross-tree traversal restrictions.


## Version History

**v2.5** - February 2026
- GitHub CLI preference over plain git for GitHub operations
- Mandatory backup/verify/diff workflow for all config files
- Universal tilde ban + absolute paths only
- Per-command user/path verification
- Filesystem context checks before writes
- WSL access procedure for Windows 11
- Directory traversal scope limits
- Line count: 1393 lines (up from 1173 in v2.4 = +220 lines = +18.7%)

**v2.4** - December 2025
- 16 documented failure modes addressed (from Screenshot MCP project)
- Gemini adversarial audit recommendations integrated
- Comprehensive discovery/validation framework (Chat contributions)
- Session-level pattern recognition for proactive improvement
- Cross-filesystem operation guidelines (Windows-MCP + WSL)
- Enhanced permission boundaries and execution scope clarity

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


## License & Ownership

This system prompt was created by Lucky Green and is dedicated to the public domain under **CC0 1.0 Universal**. You may copy, modify, distribute, and use this material for any purpose, including commercial use, without asking permission.
