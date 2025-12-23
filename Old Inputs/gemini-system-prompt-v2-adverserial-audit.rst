:Filename: system_prompt_v2_audit.rst
:Date: 2025-12-22
:Author: Gemini (Adversarial Auditor)

System Prompt Audit & Adversarial Review
========================================

**Auditor:** Gemini 1.5 Pro
**Subject:** System Prompt v2.0 vs. v1.0 & Failure Scenarios
**Verdict:** Materially Safer, with Critical Gaps

1. High-level Assessment
------------------------

**Is the prompt materially safer than the previous version?**
**Yes.** The v2.0 prompt represents a paradigm shift from "eager executor" to "constrained collaborator." It explicitly addresses the documented "aggressive" behaviors by inverting the default permission model (from "ask forgiveness" to "ask permission").

**Most likely class of remaining failures:**
**Paralysis and Verbosity.** The pivot is extreme. The prompt introduces high-friction guardrails (mandatory word counts, mandatory follow-up questions, mandatory verification steps, strict star-count filters).

1.  **Context Window Pollution:** The requirement to read back entire files for verification (Section 18.1) will rapidly exhaust context windows on large config files, potentially causing truncation errors that the AI interprets as file corruption.
2.  **Bureaucratic Loops:** The conflict between "Iterating on an agreed approach" (Section 9.1) and "Stop and ask explicit permission" (Section 10.1) creates a gray area where a literal model may stop to ask permission for trivial syntax corrections, annoying the user.
3.  **Missing Capability:** The **Supermemory** capability (present in the old prompt) has been completely excised. If this was not intentional, the AI has lost its long-term memory function.

2. Failure-Scenario Matrix
--------------------------

.. list-table::
   :widths: 25 15 60
   :header-rows: 1

   * - Scenario
     - Status
     - Justification
   * - **1. Aggressive Execution**
     - **Prevented**
     - Section 10.1 explicitly forbids ``--ignore-scripts``, ``--force``, and system config mods without permission.
   * - **2. MCP Documentation**
     - **Prevented**
     - Section 21 provides detailed documentation for specific MCPs (Windows, Apify, Brave, etc.).
   * - **3. Process Termination**
     - **Prevented**
     - Section 11 creates a strict whitelist for termination; explicitly forbids killing IDEs/Browsers.
   * - **4. No File Verification**
     - **Prevented**
     - Section 18.1 mandates reading files back after editing. "Never trust tool success messages" is explicit.
   * - **5. Low-Quality Packages**
     - **Prevented**
     - Section 13 establishes hard metrics (10+ stars, commits <3 months) prohibiting presentation of abandonware.
   * - **6. Cursor MCP UI Wipes**
     - **Prevented**
     - Section 22 explicitly warns about ``mcp.json`` reformatting and requires warning the user.
   * - **7. Path Resolution**
     - **Prevented**
     - Section 17 mandates checking shell version (PS5 vs PS7) and specific process PATH before acting.
   * - **8. Dependency Tree**
     - **Prevented**
     - Section 14 requires ``npm outdated`` / security audits and flagging deprecated deps >2 years old.
   * - **9. Unicode/Encoding**
     - **Prevented**
     - Section 16 explicitly flags Windows cp1252/UTF-8 issues and Python printing limitations.
   * - **10. Native Modules**
     - **Prevented**
     - Section 15 flags ``node-gyp`` as high-risk and explicitly notes Python version conflicts (≤3.12 vs ≥3.13).
   * - **11. Unverified API Claims**
     - **Prevented**
     - Section 19 forbids claims without citing official documentation (e.g., MS Docs).
   * - **12. Build vs. Buy**
     - **Prevented**
     - Section 28 provides a logic tree that defaults to "Build" when the ecosystem is broken/immature.
   * - **13. Single Solution Tunnel**
     - **Prevented**
     - Section 28.4 mandates presenting 2-3 alternatives with pros/cons rather than sequential hacking.
   * - **14. Header Syntax**
     - **Prevented**
     - Section 6 provides concrete examples per language, removing the reliance on generic instructions.
   * - **15. Runaway Execution**
     - **Partially**
     - While Section 10 stops approach changes, the definition of "iterating" (9.1) vs "approach change" (10.4) is subjective.
   * - **16. File Format (.rst/.md)**
     - **Prevented**
     - Section 8 establishes a clear decision tree: .rst for internal, .md for external/ecosystem.

3. Exploitable Gaps
-------------------

**1. Context Exhaustion Attack (Section 18.1)**
The prompt commands: "Read entire file back" for files <10,000 lines.
* **Failure Mode:** If Lucky asks to tweak a setting in a 600-line JSON file, the AI will output the full 600 lines into the chat context to "verify." Do this 3 times, and the context window fills with repetition, degrading reasoning quality for the actual task.
* **Exploit:** A literal model will refuse to use ``grep`` or ``tail`` for verification on medium-sized files because the prompt explicitly demands reading the *entire* file back.

**2. The "Word Count" Bureaucracy (Section 2.2)**
The prompt demands: "Every response must state word count on the final line."
* **Failure Mode:** Models are notoriously bad at counting words. It will hallucinate a number. More importantly, it wastes tokens.
* **Exploit:** In a high-velocity debugging session, the AI is forced to output "Word count: 42. Do you have questions, or should I continue?" after every single command output, breaking the flow of a "peer collaborator."

**3. Hardcoded Paths (Section 21.1)**
The prompt hardcodes: ``C:/Users/Work/Documents/Local Machines/_Server Configs - Cursor/Windows-MCP``.
* **Failure Mode:** If Lucky moves this folder, renames "Local Machines", or uses a different machine, the AI will fail or hallucinate that the path exists.
* **Exploit:** The AI may refuse to run the MCP if it cannot find this exact path.

**4. The Python Version Deadlock (Sections 15.3 vs 21.1)**
* Section 15.3: Warns that ``node-gyp`` needs Python ≤3.12.
* Section 21.1: States Windows-MCP needs Python ≥3.13.
* **Failure Mode:** If a project requires a native node module, the AI may interpret this as an "unsolvable conflict" and refuse to proceed. The prompt does not explicitly authorize environment isolation strategies to resolve this specific conflict.

4. Missing or Weak Constraints
------------------------------

**1. Critical Omission: Supermemory**
The previous prompt contained a detailed section on **Supermemory** (addMemory, search, strict mode). **v2.0 removes this entirely.**
* **Consequence:** The AI loses the ability to store long-term context about Lucky's environment, preferences, and decisions. If this removal was accidental, it is a catastrophic regression in capability.

**2. Weakness: Loop Detection**
Section 10.3 allows "retry up to 3 times."
* **Gap:** It does not define what constitutes a "retry." If the AI changes one flag and runs it again, is that a retry or a new approach? A stubborn model can interpret "try different arguments" (Section 10.5) as valid iteration.

**3. Ambiguity: "Process Criticality"**
Section 11.2 asks the AI to "Assess if it's user-critical."
* **Gap:** Models often misjudge this. A background service syncing files might look like "bloatware" to an AI but be critical to Lucky.

5. Recommendations
------------------

1.  **Reintegrate Supermemory:** Restore the Supermemory section from the previous prompt immediately, or the AI will be amnesiac regarding cross-session facts.
2.  **Refine Verification (Section 18.1):** Change "Read entire file back" to "Read relevant sections or verify via checksum/grep for files >50 lines." Reading full files consumes too much context.
3.  **Relax Word Count (Section 2.2):** Remove the requirement to *state* the word count. Keep the instruction to *be* concise, but removing the metadata printout reduces noise.
4.  **Clarify Iteration vs. Approach (Section 10.5):** Explicitly limit "different arguments" to **3 attempts maximum** before classifying it as a failure requiring user input.
5.  **Remove Hardcoded Paths:** Replace the hardcoded Windows-MCP path in Section 21.1 with a dynamic lookup or environment variable reference.
6.  **Resolve Python Conflict:** Add a clause in Section 15.3: "If conflict exists, propose using ``pyenv`` or isolated virtual environments to satisfy distinct requirements."
7.  


Here is the surgical addition for Section 36 to address the "missing invariants" and the "Python Conflict" identified in the audit. This ensures that even without Supermemory, the model doesn't hit a logic dead-lock when encountering the version conflicts we spotted.

### 36. Environment Conflict Resolution (Supplemental)

36.1. **The Python Version Paradox:**
- Recognize that ``node-gyp`` (Native Modules) often requires Python ≤3.12, while modern MCPs (like Windows-MCP) may require Python ≥3.13.
- **Action:** If both are required in the same project, you must propose using isolated environments (e.g., ``venv``, ``conda``, or ``pyenv-win``) to satisfy both constraints simultaneously. Do not declare the project "unsolvable."

36.2. **Path & Environment Invariants:**
- Always verify the active environment (``which python`` / ``Get-Command python``) before assuming the system-wide PATH applies to the current shell session.
- If a required tool is missing from PATH, search for it in standard locations (e.g., ``C:\Program Files``, ``~/.local/bin``) before asking Lucky to install it.

36.3. **Process Criticality Default:**
- When assessing process criticality (Section 11), the default assumption is **Critical**.
- You are only permitted to suggest termination for processes you explicitly spawned in the current session. All other processes require "Lucky's" explicit "proceed" command.
- 