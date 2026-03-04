# Experiment 2: Understanding the Real Problem

> **Time:** 3:20 PM - 3:45 PM (25 minutes)  
> **Status:** Copilot's PR ready. But should we merge it?

## 🔍 The Situation

**From Experiment 1:** 
- ✅ NULL_DIETARY_BUG documented (issue #1)
- ✅ Assigned to @copilot → **PR #2 created** (null check fix)
- ✅ Fix is **technically correct** - stops crashes

**Before merging PR #2:**

**Quick Fix (Merge):**
- ✅ Deploys in 10 min, stops crashes
- ⚠️ If symptom of deeper issues, just patching

**Investigation (Hold PR):**
- ✅ Understand full scope first
- ✅ Refactor once vs patch repeatedly
- ⚠️ Takes longer, more risk

**Question:** Is search.py a clean 200-line file or a 1000+ line monolith?

**Mission:** Create a **Custom Architect Agent** to review PR #2 and make informed merge decision.

---

## 🎯 Learning Objectives

- ✅ Create custom agents with specialized expertise
-  Use `.agent.md` files to give agents domain knowledge
- ✅ Perform autonomous codebase-wide analysis
- ✅ Identify systemic issues beyond surface symptoms

**Agent Capability:** Custom Agents (specialized intelligence)

---

## 📝 Exercise 2.1: Build Search Architect Agent (10 min)

### Task
Create a custom agent that's an expert in search systems.

### Steps

**2.1.1** Create `.github/agents/search-architect.agent.md`:

```yaml
---
name: search-architect
description: Senior software architect specializing in search systems, scalability, and code architecture. Analyzes search implementations for performance, reliability, and maintainability issues.
argument-hint: A codebase, file, or GitHub issue to analyze for architectural problems and modernization opportunities.
# tools: Optionally enable tools if this agent needs to perform actions - leave commented out for analysis-only agents
---

# Search Architect Agent

## Identity
You are a senior software architect specializing in search systems, scalability, and maintainability.

## Expertise
- Search algorithm design and optimization
- Code architecture patterns and anti-patterns
- Performance analysis and bottleneck identification
- Reliability and fault tolerance patterns

## Context: FlavorHub Recipe Manager
- 2M recipes in database
- 10M monthly active users
- Current search: filter-based, file-based implementation
- Tech stack: Python 3.11, FastAPI, PostgreSQL

## Your Mission
When analyzing search code, you autonomously:
1. Evaluate architecture (monolith vs modular)
2. Identify performance bottlenecks
3. Find reliability issues (not just the reported bug)
4. Assess code maintainability and testability
5. Recommend modernization strategy with priorities

## Behavior
- **Scan entire subsystem**, not just bug location
- **Provide concrete evidence** from actual code
- **Prioritize recommendations** by business impact
- **Think long-term**: What breaks at 100M users?
```

💡 **Important:** The top section (between `---`) is YAML frontmatter that VS Code uses to register the agent. The rest defines the agent's behavior.

**2.1.2** Save the file and reload VS Code window:
- Press `Ctrl+Shift+P` (Windows) or `Cmd+Shift+P` (Mac)
- Type: `Developer: Reload Window`
- Wait 30 seconds for the agent to register

**2.1.3** Verify the agent is available:
- Open Copilot Chat
- Click the **Agent** dropdown (or press Ctrl+Shift+I)
- Look for **search-architect** in the agents list

### What You Created
A specialist agent with **deep domain expertise** in search systems.

---

## 📝 Exercise 2.2: Invoke Deep Analysis (8 min)

### Task
Ask your architect agent to analyze the NULL_DIETARY_BUG deeply.

### Steps

**2.2.1** Open Copilot Chat (Ctrl+Alt+I / Cmd+Opt+I)

**2.2.2** Click the **Agent** dropdown (or press Ctrl+Shift+I) and select **search-architect** from the list

**2.2.3** Enter your prompt:
```
Review GitHub NULL_DIETARY_BUG and analyze search.py comprehensively. 
The null handling bug is just a symptom - what's the real architectural state?

Context: search.py has grown to 1103 lines over 18 months. 
Users complained about slow searches before this bug appeared.
```

### Expected Analysis

```
ARCHITECTURAL ANALYSIS - NULL_DIETARY_BUG

Actual file: 1103 lines - TEXTBOOK GOD OBJECT ANTI-PATTERN

What's in this file (everything!):
- Database connection management (hard-coded, no pooling)
- Query parsing (3 versions - v1, v2, v3, only v3 used)
- Input validation (none - accepts garbage)
- Filtering logic (5 active + 3 deprecated versions)
- Dietary restriction handling (THE BUG: Line 447)
- Ranking algorithms (3 versions: basic, weighted_v2, hybrid_v3)
- A/B testing variants (12 feature flags)
- Response formatting (JSON + half-finished XML support)
- Pagination (inefficient, loads everything in memory)
- Caching layer (broken, memory leak - CACHE_LEAK_BUG)
- Metrics collection (manual counters)
- Debug logging (print statements everywhere)

CRITICAL ISSUES:

1. Line 447: The null bug (crashes 23% of users)
   Root cause: No validation + God object makes null checks impossible
   
2. God Object anti-pattern: 1103 lines doing everything
   Impact: Untestable (0% coverage), unmaintainable
   
3. Dead code: 3 deprecated filter versions, 2 old ranking algorithms
   Impact: Confusion about what's production vs experiment
   
4. 74 magic numbers: No constants, hard-coded everywhere
   Impact: Can't tune without code changes
   
5. Broken caching: Memory leak, no eviction (CACHE_LEAK_BUG)
   Impact: Service crashes after 8 hours uptime
   
6. Feature flag chaos: 12 flags, unclear which are active
   Impact: Unknown production behavior

RECOMMENDATION FOR 1103-LINE MONOLITH:

**ARCHITECTURAL REFACTOR required**

Break into 4 modules:

1. validation_module.py (200 lines)
   - Input validation with Pydantic
   - Null handling fixed at entry point
   - Type safety enforced
   
2. filtering_module.py (300 lines)
   - Clean filter functions (remove deprecated versions)
   - Optimized filter ordering
   - No database connection logic
   
3. aggregation_module.py (250 lines)
   - Single ranking algorithm (remove experiments)
   - Caching logic (fix memory leak)
   - Metrics properly abstracted
   
4. formatting_module.py (150 lines)
   - Response formatting
   - Pagination (proper implementation)
   - No business logic

Benefits:
- Each module <300 lines, single responsibility
- Testable independently (can achieve >80% coverage)
- Remove dead code and experiments
- Fix caching and null bugs properly
- Clear separation of concerns

Estimated effort: 3-4 days (traditional approach)
With Spec Kit + Agents: 2 hours (governed implementation)

This IS the architectural crisis. Refactor is non-negotiable.
```

### What Just Happened

The agent analyzed the actual code and gave honest assessment:
- ⚠️ **ARCHITECTURAL REFACTOR** required for 1103-line God Object
- ⚠️ Break into 4 modules (validation, filtering, aggregation, formatting)
- ⚠️ 3-4 days traditional, 2 hours with Spec Kit governance
- ⚠️ Multiple systemic issues beyond just the null bug

**Key Insight:** The agent provided evidence-based analysis showing this is genuinely a refactor scenario.

---

## 📝 Exercise 2.3: Learn Refactor Principles (For Education) (7 min)

### Task


For **learning purposes**, let's ask: "What would refactor principles look like for a future scenario where we DO need to redesign?"

This teaches you governance principles you'll use in Experiment 3.

### Steps

**2.3.1** Select **search-architect** agent again and ask:

```
You recommended architectural refactor into 4 modules. 
Before we start coding, what principles should govern this work? 
What's non-negotiable for production search at our scale?
```

### Expected Response

```
GOVERNANCE PRINCIPLES FOR THIS REFACTOR

For breaking 1103-line monolith into 4 clean modules:

1. RELIABILITY: Graceful degradation over crashes
   - Validation at entry point (Pydantic models)
   - Fail-safe defaults (null → empty list)
   - Input sanitization before processing
   - Null checks everywhere external data arrives

2. ARCHITECTURE: Separation of concerns
   - validation_module.py: Pydantic models, input validation
   - filtering_module.py: All filter logic, no ranking
   - aggregation_module.py: Ranking algorithms, caching
   - formatting_module.py: Response formatting, pagination
   - EACH module: Single responsibility, <300 lines, independently testable

3. TESTABILITY: >80% code coverage
   - Every component has unit tests
   - Integration tests for end-to-end flows
   - Test edge cases (null inputs, empty lists, malformed data)

4. PERFORMANCE: Monitor and optimize when needed
   - Start with simple implementation
   - Add caching if measurements show need
   - Database indexes for common queries
   - Profile before optimizing

For your CURRENT refactor:
- #1 (reliability) - Fix null bug at validation entry point
- #2 (architecture) - Break into 4 modules, remove dead code
- #3 (testability) - Each module independently testable, >80% coverage
- #4 (performance) - Fix caching leak, optimize filter ordering
```

**2.3.2** Note these principles - you'll use them in Experiment 3 to guide improvements.

### What Just Happened
Agent established **governance principles** for the refactor. These will guide implementation in Experiment 3-4 using Spec Kit constitution.

---

## ✅ Checkpoint: What You Accomplished

🎯 **Root cause identified**: 1103-line God Object with systemic issues  
🎯 **Custom agent** analyzed comprehensively - recommended ARCHITECTURAL REFACTOR  
🎯 **Learned principles**: Separation of concerns, reliability, testability, performance  
🎯 **Clear plan**: Break into 4 modules (validation, filtering, aggregation, formatting)

**Key Insight:** Evidence-based architecture review reveals when refactor is genuinely needed. This is a real architectural crisis requiring modular redesign.

**Current Time:** 3:45 PM  
**Status:** We know the problem (null bug + validation gap) and learned architectural principles.

---

## 🚀 Next: Experiment 3

In the next experiment, we'll use these principles to guide our ARCHITECTURAL REFACTOR with **Spec Kit** providing governance and structured specifications.

We have principles, but no specification. How do we translate *"break into 4 modules following clean architecture"* into concrete implementation tasks?

**Continue to:** [Experiment 3: Designing the Solution](experiment-3.md)

Time to use: **Instruction Files + Spec Kit** for governance-driven design.



