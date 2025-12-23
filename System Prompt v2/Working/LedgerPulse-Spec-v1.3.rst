============================================================
LedgerPulse Spec v1.3 — Append-Only AI Session Persistence
============================================================

:Author: GPT-5.2 (OpenAI)
:Date: 2025-12-22T21:08:50Z
:Target OS: Windows 11
:Global Root: ``$HOME/.leger/``
:Status: Final (pragmatic, known-imperfect)

----------------------------------------
1. Design Principles (Normative)
----------------------------------------

1. Append-only. Pulses are never edited or deleted.
2. Single source of truth. The pulse stream is canonical; chat history is disposable.
3. Crash tolerance. Termination may occur at any point.
4. Multi-focus native. One pulse may reference multiple concurrent threads.
5. Human-readable, machine-validated.
6. Recoverability over elegance.

----------------------------------------
2. On-Disk Layout (Normative)
----------------------------------------

::

  $HOME/.leger/
    stream/
      pulses.log.md
      pulses.log.md.idx.json
      archive/
        pulses-YYYYMMDD-HHMMSS.mmmZ.log.md
        pulses-YYYYMMDD-HHMMSS.mmmZ.idx.json
    spec/
      LedgerPulse-Spec-v1.3.rst
      AGENTS.md

----------------------------------------
3. Pulse Record Format (Normative)
----------------------------------------

Each pulse is one Markdown record with YAML front-matter and optional body.

::

  ---
  schema: ledgerpulse/v1
  ts: 2025-12-22T21:18:12.483Z
  type: pulse
  focus:
    - proj: "A"
      topic: "TLS investigation"
    - proj: "B"
      topic: "System prompt design"
  intent:
    primary: "continue"
    summary: "Investigate TLS chain in A; then return to prompt work in B."
  status:
    confidence: "medium"
    blocked: false
  next:
    - "Run openssl s_client and capture full chain"
    - "Return to LedgerPulse spec edits"
  files:
    - "C:/normalized/path/file.txt"
  tags: ["context-switch"]
  ---
  facts:
  - "curl -v shows unknown CA error"

----------------------------------------
4. Timestamp Semantics (Normative)
----------------------------------------

- ``ts`` MUST be RFC 3339 UTC with millisecond (or better) precision.
- Timestamps are authoritative; **no IDs are used**.
- Ordering is purely chronological by ``ts``.

----------------------------------------
5. Pulse Types (Normative)
----------------------------------------

- ``pulse``
- ``checkpoint``
- ``tangent``
- ``compaction``
- ``handoff``

----------------------------------------
6. ADHD Tangents (Normative)
----------------------------------------

- Tangents MUST be ``type: tangent``.
- Tangents MUST NOT change intent.
- Tangents are excluded by default during rehydration.
- Tangents MAY use ``defer`` instead of ``next``.

----------------------------------------
7. Mental Model Shifts (Normative)
----------------------------------------

Optional field::

  model_shift:
    before: "..."
    after: "..."

Use whenever assumptions materially change.

----------------------------------------
8. Timing & Trigger Rules (Normative)
----------------------------------------

A pulse MUST be written:

- Every 10 minutes of wall-clock time.
- On any decision, success/failure, focus change.
- After any tool execution that modifies state, is irreversible,
  or runs longer than ~2 minutes.

----------------------------------------
9. Atomic Write Protocol (Normative)
----------------------------------------

1. Write pulse to temp file.
2. fsync temp file.
3. Append to ``pulses.log.md``.
4. fsync log.
5. Delete temp file.

Readers MUST ignore incomplete trailing YAML.

----------------------------------------
10. Index File (Normative)
----------------------------------------

Index maps timestamps to byte offsets.

::

  {
    "schema": "ledgerpulse-index/v1",
    "offsets": {
      "2025-12-22T21:18:12.483Z": 98123321
    }
  }

----------------------------------------
11. Rehydration Algorithm (Normative)
----------------------------------------

- Read newest → oldest.
- Max 500 pulses.
- Skip tangents unless requested.
- Stop early if intent + next steps stabilize.

----------------------------------------
12. Compaction & Rotation (Normative)
----------------------------------------

- Trigger: log size > 100MB.
- Emit compaction pulse.
- Archive old segment.
- Start new log with compaction pulse.

----------------------------------------
13. Secrets Handling (Normative)
----------------------------------------

- Pulses MUST NOT inline secrets.
- Secrets live under ``$HOME/.secrets``.
- If required secret missing, create/update secrets file.
- Pulses may reference secrets symbolically.

----------------------------------------
14. Lockfile Handling (Normative)
----------------------------------------

- Writers MUST acquire ``pulses.lock``.
- If lock exists, **alert Lucky immediately**.
- Concurrent writers are considered an error condition.

----------------------------------------
15. System Prompt Reference
----------------------------------------

See ``SystemPrompt-LedgerPulse.md``.

End of spec.
