Architecture: The 3-Layer Defense System
---
### 1. Layer 1 — “The Constitution” (Admin Rule Engine)

Fastest. Deterministic. Absolute.

This layer uses strict Regex rule enforcement defined by administrators.

How it works

Uses Python re + SQLModel (PostgreSQL).

Matches incoming commands against Allow or Block rules.

Instant (<1ms) response time.

Behavior

If matched with a Block rule → Command instantly rejected.

If matched with an Allow rule → Command moves to Layer 2 for verification.

Prevents accidental over-permissive rules (like .*).

### 2. Layer 2 — “The Watchdog” (Token Risk Scoring Engine)

Semantic. Fast. Analytical.

Commands are tokenized using shlex to understand intent rather than raw text.

Risk Scoring Logic

Each command receives a risk score:

Component	Example	Points
Binary Risk	ls = 0, python = 50, mkfs = 100	+0 to +100
Flag Risk	rm -f	+50
Target Risk	/, /etc, /boot	+100
Decision Thresholds

0 Points → EXECUTE (Safe)

1–99 Points → ESCALATE (Go to Layer 3)

≥100 Points → BLOCKED

Speed

<5ms

Balanced between safety and performance.

### 3. Layer 3 — “The Judge” (AI Context Analyzer)

Smart. Context-Aware. Final Authority.

Uses Google Gemini or OpenAI GPT-4o to analyze “ambiguous” commands.

Responsibilities

Detects obfuscated, encoded, or suspicious behavior.

Resolves disagreements between Layer 1 and Layer 2.

Final Safe / Unsafe decision-maker.

Why AI?

Humans make mistakes.

Regex and heuristics cannot detect high-context attacks.

AI can understand intent, not just syntax.

Mathematical Conflict Detection (DFA Intersection Method)

Rule conflicts are detected using Finite Automata Theory — not string comparison.

This ensures rule safety even in complex regex overlaps.

How It Works (Step-by-Step)
1. Convert Regex to DFA

Using the Greenery library:

Pattern A (new rule)

Pattern B (existing rule)

Both are converted into deterministic finite automata.

2. Compute DFA Intersection

Mathematically compute:

Intersection = DFA(A) ∩ DFA(B)


This machine represents all commands that both patterns match.

3. Analyze Intersection

If empty → No conflict. Rule is safe to add.

If not empty → Conflict exists. Rule creation is blocked.

The system can also produce a counter-example, showing which command caused the conflict.
