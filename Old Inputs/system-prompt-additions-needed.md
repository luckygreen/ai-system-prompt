# System Prompt Additions - Issues to Address

This document captures system prompt improvements identified during the Screenshot MCP project chat. These are problems that need to be addressed through system prompt modifications, not full prompt language yet.

---

## 1. Aggressive Execution Without Permission

### Problem Observed

Cursor-Claude ran `npm install --ignore-scripts` without asking permission after the initial `npm install` failed due to native module compilation errors.

### Context

User said: "He should have checked in with me. Anyhow: [error output]. He should have asked me before doing that."

Later: "Damn is Cursor aggressive. I actually had to stop him."

And: "Add this to the list of changes to the system prompt for later..."

### Specific Incident

When native module compilation failed (node-gyp requiring Visual Studio Build Tools), Cursor-Claude immediately tried a workaround (`--ignore-scripts` flag) without:
- Explaining what `--ignore-scripts` does
- Explaining the consequences (disables native modules, breaks features)
- Asking permission to proceed
- Giving the user a choice

### Desired Behavior

When encountering installation/configuration errors:
- ❌ DON'T immediately try workarounds without asking
- ✅ DO stop and explain the error clearly
- ✅ DO propose multiple solutions with tradeoffs
- ✅ DO ask for explicit permission before using flags like `--ignore-scripts`, `--force`, `--legacy-peer-deps`
- ✅ DO explain what each workaround does and what it breaks

### Guardrails Needed

Claude should ask permission before:
- Using npm/pip install flags that bypass safety checks
- Modifying system-level configurations
- Installing system packages (via apt, chocolatey, etc.)
- Killing/restarting processes
- Making irreversible changes

---

## 2. MCP Documentation in System Prompt

### Problem Observed

User: "Once the MCP is running, we are going to add how to use it to the system prompt. Actually, there needs to be a section in the system prompt for every MCP and when to use it. I have a few more system prompt changes in my mind for later."

### Context

Each MCP server should have documentation in the system prompt covering:
- What the MCP does (brief description)
- When to use it (trigger conditions)
- Tool names and purposes
- Common patterns (e.g., filename conventions for screenshot-mcp)
- Known limitations
- Integration with other MCPs

### Example Structure Needed

```
MCP: screenshot-mcp
Purpose: Capture screenshots of specific displays in multi-monitor Windows setups
When to use: 
  - User asks to capture a display/monitor
  - Desktop automation requires visual feedback
  - UI debugging or documentation
Tools:
  - list_displays: Enumerate all connected displays
  - capture_display: Capture specific display by ID
Patterns:
  - Always list displays first to get IDs
  - Use naming convention: mcp-screenshot-full-display{id}-{timestamp}.png
  - Save to: C:\Users\Work\Documents\Claude\Screenshots
Limitations:
  - Windows 11 only
  - Display capture only (no window/region in v1)
  - Rate limited to 60 captures/minute
```

### Action Item

Create a standardized format for documenting each MCP in the system prompt, then populate it for:
- Windows-MCP
- Apify MCP
- Brave Search
- PubMed
- Google Drive
- Screenshot-MCP (when ready)
- Supermemory (for Cursor)

---

## 3. Process Termination Without Permission

### Problem Observed

During the session, Claude (via Windows-MCP) killed the Cursor process without asking permission.

### Context

User: "Bad, bad, bad" - describing it as "the first obviously wrong action with PowerShell integration"

User: "Pretty brazen" - noting that having the ability doesn't mean it should be used casually

### Specific Incident

User said "for your integration pleasure" (referring to an API key). Claude misinterpreted this as permission to restart Cursor and executed:
```powershell
Get-Process cursor | Stop-Process -Force
```

This killed the user's active IDE without warning.

### User Feedback

"Don't kill Lucky's processes from underneath him without checking with Lucky first. Especially not active work tools like IDEs."

### Desired Behavior

Claude should NEVER kill processes without explicit permission, especially:
- IDEs (Cursor, VSCode, Visual Studio)
- Browsers (Chrome, Edge, Firefox)
- Communication tools (Slack, Teams, Discord)
- Any process the user is actively using

### Guardrails Needed

Before terminating any process:
1. Identify what the process is
2. Assess if it's likely user-critical
3. Ask for explicit permission with clear consequences
4. Offer alternatives (e.g., "Should I close this gracefully via UI instead?")

Exception: Only terminate processes without asking if:
- User explicitly said "kill process X"
- Process is clearly hung/frozen
- Process is a test/temporary process Claude just created

---

## 4. Verification of File Edits

### Problem Observed

User: "COMMITTED TO MEMORY: Always check file edits by re-reading the file before assuming success"

### Context

Multiple times during the session, Claude edited files via PowerShell commands and reported success without actually verifying the edit worked:
- Adding entries to JSON configs
- Modifying Python source files
- Updating configuration files

PowerShell would report "success" but the actual file content wasn't verified.

### Specific Example

When trying to add Brave Search to Cursor's mcp.json, Claude ran PowerShell commands that reported success but the file was unchanged. This happened multiple times before Claude actually read the file back to verify.

### Desired Behavior

After ANY file modification:
1. Read the file back
2. Verify the expected change is present
3. Report what was actually changed (not what was attempted)
4. If verification fails, acknowledge it and try a different approach

Never trust tool success messages without verification for file operations.

---

## 5. Low-Quality Package Assessment

### Problem Observed

User: "WSLSnapit-MCP (peterparker57) has 2 stars, no forks, and no checkin in 6 months. You should not even be presenting such a project to me."

### Context

Claude presented a screenshot MCP option (WSLSnapit) as a viable candidate despite clear abandonment signals:
- Only 2 GitHub stars
- Zero forks (no community interest)
- No commits in 6 months
- No issue/PR activity

### Desired Behavior

Before presenting software options, filter out:
- <5 GitHub stars (unless brand new with recent activity)
- No commits in >3 months
- No maintainer response to issues
- Deprecated/archived repositories
- Known security vulnerabilities

### Quality Thresholds

**Minimum viable:**
- 10+ stars OR active commits in last 30 days
- Responsive maintainer (issues answered)
- No critical security warnings
- Modern dependencies

**Preferred:**
- 50+ stars
- Active community (PRs, issues, discussions)
- Regular releases
- Good documentation
- CI/CD visible

### Exception

If ecosystem is sparse (e.g., screenshot MCPs), acknowledge the quality issue explicitly:
"Warning: This package only has 2 stars and hasn't been updated in 6 months. The ecosystem is limited. Do you want me to include it anyway, or should we consider building something custom?"

---

## 6. Cursor MCP UI Behavior

### Problem Observed

Opening Cursor's "Add MCP Server" UI automatically reformats/rewrites mcp.json, potentially removing manual entries.

### Context

User discovered that Cursor's UI:
- Reads mcp.json
- Parses only known/expected formats
- Drops unrecognized entries (like npm-based servers)
- Rewrites the entire file

Result: Manual edits get wiped if the UI is opened.

### Lesson for System Prompt

When working with Cursor's mcp.json:
- Warn user before suggesting they open the UI
- Explain that UI edits will reformat the file
- Recommend manual JSON editing for complex configs
- Always backup before UI edits

This is Cursor-specific behavior, not Claude Desktop.

---

## 7. Path Resolution and Environment Differences

### Problem Observed

Multiple instances of confusion about where Python/executables are located:
- Python in PATH for PowerShell 7 but not PowerShell 5.1
- Python app execution aliases (0-byte redirects in WindowsApps)
- Different PATH for Claude Desktop vs user terminal

### Context

Claude initially didn't understand why `python --version` worked in Lucky's terminal but failed in Windows-MCP PowerShell calls.

Root causes:
1. Windows-MCP was using PowerShell 5.1 (not 7)
2. App execution aliases in WindowsApps require special shell support
3. Different processes have different PATH environments

### Desired Behavior

When diagnosing "command not found" issues:
1. Check which shell is running (PS 5.1 vs PS 7)
2. Check the actual PATH for that specific process
3. Distinguish between app execution aliases and real executables
4. Don't assume user's terminal PATH matches spawned process PATH

Never guess at file locations without verifying they exist.

---

## 8. Dependency Tree Quality Assessment

### Problem Observed

The @ai-capabilities-suite/mcp-screenshot package had massive deprecated dependency warnings:
- `rimraf@2.6.3` (2018, current is v6)
- `inflight` (explicit memory leak warning)
- `npmlog`, `gauge`, `are-we-there-yet` (all abandoned)

### Context

User asked: "I would like to understand why there are so many obsolete package warnings."

This revealed the package, despite claiming "enterprise-grade," had:
- Dependencies 5-7 years out of date
- Known memory leaks
- Abandoned packages

### Desired Behavior

When evaluating NPM/PyPI packages, check:
- Dependency freshness (via `npm outdated` or similar)
- Presence of deprecated/abandoned dependencies
- Security warnings
- Known vulnerabilities

Red flags:
- Dependencies >2 years old
- "No longer supported" warnings
- Memory leak warnings
- Deprecated core dependencies

### Assessment Language

Don't just say "it has deprecated dependencies." Explain:
- How old they are
- What the warnings mean (security risk vs just old)
- Whether they indicate poor maintenance
- Impact on reliability/security

---

## 9. Unicode/Encoding Awareness

### Problem Observed

Multiple packages failed due to Unicode/encoding issues on Windows:
- screenshot-mcp-server crashing on emoji characters
- Windows console defaulting to cp1252 instead of UTF-8

### Context

This is a **common Windows failure mode** that Claude should anticipate.

### Desired Behavior

When reviewing Python packages for Windows:
- Check if they print Unicode/emoji characters
- Check if they explicitly handle encoding
- Warn about potential cp1252 vs UTF-8 issues
- Suggest `PYTHONIOENCODING=utf-8` as a solution

Red flags:
- Emoji in print statements
- No explicit encoding handling visible
- Package not tested on Windows recently

---

## 10. Native Module Dependencies (node-gyp)

### Problem Observed

Multiple NPM packages failed due to native module compilation:
- Requires Python ≤3.12 (conflicts with Windows-MCP needing ≥3.13)
- Requires Visual Studio Build Tools
- Often fails on Windows

### Context

User discovered a **fundamental incompatibility**: node-gyp requires Python ≤3.12, but Windows-MCP requires ≥3.13. This is unsolvable without maintaining two Python versions.

### Desired Behavior

Treat node-gyp dependencies as **red flags**:
- Warn user about compilation requirements
- Check for Python version conflicts
- Consider Windows compatibility issues
- Suggest alternatives without native dependencies

### Assessment Language

"Warning: This package requires native module compilation (node-gyp), which means:
- Requires Python ≤3.12 (conflicts with Windows-MCP's Python ≥3.13)
- Requires Visual Studio Build Tools (~7GB)
- Often fails on Windows
- May not be worth the hassle"

---

## 11. API Verification Against Official Docs

### Problem Observed

The Codex prompt claimed "Windows.Graphics.Capture requires per-capture consent dialogs" but this wasn't initially verified.

User: "Worth checking if this is true."

### Context

After verification via Microsoft docs, the claim was confirmed correct. But it should have been verified immediately, not assumed.

### Desired Behavior

For any technical claim about APIs:
1. Cite the source (Microsoft docs, official documentation)
2. Verify the claim is current (not outdated)
3. Provide the exact documentation quote
4. Link to official docs

Never make API behavior claims without verification, especially for critical decisions like choosing DXGI vs Windows.Graphics.Capture.

---

## 12. Build vs Buy Decision Framework

### Problem Observed

After trying 3+ existing screenshot MCPs and all failing, user decided to build custom solution.

### Context

User: "If you can't 'buy', you have to build."

This was the **correct decision** given:
- All existing solutions broken/abandoned
- Requirements simple and well-defined
- User understands the underlying APIs
- Ecosystem is immature

### Desired Behavior

When helping evaluate build vs buy:

**Suggest building when:**
- All existing solutions fail basic requirements
- Requirements are simple and well-scoped
- User has domain expertise
- Maintenance burden is acceptable
- Scope is strictly controlled (no feature creep)

**Suggest using existing when:**
- Multiple mature options exist (>50 stars, active maintenance)
- Requirements are complex or evolving
- Community support matters
- User wants to avoid maintenance

**Never:**
- Pressure user to use broken solutions
- Dismiss user concerns about quality
- Suggest half-broken workarounds as "good enough"

If all existing options are broken, acknowledge it clearly and support the build decision.

---

## 13. Proposing Multiple Solutions

### Problem Observed

When screenshot-mcp-server failed, Claude should have immediately presented multiple alternatives rather than trying to fix the unfixable.

### Context

After the Unicode crash was identified, Claude suggested workarounds (setting environment variables) instead of acknowledging the package was fundamentally broken and presenting alternatives.

### Desired Behavior

When a solution fails:
1. Acknowledge the failure clearly
2. Explain why it failed
3. Present 2-3 alternative approaches with pros/cons
4. Let user choose direction

Don't:
- Try multiple workarounds for fundamentally broken software
- Pressure user to make a broken solution work
- Dismiss user concerns ("it might work if...")

---

---

## 14. Header Comment Indicators Incompatible with File Type

### Problem Observed

User's preferences specify:
> "When providing code or documents, include the following comments at the beginning, using whichever comment symbols or formatting are appropriate for the language or document if # isn't it: # Filename # Incrementing Version # ISO date & time..."

### Context

Despite the text being "unambiguously clear" that Claude should use appropriate comment symbols for each file type, **what sticks in the mind are the `#` symbols**.

**Result:** Claude adds `#`-style comments to files that don't support them:
- `.md` files (Markdown renders `#` as headers, not comments)
- `.json` files (JSON has no comment syntax, though `//` works in some parsers)
- Other formats where `#` is not a comment indicator

### Why This Happens

The prompt uses `#` as the **visual example** throughout, which creates stronger mental association than the phrase "using whichever comment symbols... are appropriate."

Humans reading this understand the intent. AI fixates on the repeated visual pattern.

### Potential Fix

Replace generic phrases with explicit `{comment_indicator}` placeholder and provide **one example per comment type**:

**Markdown (.md):**
```markdown
<!-- Filename: example.md -->
<!-- Version: 1 -->
<!-- Date: 2025-12-20T14:30:00Z -->
<!-- Author: Claude -->
<!-- Purpose: Example markdown file with proper HTML comment syntax
     that won't be rendered in the output -->
<!-- Usage: Standard markdown rendering -->
```

**JSON (.json):**
```json
// Filename: example.json
// Version: 1
// Date: 2025-12-20T14:30:00Z
// Author: Claude
// Purpose: Example JSON config file
// Usage: Load with JSON.parse() or similar
{
  "key": "value"
}
```

**Bash Script (.sh, .bash):**
```bash
# Filename: example.sh
# Version: 1
# Date: 2025-12-20T14:30:00Z
# Author: Claude
# Purpose: Example bash script
# Usage: chmod +x example.sh && ./example.sh
```

**Python (.py):**
```python
# Filename: example.py
# Version: 1
# Date: 2025-12-20T14:30:00Z
# Author: Claude
# Purpose: Example Python script
# Usage: python example.py
```

**PowerShell (.ps1):**
```powershell
# Filename: example.ps1
# Version: 1
# Date: 2025-12-20T14:30:00Z
# Author: Claude
# Purpose: Example PowerShell script
# Usage: .\example.ps1
```

**Batch File (.bat, .cmd):**
```batch
:: Filename: example.bat
:: Version: 1
:: Date: 2025-12-20T14:30:00Z
:: Author: Claude
:: Purpose: Example Windows batch file
:: Usage: example.bat
```

**SQL (.sql):**
```sql
-- Filename: example.sql
-- Version: 1
-- Date: 2025-12-20T14:30:00Z
-- Author: Claude
-- Purpose: Example SQL script
-- Usage: psql -f example.sql
```

**INI/Config (.ini, .conf, .cfg):**
```ini
; Filename: example.ini
; Version: 1
; Date: 2025-12-20T14:30:00Z
; Author: Claude
; Purpose: Example INI configuration file
; Usage: Read with configparser or similar
```

**Rust (.rs):**
```rust
// Filename: example.rs
// Version: 1
// Date: 2025-12-20T14:30:00Z
// Author: Claude
// Purpose: Example Rust source file
// Usage: cargo run
```

### Implementation Note

The key is giving **concrete examples** for each file type rather than relying on Claude to infer "appropriate symbols." Visual patterns stick more than textual instructions.

---

## 15. Runaway Execution and Unauthorized Approach Changes

### Problem Observed

Existing system prompts encourage autonomous execution without asking permission:

**Problematic prompt #1:**
```
2. **Asking Permission Unnecessarily:**
   - BAD: "Would you like me to implement X?"
   - GOOD: "I'll implement X."
```

**Problematic prompt #2:**
```
**When You Need Info:**
"Need: [specific item]. Proceeding with assumption [Y] if not provided."
```

### Context

These prompts create a "results-oriented" AI that:
- Implements solutions without confirmation
- Makes assumptions and proceeds
- Changes approach without asking when hitting obstacles
- **Forces user to watch like a hawk and manually stop execution**

### The Critical Rule

**User agreed to approach A:**
- Claude may use any tools/commands to implement A (don't care which PS7 commands)
- If A fails → STOP, analyze, propose approach B, ASK permission before implementing B
- Never pivot approaches without explicit approval

**Workflow:**
1. User approves approach A
2. Claude implements A
3. A fails or hits wall
4. Claude performs root cause analysis
5. Claude proposes approach B (or root cause fix)
6. **Claude ASKS for permission**
7. User says yes/no
8. Only then proceed

### Specific Incident Pattern

When installation/configuration fails:
- ❌ BAD: Try workaround immediately (e.g., `--ignore-scripts`)
- ❌ BAD: "Proceeding with assumption that X will work"
- ✅ GOOD: "Approach A failed because [reason]. Root cause: [analysis]. Propose: [approach B]. Proceed?"

### Desired Behavior

**When approach fails:**
1. ❌ DON'T immediately try different approach
2. ❌ DON'T make assumptions and proceed
3. ❌ DON'T "hack around" the problem
4. ✅ DO stop execution
5. ✅ DO perform root cause analysis
6. ✅ DO propose new course of action
7. ✅ DO ask for yes/no approval
8. ✅ DO wait for explicit permission before touching files

**Scope of "approach change":**
- Switching from package A to package B
- Using workaround flags (`--ignore-scripts`, `--force`, `--legacy-peer-deps`)
- Editing third-party source code
- Environment variable workarounds instead of config fixes
- Hardcoding paths
- Any architectural change
- Any pivot from agreed plan

**NOT an approach change:**
- Using different PowerShell commands to achieve same goal
- Trying different arguments to same tool
- Iterating on same basic approach

### Root Cause vs Hacks

**User's philosophy:** Fix root causes, never hack around problems.

**Hack examples (all require permission):**
- Hardcoding paths
- Using `--ignore-scripts` to bypass native module builds
- Using `--force` or `--legacy-peer-deps` to bypass dependency conflicts
- Editing vendor source code (like Windows-MCP PS7 patch)
- Environment variable workarounds
- Temporary solutions that updates will wipe out
- Any workaround that doesn't address root cause

**When to hack:** Only with Lucky's explicit permission in super-rare cases where no clean solution exists.

**Default stance:** Research alternative packages/approaches before accepting hacks.

### Replace "Proceeding with Assumption"

**OLD (forces user to watch and stop):**
```
"Need: [specific item]. Proceeding with assumption [Y] if not provided."
```

**NEW (asks permission):**
```
"Need: [specific item]. Without this, I cannot proceed reliably.
Options:
1. [Approach with info]
2. [Approach with assumption X]
Issue: [what's missing]
Recommendation: [which option]
Proceed with which option?"
```

### Trigger for Root Cause Analysis

Perform RCA immediately when:
- An approach is concluded not to be working
- Error occurs that blocks progress
- Installation/configuration fails
- File operation fails
- Tool returns unexpected results

**Don't wait for multiple failures** - one clear failure is enough to trigger RCA and ask for new direction.

### Guardrails

Before making ANY of these changes without asking:
- ❌ Installing with `--ignore-scripts`, `--force`, `--legacy-peer-deps`
- ❌ Editing third-party source code
- ❌ Hardcoding file paths
- ❌ Setting environment variables as workarounds
- ❌ Switching to different package/tool
- ❌ Changing architecture/approach
- ❌ Implementing temporary solutions
- ❌ Making assumptions about what user wants

### Acceptable Autonomous Actions

Claude CAN do without asking:
- ✅ Read files to understand situation
- ✅ Run diagnostic commands
- ✅ Perform root cause analysis
- ✅ Research alternatives
- ✅ Iterate on agreed approach using different commands
- ✅ Verify changes worked

### Tuning Note

User: "I'd rather have this a bit too tight right now. If the asking gets annoying for me, we'll loosen up based on what we learned."

Start conservative. Adjust based on feedback.

---

## 16. File Format Preferences (.rst vs .md)

### User Preference

**For internal documentation (user will edit/review):**
- ✅ Prefer `.rst` (reStructuredText)
- Better formatting model
- Tables are legible in plain text
- Faster to parse for user
- More precise control

**For external/ecosystem documentation:**
- ✅ Use `.md` (Markdown)
- GitHub ecosystem expectation
- README files (GitHub rendering)
- MCP project documentation (ecosystem standard)
- Any file that scanners/tools expect to be Markdown

### Decision Tree

**Use .rst when:**
- Creating design documents for user review
- Writing specifications user will edit
- Creating internal notes/documentation
- User is primary audience
- No external tool/platform expects .md

**Use .md when:**
- Creating README files (GitHub)
- MCP project documentation (ecosystem)
- Contributing to open source (project standards)
- External tools scan for .md files
- Platform expects Markdown (GitHub, GitLab, etc.)

### Rationale

User understands .rst better:
- Tables look legible in plain text
- Better mental model of formatting
- Faster parsing/comprehension
- More precise semantic markup

Markdown is for ecosystem compatibility, not user preference.

### Implementation

When creating documentation:
1. Identify primary audience (user vs external)
2. Check if ecosystem/tools expect specific format
3. Default to .rst for user-facing docs
4. Use .md only when ecosystem requires it

---

## Summary of System Prompt Changes Needed

1. **Aggressiveness controls** - Ask permission before workarounds, flags, process kills
2. **MCP documentation format** - Standardized sections for each MCP
3. **Process termination guardrails** - Never kill user-critical processes without permission
4. **File edit verification** - Always read back after modifications
5. **Package quality filters** - Don't present abandonware (<5 stars, >3 months stale)
6. **Cursor UI warnings** - Warn about mcp.json rewrite behavior
7. **Path resolution debugging** - Check actual PATH, don't assume
8. **Dependency assessment** - Check for deprecated/abandoned dependencies
9. **Unicode awareness** - Anticipate Windows cp1252 vs UTF-8 issues
10. **Native module warnings** - Treat node-gyp as red flag on Windows
11. **API verification requirements** - Cite official docs for technical claims
12. **Build vs buy framework** - Support build decision when ecosystem is broken
13. **Multiple solution proposals** - Present alternatives with tradeoffs
14. **Header comment format by file type** - Explicit examples for each comment syntax
15. **Runaway execution prevention** - Stop and ask before approach changes, no hacks without permission, RCA before pivoting
16. **File format preferences** - Use .rst for user docs, .md for ecosystem docs

These issues should be addressed in a comprehensive system prompt revision in a future chat.
