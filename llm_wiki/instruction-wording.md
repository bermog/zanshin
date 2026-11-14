---
description: How to word a single rule or constraint for a model. Read when you write or edit any prompt, skill or agent instruction.
---

# Wording of instructions

Rules for a single instruction or constraint in a prompt, a skill, an `AGENTS.md` or a `CLAUDE.md`.

Each rule gives the missing information that a model would otherwise invent.

Instructions that form steps also follow [agent-procedures.md](agent-procedures.md). Descriptions and examples follow [skill-packaging.md](skill-packaging.md). The checklist is in [instruction-review.md](instruction-review.md).

## Give the criterion, not a vague word

Replace each word in the table with the information it stands for. The words are permitted in text that is not an instruction.

Reason: a word with no criterion makes the model supply its own criterion, and that criterion changes from run to run.

| Word in an instruction | What is missing | Write instead |
|------------------------|-----------------|---------------|
| ensure, make sure | If the agent must check or must change | `check` with an action for failure, or `edit` |
| handle, deal with, take care of | The action | The specific action |
| consider | If the agent must do it | A rule with a condition, or `You may` |
| should | If the rule is required | A [strength word](#show-strength-with-fixed-words-not-with-emphasis) |
| try | The action for failure | The action, then the [failure action](agent-procedures.md#give-a-failure-action-for-each-step-that-can-fail) |
| appropriate, proper, correct, relevant, reasonable | The criterion | The criterion |
| as needed, if necessary, where possible | The condition | The condition |
| some, several, a few, many, various | The number | A number or a range |
| best practices, as usual, the standard way | The content | The practice, or a link to it |
| etc., and so on | The rest of the list | The full list, or `for example` before a partial list |

Incorrect:

```markdown
Make sure components are styled properly using the design system. Consider
using tokens where possible, and handle any edge cases appropriately.
```

Correct:

```markdown
- In a component stylesheet, use only design tokens (`var(--token-*)`) for
  colors. Reason: the theme changes the token values, not the stylesheets.
- If no token fits, ask the user. Do not add a literal color value.
```

## Give a reason for each rule that is not obvious

Write the reason in one sentence that starts with `because` or `Reason:`. Give a reason for every `Always` and `Never`.

Reason: the reason lets the model find the right behavior in cases that the text does not list, and shows the model where the rule does not apply.

Incorrect:

```markdown
Never use npm.
```

Correct:

```markdown
Never run `npm` or `yarn`. Use `pnpm` instead, because the lockfile is
`pnpm-lock.yaml` and other package managers change it.
```

## Show strength with fixed words, not with emphasis

Use only the wordings in the table. Do not add strength with capitals, bold text, exclamation marks or words such as `CRITICAL` and `IMPORTANT`.

Reason: a model applies an emphasized rule too widely and ahead of rules that matter more. If many rules are emphasized, none has priority.

| Wording | Meaning |
|---------|---------|
| `Always <X>` / `Never <X>` | An invariant. The only exceptions are the ones written in the rule. |
| `<X>.` (bare imperative) | A required step or rule. |
| `Prefer <X> to <Y>.` | A default. The rule says when `<Y>` is permitted. |
| `You may <X>.` | An option. |

Incorrect:

```markdown
IMPORTANT: you MUST ALWAYS run the linter after changes!!
```

Correct:

```markdown
Run `npm run lint` after each edit to a `.ts` file.
```

## Keep `Always` and `Never` for severe failures

Use `Always` and `Never` only if one violation causes data loss, a security problem, an external action that cannot be undone, or a broken build. For other rules, use a bare imperative or `Prefer`.

Reason: the two words keep their force only if they are rare.

Incorrect:

```markdown
Always use descriptive variable names. Never write long functions.
```

Correct:

```markdown
Prefer functions under 40 lines. A longer function is permitted if a split
would divide a single database transaction.

Never run `git push --force` on `main`. Use `--force-with-lease` on a feature
branch instead, because a force push on `main` deletes commits of other people.
```

## Give the alternative with each prohibition

Say what to do. If a prohibition is necessary, put the alternative in the same sentence or the next sentence.

Reason: a model that is told only what not to do still has to do something, and the improvised action can be worse than the prohibited one.

Incorrect:

```markdown
Do not mock the database.
```

Correct:

```markdown
Do not mock the database in integration tests. Run the tests against the
Testcontainers PostgreSQL instance instead, because mocks hide migration errors.
```

## Give a default for each input that can be missing

For each input that the user can omit, write a default value or the instruction `ask the user`.

Reason: without a default, the model guesses a value and continues without telling the user.

Incorrect:

```markdown
Open the pull request against the target branch.
```

Correct:

```markdown
If the user gives a target branch, open the pull request against that branch.
If not, use `main`.
```

## State the priority where rules can conflict

If two rules can conflict, say which rule has priority. If a user instruction can conflict with a rule, say which of the two has priority.

Reason: without a stated priority, the model picks one rule, and the pick can change between runs.

Incorrect:

```markdown
- Keep functions under 40 lines.
- Change only the code that the task names.
```

Correct:

```markdown
- Prefer functions under 40 lines.
- Change only the code that the task names. This rule has priority over the
  40-line rule: leave a long function outside the task as it is and report it.
- If a user instruction conflicts with a rule in this file, follow the user
  instruction, except for a rule that starts with `Never`.
```
