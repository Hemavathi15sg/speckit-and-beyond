# Experiment 3: Solution Design & Implementation

> **Time:** 3:45 PM - 4:48 PM (63 minutes)  
> **Status:** Decision made to refactor. Need specification, technical plan, and implementation.

## 🎯 The Challenge

@search-architect gave us principles and identified the problem: 1103-line monolith needs breaking into 4 modules.

But how do we turn *"refactor 1103-line God Object into clean architecture"* into concrete, trackable implementation tasks - and then actually build it?

**Your mission:** Use Spec Kit to create governance-driven specifications, then implement the solution using Copilot CLI and /speckit.implement.

---

## 🎯 Learning Objectives

- ✅ Install and configure Spec Kit for existing projects
- ✅ Use Spec Kit to establish project constitution
- ✅ Generate detailed specifications from high-level intent
- ✅ Create technical plans with architectural decisions
- ✅ Break specifications into actionable implementation tasks
- ✅ Use Copilot CLI for multi-step project setup
- ✅ Generate code with /speckit.implement following specifications
- ✅ Wire modules together with @workspace for integration
- ✅ Understand the complete Spec Kit workflow from design to implementation

**Agent Capabilities:** Spec Kit (constitution → specify → plan → tasks → implement) + Copilot CLI + @workspace

---

## 📝 Exercise 3.0: Install Spec Kit (5 min)

### Task
Install GitHub Spec Kit extension for specification-driven development.

### Steps

**3.0.1** Install Spec Kit:

1. Visit [GitHub Spec Kit repository](https://github.com/github/spec-kit)

2. Install using the following command:

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```
![GitHub Spec Kit](assets/speckit.png)
   *GitHub Spec Kit - Specification-driven development framework*

**3.0.2** Initialize Spec Kit in your existing project:

```bash
# Navigate to recipe-manager directory
cd recipe-manager

# Initialize Spec Kit for existing directory
specify init --here
```

When prompted "Initialize Spec Kit in existing repository?", type **yes** to continue

   ![Spec Kit Same Repo Confirmation](assets/specsamerepo.png)
   *Select 'yes' to initialize Spec Kit in the existing repository*

**3.0.3** Verify Spec Kit slash commands are available:

Open Copilot Chat and type `/spec` - you should see:
- `/speckit.constitution` - Create governance rules
- `/speckit.specify` - Generate specifications
- `/speckit.plan` - Create technical plans
- `/speckit.implement` - Generate code from specs
- `/speckit.analyze` - Validate specifications

   ![Spec Kit Slash Commands](assets/specfinal.png)
   *Spec Kit slash commands available in Copilot Chat*

### What You Created
- ✅ Spec Kit initialized in your project
- ✅ `.specify/` folder created for specifications
- ✅ Slash commands available for specification workflow

---

## 📝 Exercise 3.1: Establish Constitution (8 min)

### Task
Use Spec Kit to create governance from architect's principles.

### Steps

**3.1.1** In Copilot Chat, use the `/speckit.constitution` command:

```
/speckit.constitution

Context: "Refactoring FlavorHub search.py (1103 lines) into clean architecture. 
Based on #search-architect analysis:
1. Reliability (input validation + null handling)
2. Architecture (break into 4 modules: validation, filtering, aggregation, formatting)
3. Testability (>80% coverage, each module independently testable)
4. Performance (fix caching leak, optimize filter ordering)
5. Maintainability (remove 74 magic numbers, eliminate dead code)

Domain context available in models.py, search.py, and README.md"
```

### Expected Output

Spec Kit creates `constitution.md`:

**Mission:** Transform 1103-line monolith → 4 clean modules

**Principles:**
1. Modular Architecture (4 modules <300 lines each)
2. Reliability & Safety (input validation, error handling)
3. Clean Code Quality (remove dead code, fix caching)
4. Quality Standards (>85% coverage, 100% type hints)
5. Deployment Safety (backward compatible, feature flags)

---

## 📝 Exercise 3.2: Generate Specification (10 min)

### Task
Create detailed spec for breaking monolith into 4 modules.

### Steps

**3.2.1** Continue with Spec Kit:

```
/speckit.specify "Break search.py into 4 modules following constitution.
Fix null handling via validation_module. 
Remove dead code. Fix caching leak. 
Each module independently testable with >80% coverage.
Maintain API backward compatibility."
```

### Expected Output

Spec Kit generates `specification.md`:

**4 Modules:**
1. validation_module.py (~200 lines) - FIXES NULL_DIETARY_BUG
2. filtering_module.py (~300 lines) - Remove deprecated code
3. aggregation_module.py (~250 lines) - FIXES CACHE_LEAK_BUG
4. formatting_module.py (~150 lines) - Remove XML support

**Success Criteria:** <300 lines each, both bugs fixed, >80% coverage, API compatible

**3.2.2** Review the specification. This defines WHAT we're building.

---

## 📝 Exercise 3.3: Generate Technical Plan (8 min)

### Task
Create technical plan showing HOW to implement the specification.

### Why This Step Matters

Spec Kit separates concerns:
- `/speckit.specify` → **WHAT** to build (requirements, features, success criteria)
- `/speckit.plan` → **HOW** to build it (technical approach, architecture, design patterns)
- `/speckit.tasks` → **WHEN/WHO** (implementation breakdown, dependencies)

The plan bridges the gap between specification and implementation.

### Steps

**3.3.1** Continue with Spec Kit:

```
/speckit.plan "Create technical implementation plan for 4-module refactor.
Based on specification, detail the technical approach for:
- Module extraction strategy (which functions move where)
- Interface design between modules
- Migration sequence (minimize disruption)
- Testing strategy for each module
- Rollout approach (gradual vs big-bang)

Follow constitution and consider current 1103-line structure."
```

### Expected Output

Spec Kit generates `plan.md`:

**5-Phase Migration:**
1. Extract validation_module (fixes NULL_DIETARY_BUG)
2. Extract filtering_module (remove deprecated code)
3. Extract aggregation_module (fixes CACHE_LEAK_BUG)
4. Extract formatting_module (remove XML support)
5. Integration & cleanup

**Testing:** 65 unit tests, >80% coverage | **Rollout:** Gradual per-module

**3.3.2** Review the plan. This defines HOW we'll implement the specification.

### What Just Happened

Spec Kit created **technical implementation plan**:
- **Module extraction strategy** - which functions move where
- **5-phase migration** - incremental, testable, rollback-safe
- **Interface design** - clean boundaries between modules  
- **Testing strategy** - 65 unit tests + integration tests
- **Rollout approach** - gradual with feature flags

This bridges the gap between "what to build" (spec) and "implementation tasks" (next step).

---

## 📝 Exercise 3.4: Generate Task Breakdown (5 min)

### Task
Turn specification into ordered implementation tasks.

### Steps

**3.4.1** In Copilot Chat:

```
/speckit.tasks
```

### Expected Output

Spec Kit generates `tasks.md`:

**Phase 1:** validation_module.py (P0) - Create SearchQuery model, fix NULL_DIETARY_BUG
**Phase 2:** filtering_module.py (P0) - Move filters, remove deprecated versions
**Phase 3:** aggregation_module.py (P0) - Fix CACHE_LEAK_BUG, replace magic numbers
**Phase 4:** formatting_module.py (P1) - Move formatting, remove XML
**Phase 5:** Integration (P0) - Update imports, verify coverage

**3.4.2** Note: Spec Kit understood dependencies automatically.

---

## 📝 Exercise 3.5: Use Copilot CLI for Setup (10 min)

### Task
Use CLI to handle multi-step foundation work.

### Prerequisites

**Install Copilot CLI:**
```bash
npm install -g @githubnext/github-copilot-cli
```

### Steps

**3.5.1** Use CLI for setup tasks:

```bash
"Setup for search modularization refactor:
1. Add pydantic to requirements.txt
2. Create 4 new module files: validation_module.py, filtering_module.py, aggregation_module.py, formatting_module.py
3. Create corresponding test files in tests/
4. Add pytest and pytest-cov to dev dependencies
5. Create .coveragerc configuration for 80% coverage target per module"
```

**3.5.2** Review CLI's suggested commands, then execute:

```bash
# CLI generates these commands (review and run):
pip install pydantic
touch validation_module.py filtering_module.py aggregation_module.py formatting_module.py
mkdir -p tests
touch tests/test_validation.py tests/test_filtering.py tests/test_aggregation.py tests/test_formatting.py
cat >> requirements.txt <<EOF
pydantic>=2.0.0
EOF

cat >> requirements-dev.txt <<EOF
pytest>=7.0.0
pytest-cov>=4.0.0
EOF

cat > .coveragerc <<EOF
[run]
source = .
omit = tests/*,venv/*

[report]
precision = 2
fail_under = 80
EOF
```

### What Just Happened
CLI handled boring setup work - installed dependencies, created 4 module files + test structure, configured coverage following your spec.

---

## 📝 Exercise 3.6: Implement Modules with Spec Kit (10 min)

### Task
Generate the 4 modules from specification using Spec Kit.

**Why Spec Kit:** Best for generating code that must follow specifications exactly.

### Steps

**3.6.1** Implement validation_module.py:

```
/speckit.implement "Create validation_module.py from specification. 
Include SearchQuery Pydantic model with null-to-list validators (NULL_DIETARY_BUG fix).
Add validate_search_request() entry point with comprehensive input validation."
```

**Expected:** Spec Kit creates validation_module.py with SearchQuery BaseModel, validators for None→[], validate_search_request() function, type hints and error handling.

**3.6.2** Implement filtering_module.py:

```
/speckit.implement "Create filtering_module.py from specification.
Include filter_recipes() main function and all helper filter functions.
Remove deprecated versions. Use optimized filter ordering (most selective first)."
```

**Expected:** Spec Kit creates filtering_module.py with filter_recipes() orchestrator and helper functions, null-safe and production-ready.

**3.6.3** Implement aggregation_module.py:

```
/speckit.implement "Create aggregation_module.py from specification.
Include rank_recipes() with hybrid_v3 algorithm only. 
Replace magic numbers with named constants (RELEVANCE_WEIGHT, etc.).
Fix caching with LRU (CACHE_LEAK_BUG)."
```

**Expected:** Spec Kit creates aggregation_module.py with named constants, rank_recipes() with hybrid_v3, and LRU caching.

**3.6.4** Implement formatting_module.py:

```
/speckit.implement "Create formatting_module.py from specification.
Include format_search_response() with pagination.
JSON formatting only (remove XML support)."
```

**Expected:** Spec Kit creates formatting_module.py with format_search_response(), pagination logic, JSON-only support.

### What Just Happened
Spec Kit **read your specification and constitution**, then generated 4 clean, modular files following clean architecture principles.

**Spec Kit is ideal for:** Generating multiple components that must precisely follow architectural specifications.

---

## 📝 Exercise 3.7: Wire Modules Together with @workspace (7 min)

### Task
Create clean orchestrator in __init__.py that wires all 4 modules together.

**Why @workspace:** Best for integration with full codebase context.

### Steps

**3.7.1** In Copilot Chat:

```
@workspace Create __init__.py orchestrator that wires all 4 modules together.

Wire these modules with proper function signatures:
- validate_search_request(request_data, user) from validation_module
- apply_filters(recipes, query, criteria) from filtering_module  
- rank_and_cache(recipes, query, criteria) from aggregation_module
- format_search_response(ranked_recipes, page, page_size) from formatting_module

The orchestrator should:
1. Import all 4 modules
2. Call validation_module to fix NULL_DIETARY_BUG (None→[] conversion)
3. Pipeline data through filtering → aggregation → formatting
4. Handle errors gracefully per constitution
5. Maintain API backward compatibility

Keep __init__.py clean (<50 lines), just orchestration logic.
```

### What Just Happened
**@workspace** created clean orchestrator:
- Wired all 4 modules with correct function signatures
- Fixed NULL_DIETARY_BUG at validation layer (None converted to empty list)
- Fixed CACHE_LEAK_BUG in aggregation (LRU caching prevents memory leak)
- Pipeline pattern: validate → filter → rank → format
- Backward compatible API

**@workspace is ideal for:** Wiring components together, integration work with full context.

---

## 📊 Comparison: When to Use Each

| Scenario | Use This | Why |
|----------|----------|-----|
| **Generate new modules from spec** | `/speckit.implement` | Ensures spec compliance automatically |
| **Create multiple files at once** | `/speckit.implement` | Understands module boundaries |
| **Wire components together** | `@workspace` | Better at integration with context |
| **Must follow constitution** | `/speckit.implement` | Reads constitution, enforces principles |
| **Update existing orchestrator** | `@workspace` | Understands existing code structure |
| **Generate tests for each module** | Both work | Spec Kit auto-generates, @workspace on request |
| **Maintain architectural boundaries** | `/speckit.implement` | Constitution-aware |
| **Integration work** | `@workspace` | Sees full codebase context |

**Best practice:** Use both! Spec Kit for spec-driven components, @workspace for custom logic and refinement.

---

## ✅ Checkpoint: What You Accomplished

🎯 **Task breakdown** generated automatically  
🎯 **Setup automated** with Copilot CLI  
🎯 **4 modules created** with /speckit.implement (validation, filtering, aggregation, formatting)  
🎯 **Orchestrator created** in __init__.py wiring all modules with correct signatures  
🎯 **Dead code removed** (3 old filters, 2 old algorithms, XML support)  
🎯 **Caching fixed** (CACHE_LEAK_BUG - LRU eviction)  
🎯 **Null handling fixed** (NULL_DIETARY_BUG - validation converts None→[])  
🎯 **Tests generated** for all 4 modules  

**Agent Capabilities Used:**
- Copilot CLI: Multi-step automation for project setup  
- /speckit.implement: Generate 4 clean modules from architectural spec
- @workspace: Create clean orchestrator maintaining backward compatibility

**Current Time:** 4:48 PM  
**Status:** 4-module architecture implemented. From 1103-line monolith to clean separation of concerns. But is it quality?

---

## 🚀 Next: Experiment 4

Code is written, tests pass. But does it meet our constitution? Are we actually ready for production?

**Continue to:** [Experiment 4: Validation & Quality Gates](experiment-4.md)

Time to use: **/speckit.analyze + checklist** for systematic quality validation.




