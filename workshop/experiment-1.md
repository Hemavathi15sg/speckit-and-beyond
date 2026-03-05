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

**1.0.4** Open `recipe-manager/search.py` and locate line 447 to see the problematic code:
```python
for restriction in user.dietary_restrictions:  # ← CRASHES IF None!
```

### What You Verified
- ✅ **Bug is real** - Reproducible with test script
- ✅ **Stack trace available** - You now have the error details for analysis
- ✅ **Root cause visible** - Line 447 iterates over None value
- ✅ **Context understood** - Affects users without dietary restrictions (30% of searches)

**💡 Now you have the stack trace and context to give your agent skill!**

---

## 📝 Exercise 1.1: Create Issue Analyzer Skill (6 min)

### Task
Build an agent skill that can analyze production errors intelligently.

### Steps

**1.1.1** Create the skill using Copilot Chat UI:

1. Open **GitHub Copilot Chat** 

2. Click the **⚙️ Configure** button (top-right of chat panel)

3. Select **Skills** from the menu

   ![Configure Menu - Select Skills](assets/skills.png)
   *The Configure menu with Skills option highlighted*

4. Click **➕ New Skill** button

   ![New Skill Button](assets/newskill.png)
   *Select "New skill..." to create a custom skill*

5. In the file dialog, select location:

   ![Select Location Dialog](assets/githubskills.png)
   *Choose .github\skills as the location for your skill*
   - Enter skill name: `issue-analyzer`
   - Click Save

**Result:** VS Code creates `.github/skills/issue-analyzer/SKILL.md` automatically

---

**1.1.2** Edit the generated `SKILL.md` file:

Replace the template content with:

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


### What You Created
An agent that thinks like a senior engineer - not just reading errors, but **analyzing root causes and impacts**.

---

## 📝 Exercise 1.2: Invoke the Skill (3 min)

### Task
Use your skill to analyze the production error.

### Steps

**1.2.1** In the terminal where you ran `python test_bug.py`, select the error output (the crash section with stack trace)

**1.2.2** Open Copilot Chat and invoke your skill:
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
Root Cause: Line 447 assumes dietary_restrictions is a list, 
            but can be None for users with no preferences set
Affected Files: 
  - search.py:447 (primary failure point)
  - models.py (User model allows None)
  - api/routes.py (calls filter function)

Impact: ~30% of searches (users without dietary preferences)
Users see: 500 Internal Server Error

Immediate Fix: Add null check before line 447
  if user.dietary_restrictions is None:
      user.dietary_restrictions = []

Long-term Fix: Refactor search architecture
  - Add input validation layer
  - Use Pydantic models (defaults to empty list)
  - Separate concerns (parsing vs filtering)

```

### What Just Happened
Your agent **autonomously analyzed** the error, traced it across 3 files, estimated impact, and proposed both quick and proper fixes. Let's assign the Copilot coding agent for the immediate fix and investigate deeper architectural issues for the long-term fix.

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

**1.3.1** Add the GitHub Issues skill from the community library:

GitHub maintains a curated collection of reusable skills. Let's add the official `github-issues` skill:

1. Visit [GitHub's Awesome Copilot Skills Library](https://github.com/github/awesome-copilot/tree/main/skills)

   ![Awesome Copilot Skills](assets/awesomeskills.png)
   *GitHub's official skills library with community-contributed skills*

2. Create the `github-issues` skill folder structure:
   ```bash
   mkdir -p .github/skills/github-issues/references
   ```

3. Copy the official SKILL.md from GitHub:
   - Navigate to the `github-issues` skill in the [Awesome Copilot Skills repository](https://github.com/github/awesome-copilot/blob/main/skills/github-issues/SKILL.md)
   - Click **Raw** button to view the raw markdown
   - Copy the entire content
   - Paste into your `.github/skills/github-issues/SKILL.md` file
   - Save the file

4. Copy the reference template:
   - Navigate to [issue-template.md](https://github.com/github/awesome-copilot/blob/main/skills/github-issues/references/issue-template.md) in the same repository
   - Click **Raw** button
   - Copy the entire content
   - Paste into your `.github/skills/github-issues/references/issue-template.md` file
   - Save the file

5. Verify the skill structure:
   ```bash
   ls .github/skills/github-issues/
   # You should see: SKILL.md and references/
   ```

💡 **What You Created:**
- ✅ Official GitHub Issues skill for consistent formatting
- ✅ Reference folder with example templates
- ✅ Reusable pattern for future issues

**1.3.2** Reload VS Code window (Ctrl+Shift+P → "Developer: Reload Window")

**1.3.3** In Copilot Chat, create the issue and assign to Copilot:
```
Create a GitHub issue based on the #file:issue-analyzer analysis from the previous conversation. Use #file:github-issues format and use #mcp_github_assign_copilot_to_issue to fix the issue.

Repository: recipe-manager
Labels: bug, critical, production, search
```

💡 **What's happening:**
- `#file:issue-analyzer` - References the previous analysis in chat history
- `#file:github-issues` - Applies the issue formatting skill
- `#mcp_github_assign_copilot_to_issue` - Automatically assigns the created issue to @copilot agent
- Copilot creates the issue, assigns it to itself, and will create **PR #2** with a fix

![GitHub Issue Created](assets/issuecreated.png)
*Issue #1 created with proper formatting and automatically assigned to @copilot*

**Expected Output:**
```
✅ Created issue #1: https://github.com/Hemavathi15sg/recipe-manager/issues/1
✅ Successfully assigned issue #1 to @copilot
🔄 Copilot is analyzing the issue and will create a fix...
```

💡 **What happens next:**
- Copilot analyzes the issue and creates **PR #2** with a quick fix (null check)
- The fix is **technically correct** - solves the immediate crash
- But should we merge it, or investigate deeper first?

![Copilot PR Created](assets/copilotpr.png)
*PR #2 automatically created by @copilot with the null check fix*

---

## ✅ Checkpoint: What You Accomplished

**What We DID in Experiment 1:**
🎯 **Bug reproduced** with test_bug.py to get actual stack trace  
🎯 **Custom @issue-analyzer skill** created for autonomous error analysis  
🎯 **Official @github-issues skill** added from GitHub's skill library  
🎯 **Skill composition** demonstrated - two specialized skills working together  
🎯 **MCP automation** - Issue created and assigned to @copilot in single command   
🎯 **Copilot auto-assigned** - Will create **PR #2** automatically  
 

**What We Have Now:**

✅ **GitHub issue #1** documenting the NULL_DIETARY_BUG  
✅ **PR #2 from Copilot** with null check (technically correct fix)  
❓ **Unanswered**: Should we merge the PR or investigate deeper?


**Strategic Question:**
Lets merge, However should we check for deeper architectural issues? Is search.py clean or a monolith where patches hide problems?

**Current Time:** 3:20 PM  
**Status:** Crisis documented. PR ready. Need to check architecture first.

---

## 🚀 Next: [Experiment 2](experiment-2.md)

**Decision Point:**
- ✅ NULL_DIETARY_BUG documented as **GitHub issue #1**
- ✅ **PR #2** ready with null check fix
- **merge PR #2** but **investigate deeper** - Is search.py a monolith? Are there architectural issues hiding under the surface?

**Next Step:** Create a **Custom Architect Agent** to analyze search system architecture for deeper issues.

