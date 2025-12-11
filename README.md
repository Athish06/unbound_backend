# **Architecture: The 3-Layer Defense System**

---

## **1. Layer 1 — “The Constitution” (Admin Rule Engine)**

This is the first and fastest layer. It uses strict Regular Expression (Regex) rules defined by administrators.

### **How It Works**
- Implemented using Python `re` and SQLModel with PostgreSQL.
- Matches incoming commands against stored **Allow** or **Block** patterns.
- Executes in under **1 millisecond**.

### **Behavior**
- If a command matches a **Block** rule, it is rejected immediately.
- If a command matches an **Allow** rule, it is forwarded to Layer 2 for verification.
- Prevents accidental over-permissive rules such as `.*`.

---

## **2. Layer 2 — “The Watchdog” (Token Risk Scoring Engine)**

This layer performs semantic analysis by tokenizing commands using `shlex` to understand structure and intent.

### **Risk Scoring Logic**
Each command is scored based on the following components:

| **Component** | **Example** | **Points** |
|--------------|-------------|------------|
| Binary Risk  | `ls = 0`, `python = 50`, `mkfs = 100` | +0 to +100 |
| Flag Risk    | `rm -f` | +50 |
| Target Risk  | `/`, `/etc`, `/boot` | +100 |

### **Decision Thresholds**
- **0 points:** Command is safe and executed.  
- **1–99 points:** Ambiguous commands are escalated to Layer 3.  
- **100+ points:** Command is blocked.

### **Performance**
- Executes in under **5 milliseconds**.
- Designed to balance accuracy and efficiency.

---

## **3. Layer 3 — “The Judge” (AI Context Analyzer)**

This is the final evaluation layer. It uses an LLM (Google Gemini or OpenAI GPT-4o) to analyze commands that cannot be conclusively classified by Layers 1 and 2.

### **Responsibilities**
- Detects obfuscated, encoded, or suspicious commands.
- Resolves disagreements between Layer 1 and Layer 2.
- Provides the final **Safe / Unsafe** verdict.

### **Purpose of AI**
- Handles high-context decisions that Regex and heuristic scoring cannot process.
- Interprets command intent rather than relying only on patterns or tokens.
- Eliminates risk from human configuration mistakes.

---

# **Mathematical Conflict Detection (DFA Intersection Method)**

Rule conflicts are detected using Finite Automata Theory to ensure mathematical correctness, even when patterns overlap in complex ways.

---

## **Step-by-Step Process**

### **1. Convert Regex to DFA**
Using the **Greenery** library:
- The new rule (**Pattern A**) is converted into a Deterministic Finite Automaton (DFA).
- Each existing rule (**Pattern B**) from the database is also converted into a DFA.

---

### **2. Compute DFA Intersection**
The system calculates:
Intersection = DFA(A) ∩ DFA(B)

The resulting DFA accepts only the strings that match **both** patterns.

---

### **3. Analyze Intersection**
- If the intersection is **empty**, the rules are disjoint and the new rule is safe to add.
- If the intersection is **not empty**, a conflict exists and the new rule is rejected.
- A counter-example command can be generated to show exactly which input triggers both patterns.


