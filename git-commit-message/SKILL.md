---
name: git-commit-message
description: >
  Use this skill whenever the user wants to write, generate, validate, review, or get feedback on a Git commit message.
  Trigger when the user shares file diffs or code changes and asks for a commit message, when they paste an existing commit
  message for review, when they ask if their commit message is good/correct/follows conventions, or when they mention
  "commit message", "git commit", or want to check if their message follows best practices. Also trigger when a user
  asks to "write a commit for these changes" or "help me commit this". Use this skill even if the user just says
  something like "does this commit look right?" or shares a git diff.
---

# Git Commit Message

This skill enables you to:
1. **Generate** commit messages from file diffs or described changes
2. **Validate** commit messages against the 7 rules
3. **Give structured feedback** on what's good and what needs improvement
4. **Suggest improvements** for any failing sections

---

## The 7 Rules of a Great Git Commit Message

Every commit message must be evaluated against these rules:

| # | Rule | Details |
|---|------|---------|
| 1 | **Separate subject from body with a blank line** | The blank line is critical — tools like `log`, `shortlog`, and `rebase` break without it |
| 2 | **Limit the subject line to 50 characters** | 50 is the target; 72 is the hard maximum (GitHub truncates beyond 72 with an ellipsis) |
| 3 | **Capitalize the subject line** | First word must start with a capital letter |
| 4 | **Do not end the subject line with a period** | No trailing punctuation — space is precious |
| 5 | **Use the imperative mood in the subject line** | "Fix bug" not "Fixed bug" or "Fixing bug". Test: "If applied, this commit will _____" |
| 6 | **Wrap the body at 72 characters** | Git never wraps text automatically — do it manually |
| 7 | **Use the body to explain *what* and *why*, not *how*** | The code explains how; the message should explain the reasoning and context |

---

## Imperative Mood Quick Test

A subject line passes if it completes this sentence naturally:
> **"If applied, this commit will _______________"**

✅ Good: "If applied, this commit will **fix login timeout for inactive sessions**"  
❌ Bad: "If applied, this commit will **fixed login timeout**" (past tense)  
❌ Bad: "If applied, this commit will **fixing login timeout**" (present participle)

---

## Ideal Commit Message Structure

```
Short imperative summary in ~50 chars (72 max)
<blank line>
More detailed explanation of WHY the change was made. Wrap lines at
72 characters. Focus on the problem being solved, not the solution.
Are there side effects or unintuitive consequences? Explain them here.

Further paragraphs come after blank lines.

 - Bullet points are fine
 - Use a hyphen or asterisk, preceded by a single space

Resolves: #123
See also: #456, #789
```

---

## Task: Generating a Commit Message

When given a diff or description of changes:

1. Identify the primary intent of the change (fix, feature, refactor, docs, test, chore, etc.)
2. Write a subject line in imperative mood, ≤50 characters, capitalized, no trailing period
3. If the change needs context, write a body explaining:
    - What problem does this solve?
    - Why was this approach chosen?
    - Any side effects or notable consequences?
4. Wrap body lines at 72 characters
5. Add issue tracker references at the bottom if mentioned

**Output format:** Present the commit message in a code block, then briefly explain any choices made.

---

## Task: Validating a Commit Message

When given a commit message to validate, check each rule and produce a structured report:

### Validation Report Format

For each of the 7 rules, report:
- ✅ **PASS** — explain briefly why it passes
- ❌ **FAIL** — explain what's wrong and provide a concrete suggested fix
- ⚠️ **WARNING** — technically valid but could be improved (e.g., subject is 58 chars — within limit but above 50 target)

**End the report with:**
- An **overall verdict**: PASS (all rules met) or FAIL (one or more rules violated)
- A **suggested improved version** of the commit message if any rules failed

### Validation Checklist (run through in order)

**Rule 1 — Blank line between subject and body:**
- If there's a body, is there exactly one blank line separating subject from body?
- If there's no body, this rule is N/A ✅

**Rule 2 — Subject line length:**
- Count characters in the subject line only
- ≤50: ✅ PASS
- 51–72: ⚠️ WARNING (over target, under hard limit)
- >72: ❌ FAIL

**Rule 3 — Capitalization:**
- Does the subject line start with an uppercase letter?

**Rule 4 — No trailing period:**
- Does the subject line end with a period (`.`)? If so, ❌ FAIL.

**Rule 5 — Imperative mood:**
- Apply the "If applied, this commit will ___" test
- Common failures: past tense (-ed endings), present participle (-ing endings), noun phrases

**Rule 6 — Body line wrapping:**
- If a body is present, are all lines ≤72 characters?
- Check every line individually

**Rule 7 — Body content (what/why, not how):**
- Does the body (if present) focus on the reason for the change and its context?
- If it describes implementation details that are obvious from the code, flag it

---

## Task: Providing Feedback and Suggestions

When giving feedback:

1. **Be specific** — don't just say "use imperative mood"; show the exact fix
2. **Explain the why** — briefly note why each rule matters
3. **Prioritize** — if multiple issues exist, lead with the most impactful ones
4. **Always provide a corrected version** if any rule fails

### Suggestion Templates

| Issue | Example Suggestion |
|-------|--------------------|
| Past tense subject | Change "Fixed null pointer in auth" → "Fix null pointer in auth" |
| Period at end | Remove trailing period: "Update README." → "Update README" |
| Subject too long | Condense: "Add validation for email field in the user registration form" (60 chars) → "Add email validation to user registration" (41 chars) |
| Missing blank line | Insert a blank line between subject and first body line |
| Body explains how | Replace implementation details with problem context and rationale |
| No capitalization | Capitalize first word: "add retry logic" → "Add retry logic" |

---

## Examples of Good vs. Bad Commit Messages

### ✅ Excellent
```
Fix null pointer in session token refresh

Session tokens with missing expiry fields caused a NullPointerException
on refresh. This happened because the token parser assumed all fields
were present, which was valid under the old auth service but not the
new one deployed in v2.4.

Resolves: #891
```

### ❌ Poor (multiple violations)
```
fixed the bug where tokens with no expiry crash the app and also updated some tests and refactored the token parser to be cleaner.
```

Issues: past tense, too long, no body separation, explains "what" without "why", includes unrelated changes in one commit.

---

## Notes on Single-Line Commits

Simple, self-explanatory changes don't need a body:
```
Fix typo in README
```
This is perfectly valid. Not every commit needs explanation — use judgment based on how obvious the change is from the diff alone.