# Experiment 1 Creating Order from Chaos

> **Time:** 3:00 PM - 3:20 PM (20 minutes)  
> **Crisis State:** 500+ errors, users angry, no structure

## 🔥 The Situation

FlavorHub's search feature crashed. Production logs show:
```
TypeError: 'NoneType' object is not iterable
  File "search.py", line 116, in filter_by_dietary
    for restriction in user.dietary_restrictions:
```

30% of searches failing. Error reports flooding Slack. No GitHub issue yet. No analysis. Just chaos.

**Your mission:** Create structure from chaos using Agent Skills + GitHub MCP.

---

## 🎯 Learning Objectives

By end of this experiment, you will:
- ✅ Create and invoke agent skills for autonomous problem analysis
- ✅ Use GitHub MCP to automate GitHub operations
- ✅ Transform error logs into structured, actionable documentation

**Agent Capabilities:** Agent Skills (analysis) + GitHub MCP (automation)

---

## 📝 Exercise 1.0: Reproduce the Bug (5 min)

### Task
Before analyzing the error, let's reproduce it to get the actual stack trace and understand the problem.

### Steps

**1.0.1** Navigate to the recipe-manager folder:
```bash
cd recipe-manager
```

**1.0.2** Run the bug reproduction script:
```bash
python test_bug.py
```

**1.0.3** Observe the output:

```
🧪 Testing with Alice (has dietary restrictions)...
✅ Search succeeded!

🧪 Testing search with user who has dietary_restrictions=None...
❌ CRASH! (This is the NULL_DIETARY_BUG)
Error: 'NoneType' object is not iterable

Stack trace points to search.py line 116:
  for restriction in user.dietary_restrictions:
  TypeError: 'NoneType' object is not iterable

🧪 Testing with Bob (SAMPLE_USERS[1])...
❌ CRASH! Same issue as production
```

**1.0.4** Open `recipe-manager/search.py` and locate line 116 to see the problematic code:
```python
for restriction in user.dietary_restrictions:  # ← CRASHES IF None!
```

### What You Verified
- ✅ **Bug is real** - Reproducible with test script
- ✅ **Stack trace available** - You now have the error details for analysis
- ✅ **Root cause visible** - Line 116 iterates over None value
- ✅ **Context understood** - Affects users without dietary restrictions (30% of searches)

**💡 Now you have the stack trace and context to give your agent skill!**

---

## 📝 Exercise 1.1: Create Issue Analyzer Skill (6 min)

### Task
Build an agent skill that can analyze production errors intelligently.

### Steps

**1.1.1** Create the skill directory:
```bash
mkdir -p .github/skills/issue-analyzer
```

**1.1.2** Create `.github/skills/issue-analyzer/SKILL.md`:
```markdown
# Issue Analyzer Skill

You are an expert at diagnosing production errors and creating structured issue reports.

## Your Capabilities

When given error logs or stack traces, you autonomously:
1. **Extract root cause** from stack traces
2. **Identify affected files** and line numbers
3. **Assess severity** (critical/high/medium/low)
4. **Estimate impact** (% of users affected)
5. **Suggest immediate hotfix** and long-term solution
6. **Recommend labels** for issue tracking

## Output Format

Always structure your analysis as:
- **Title:** [Component] Brief description
- **Severity:** Critical/High/Medium/Low
- **Root Cause:** Technical explanation
- **Affected Files:** List with line numbers
- **Impact:** User-facing impact description
- **Immediate Fix:** Quick resolution
- **Long-term Fix:** Proper solution approach
```

**1.1.3** Save the file and restart VS Code (for skill to load).

### What You Created
An agent that thinks like a senior engineer - not just reading errors, but **analyzing root causes and impacts**.

---

## 📝 Exercise 1.2: Invoke the Skill (3 min)

### Task
Use your skill to analyze the production error.

### Steps

**1.2.1** In the terminal where you ran `python test_bug.py`, select the error output (the crash section with stack trace)

**1.2.2** Open Copilot Chat (Ctrl+Alt+I / Cmd+Opt+I) and invoke your skill:
```
Look at #terminalSelection using #file:issue-analyzer analyse the production error
```

💡 **What's happening:**
- `#terminalSelection` - Attaches the terminal output you selected
- `#file:issue-analyzer` - References your custom skill folder
- Copilot reads the SKILL.md and applies its analysis format

### Expected Output
```
ANALYSIS COMPLETE

Title: [Search] Null handling error in dietary restrictions filter
Severity: CRITICAL
Root Cause: Line 116 assumes dietary_restrictions is a list, 
            but can be None for users with no preferences set
Affected Files: 
  - search.py:116 (primary failure point)
  - models.py (User model allows None)
  - api/routes.py (calls filter function)

Impact: ~30% of searches (users without dietary preferences)
Users see: 500 Internal Server Error

Immediate Fix: Add null check before line 116
  if user.dietary_restrictions is None:
      user.dietary_restrictions = []

Long-term Fix: Refactor search architecture
  - Add input validation layer
  - Use Pydantic models (defaults to empty list)
  - Separate concerns (parsing vs filtering)

Labels: bug, critical, production, search
Estimated Time: 10 min hotfix, 2-4 hours for proper refactor
```

### What Just Happened
Your agent **autonomously analyzed** the error, traced it across 3 files, estimated impact, and proposed both quick and proper fixes.

---

## 📝 Exercise 1.3: Auto-Create GitHub Issue with Skill Composition (6 min)

### Task
Use **skill composition**: Combine your custom @issue-analyzer with official @github-issues skill.

### Concept: Skill Composition
Instead of one skill doing everything, compose specialized skills:
- **@issue-analyzer** → Analyzes errors (your custom skill)
- **@github-issues** → Creates issues (GitHub's official skill)

This is the **professional pattern** for reusable agent capabilities.

### Steps

**1.3.1** The GitHub Issues skill is already provided in `.github/skills/github-issues/SKILL.md`

Verify it exists:
```bash
ls .github/skills/
# You should see: issue-analyzer/ and github-issues/
```

💡 **Note:** This skill is based on [GitHub's official skills library](https://github.com/github/awesome-copilot/tree/main/skills/github-issues) and provides best practices for issue formatting.

**1.3.2** Reload VS Code window (Ctrl+Shift+P → "Developer: Reload Window")

**1.3.3** In Copilot Chat, compose both skills to create the issue:
```
Create a GitHub issue based on the #file:issue-analyzer analysis from the previous conversation. Use #file:github-issues format.

Repository: recipe-manager
Labels: bug, critical, production, search
```

💡 **What's happening:**
- `#file:issue-analyzer` - References the previous analysis in chat history
- `#file:github-issues` - Applies the issue formatting skill
- Copilot creates the issue and provides the link directly

**Expected:** Copilot generates the issue content and creates it in your repository, returning a link like:
```
✅ Created issue #1: https://github.com/Hemavathi15sg/recipe-manager/issues/1
```

**1.3.4** Quality Review (Optional but Recommended):

AI-generated outputs are powerful but may need light refinement. Review the issue for:

✅ **Title Length** - Should be <80 characters for readability
```
❌ Too long: "[Bug] NoneType iteration error crashes search for users without dietary restrictions #1"
✅ Better: "[Search] NoneType error in dietary filter"
```

✅ **Line Number Accuracy** - Verify references match actual code
```
Open search.py and confirm line 116 has the bug
AI sometimes counts blank lines differently
```

✅ **Code References** - Check all mentioned code exists
```
Search for mentioned functions/variables in your codebase
Remove hallucinated code that doesn't exist
```

✅ **Formatting Artifacts** - Remove extra characters
```
❌ Title ending with "#1" or duplicate numbers
❌ Incomplete code blocks or broken markdown
```

**To Refine the Output:**
```
The issue looks great! Please refine it:
1. Shorten title to "<80 chars focusing on component and error type"
2. Remove any "#1" artifacts from the title
3. Verify line numbers against actual search.py file
4. Make description more concise (2-3 sentences max in Problem section)
```

💡 **Key Insight:** Agents are **powerful assistants**, not perfect replacements. A 30-second review ensures production quality while still saving 15+ minutes vs manual creation.

**1.3.5** Assign the issue to Copilot workspace agent:
```
#issue_read 1 and Use #assign_copilot_to_issue to assign the Copilot agent for automated analysis
```

💡 **What happens next:**
- Copilot will analyze the issue and create **PR #2** with a quick fix (null check)
- The fix is **technically correct** - solves the immediate crash
- But should we merge it, or investigate deeper first?

**Expected Output:**
```
✅ Successfully assigned issue #1 to @copilot
Copilot will analyze and provide suggestions shortly
```

---

### Example Output

Here's what the skill composition generates (your output will be similar):

**GitHub Issue Created:**

**Title:** [Search] NoneType iteration error crashes search for users without dietary restrictions

**Labels:** bug, critical, production, search

**Problem:**  
TypeError occurs when users without dietary restrictions attempt to search recipes.

**Root Cause:**  
Line 116 in search.py attempts to iterate over `user.dietary_restrictions` without checking if it's None.

**Impact:**
- **Severity:** Critical
- **Affected Users:** ~30% (users without dietary preferences)  
- **Error Rate:** 547 errors in last 30 minutes
- **User Experience:** 500 Internal Server Error

**Affected Files:**
- `search.py:116` - Primary failure point
- `models.py` - User model allows None
- `api/routes.py` - Calls filter function

**Immediate Fix:** Add null check before iteration  
**Long-term Solution:** Add input validation layer to prevent similar issues

---

### What Just Happened

**Skill Composition Pattern:**
1. ✅ **@issue-analyzer** - Diagnosed the error (your custom skill)
2. ✅ **@github-issues** - Formatted and created issue (official skill)
3. ✅ **Two specialized agents** working together vs one do-everything skill
4. ✅ **Quality review** - Light refinement ensures production readiness
5. ✅ **Assigned to Copilot** - Baseline response without custom infrastructure

**Why This Matters:**
- Reusable across projects (both skills work independently)
- Each skill has single responsibility
- Official skills maintained by GitHub community
- Mix-and-match capabilities as needed
- **Realistic workflow**: Agents assist, humans refine (30 seconds vs 15+ minutes manual)
- **Baseline established**: You'll see Copilot's generic response now, then compare with enhanced agent infrastructure in Experiments 2-3

Time saved: 15 minutes of manual issue creation, with 30-second quality check.

---

## ✅ Checkpoint: What You Accomplished

**What We DID in Experiment 1:**
🎯 **Bug reproduced** with test_bug.py to get actual stack trace  
🎯 **Custom @issue-analyzer skill** created for autonomous error analysis  
🎯 **Official @github-issues skill** added from GitHub's skill library  
🎯 **Skill composition** demonstrated - two specialized skills working together  
🎯 **Quality review process** - Learned to refine AI outputs for production  
🎯 **NULL_DIETARY_BUG documented** with structured analysis and proper formatting  
🎯 **Assigned to Copilot** - Copilot created **PR #2** with quick fix  
🎯 **Crisis documented** in 20 minutes vs 1+ hour manually  

**What We Have Now:**
✅ GitHub issue #1 documenting the NULL_DIETARY_BUG  
✅ **PR #2 from Copilot** with null check (technically correct fix)  
❓ **Unanswered**: Should we merge the PR or investigate deeper?

**Agent Capabilities Demonstrated:**
- Custom Agent Skills: Build specialized analysis capabilities
- Official Skills Library: Leverage community-maintained skills
- Skill Composition: Combine skills for powerful workflows
- Reusable Patterns: Both skills work independently across projects
- **Realistic AI Workflow**: Agents assist, humans ensure quality (90% automation + 10% review)
- **GitHub MCP**: Programmatic GitHub operations (issue assignment)

**What Copilot Delivered:**
**PR #2** with null check fix - technically correct, will stop crashes immediately.

**Strategic Question:**
Before merging, should we understand if this is the right approach? Is search.py clean or a monolith where patches hide deeper problems?

**Current Time:** 3:20 PM  
**Status:** Crisis documented. PR ready. Should we merge it?

---

## 🚀 Next: [Experiment 2](experiment-2.md)

**Decision Point:**
- ✅ NULL_DIETARY_BUG documented as GitHub issue #1
- ✅ **PR #2** ready with null check fix
- ❓ Should we **merge PR #2** or **investigate deeper**?

**Next Step:** Create a **Custom Architect Agent** to review PR #2 and analyze if we need architectural refactor.

