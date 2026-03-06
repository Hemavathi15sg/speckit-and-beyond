# Exercise 2: Understanding the Real Problem

> **Time:** 3:20 PM - 3:45 PM (25 minutes)  
> **Status:** Copilot's PR ready. But should we merge it?

## 🔍 The Situation

**From Experiment 1:** 
- ✅ NULL_DIETARY_BUG documented (issue #1)
- ✅ Assigned to @copilot → **PR #2 created** (null check fix)
- ✅ Fix is **technically correct** - stops crashes

**Investigation Further:**
- ✅ Check architecture for deeper problems first
- ✅ Refactor once vs patch repeatedly
- ⚠️ Takes longer, more risk

**Question:** Is search.py a clean 200-line file or a 1000+ line monolith?

**Mission:** Create a **Custom Architect Agent** to analyze search system architecture and discover any deeper issues.

---

## 🎯 Learning Objectives

- ✅ Create custom agents with specialized expertise
-  Use `.agent.md` files to give agents domain knowledge
- ✅ Perform autonomous codebase-wide analysis
- ✅ Identify systemic issues beyond surface symptoms

---

## � Why Custom Agents?

**The Problem with Generic @copilot:**

- ✅ Generic @copilot sees the bug → creates null check fix → done
- ❌ Doesn't question if the codebase has deeper issues
- ❌ Treats every bug as isolated

**Result:** You get a **quick patch** but miss systemic problems. The monolith grows, technical debt accumulates, and the next bug requires another patch.

**The Solution: Custom Agents**

Let's create a **@search-architect** agent with specialized expertise to:
- Analyze the entire search system architecture
- Identify root causes at the design level
- Recommend proper solutions, not just patches

---

## 📝 Exercise 2.0: Build Search Architect Agent (10 min)

### Task
Create a custom agent that's an expert in search systems.

### Steps

**2.0.1** Create the custom agent using Copilot Chat UI:

1. Open **GitHub Copilot Chat** (Ctrl+Shift+I or Cmd+Shift+I)

2. Click the **⚙️ Configure** button (top-right of chat panel)

3. Select **Custom Agents** from the menu

   ![Configure Menu - Select Custom Agents](assets/customagent.png)
   *The Configure menu with Custom Agents option highlighted*

4. Click **➕ New Custom Agent** button

   ![New Custom Agent Button](assets/newcustomagent.png)
   *Select "New custom agent..." to create a specialized agent*

5. In the file dialog, select location:

   ![Select Location Dialog](assets/githubcustomagent.png)
   *Choose .github\agents as the location for your custom agent*
   - Enter agent name: `search-architect`
   - Click Save

**Result:** VS Code creates `.github/agents/search-architect.agent.md` automatically

---

**2.0.2** Copy the agent definition:

Replace the generated template with the following content:

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

💡 **Optional - Configure Tools:** If your agent needs to perform actions (create files, run commands, etc.), you can enable specific tools in the frontmatter by uncommenting the `tools:` line and specifying which tools to enable. For analysis-only agents like search-architect, tools are not required.

   ![Configure Tools](assets/configuretools.png)
   *Tools can be enabled in agent configuration as needed*

**2.0.3** Save the file and reload VS Code window

### What You Created
A specialist agent with **deep domain expertise** in search systems.

---

## 📝 Exercise 2.1: Invoke Deep Analysis (8 min)

### Task
Ask your architect agent to analyze the NULL_DIETARY_BUG deeply.

### Steps

**2.1.1** Open Copilot Chat 

**2.1.2** Click the **Agent** dropdown and select **search-architect** from the list

   ![Select Search Architect Agent](assets/customarchitectagent.png)
   *The search-architect custom agent available in the agents dropdown*

**2.1.3** Enter your prompt:
```
Review NULL_DIETARY_BUG and analyze search.py comprehensively. 
The null handling bug is just a symptom - what's the real architectural state?

Context: search.py has grown to 1103 lines over 18 months. 
Users complained about slow searches before this bug appeared.
```

### Expected Analysis

```
ARCHITECTURAL ANALYSIS - NULL_DIETARY_BUG

Current State: 1103 lines - GOD OBJECT ANTI-PATTERN

What's in this file:
- Database connection, query parsing, input validation
- Filtering logic (5 active + 3 deprecated versions)
- Dietary restriction handling (THE BUG: Line 447)
- Ranking algorithms, A/B testing, response formatting
- Caching (broken, memory leak), metrics, debug logs

CRITICAL ISSUES:
1. Line 447: Null bug crashes 23% of users
2. God Object: 1103 lines, untestable (0% coverage)
3. Dead code: 300+ lines of deprecated filters/algorithms
4. 74 magic numbers hard-coded
5. Broken caching causing memory leaks

RECOMMENDATION:
Architectural refactor into 4 modules:
- validation_module.py (input validation, null handling)
- filtering_module.py (clean filter functions)
- aggregation_module.py (ranking, caching fixed)
- formatting_module.py (response formatting)

Benefits: Each module <300 lines, testable, maintainable
Effort: 3-4 days traditional, 2 hours with Spec Kit + Agents

This is an architectural crisis. Refactor is non-negotiable.
```

### What Just Happened

The agent analyzed the actual code and gave honest assessment:
- ⚠️ **ARCHITECTURAL REFACTOR** required for 1103-line God Object
- ⚠️ Break into 4 modules (validation, filtering, aggregation, formatting)
- ⚠️ 3-4 days traditional, 2 hours with Spec Kit governance
- ⚠️ Multiple systemic issues beyond just the null bug

**Key Insight:** The agent provided evidence-based analysis showing this is genuinely a refactor scenario.

---

## 📝 Exercise 2.2: Learn Refactor Principles  (7 min)

### Task


For **learning purposes**, let's ask: "What would refactor principles look like for a future scenario where we DO need to redesign?"

This teaches you governance principles you'll use in Experiment 3.

### Steps

**2.2.1** Select **search-architect** agent again and ask:

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

**Continue to:** [Exercise 3: Solution Design & Implementation](exercise-3.md)

Time to use: **Spec Kit** for governance-driven design.



