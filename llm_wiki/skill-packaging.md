---
description: The trigger description, the examples and the length of a skill or reusable prompt. Read when you create or edit a skill, a slash command or an agent definition.
---

# Skills and reusable prompts

Rules for the parts around the instructions: the trigger description, the examples and the amount of text.

These rules apply to skills (`SKILL.md` with optional reference files), slash commands and agent definitions.

The body of a skill follows [instruction-wording.md](instruction-wording.md). If the body has steps, the steps follow [agent-procedures.md](agent-procedures.md). The rule to [give the criterion, not a vague word](instruction-wording.md#give-the-criterion-not-a-vague-word) does not apply to the description, because the description must hold the user's informal words. The checklist is in [instruction-review.md](instruction-review.md).

## Write the description in the user's words

Write the `description` in two parts: what the skill does, then `Use when ...` with the situations. Put the words that a user types into the description, with synonyms and informal terms.

Reason: the model selects a skill by matching the user's words against the description. A description in internal terms does not match, and the skill does not trigger.

Incorrect:

```yaml
description: Component helper.
```

Correct:

```yaml
description: Creates an Angular standalone component with its test file, following the project conventions. Use when the user asks to add, make, scaffold or generate a component, widget, view or UI element.
```

## Name the neighbor skill

If another skill does a similar task, add `Do not use for ...` and name the other skill.

Reason: without that sentence, the model can select the wrong one of two similar skills.

Incorrect:

```yaml
description: Reviews component styles. Use when the user asks about styling.
```

Correct:

```yaml
description: Reviews the stylesheets of existing components against the design tokens. Use when the user asks to review, audit or check styles, CSS or theming. Do not use for creating a component; use create-component for that.
```

## Give 2 different examples if the model must generalize

Give at least 1 example for each output format. If the model must generalize from the examples, give at least 2 that differ in content, length and structure.

Reason: a model copies a single example too closely, including its length, its topic and its wording.

Incorrect:

````markdown
Write the commit subject like this:

```
fix(auth): reject expired refresh tokens
```
````

Correct:

````markdown
Write the commit subject as `<type>(<scope>): <summary>`. Examples:

```
fix(auth): reject expired refresh tokens
docs: add the stow layout to the README
feat(billing)!: remove the v1 invoice endpoint
```
````

## Label a negative example and correct it

Label a negative example `Incorrect:`, give the reason, and put the correct version immediately after.

Reason: a model can copy an unlabeled negative example as if the example were the target.

Incorrect:

```markdown
Some people write `catch (e) {}`.
```

Correct:

````markdown
Incorrect, because the error is lost:

```ts
catch (e) {}
```

Correct:

```ts
catch (e) { logger.error(e); throw e; }
```
````

## Leave out what the model already knows

Write the project-specific facts, the decisions and the differences from common practice. Do not explain general knowledge.

Reason: the text shares the context window with the task. General knowledge uses context and gives the model no information.

Incorrect:

```markdown
Git is a version control system. A commit records a snapshot of the files.
Write good commit messages so that other developers understand the history.
```

Correct:

```markdown
Commit subjects follow Conventional Commits. The scope is the module folder
name under `src/modules/`.
```

## Write each rule once, with no filler

Write a rule once, in the section where it applies. Remove introductions, summaries and motivational text.

Reason: a repeated rule gets extra weight that the writer did not intend, and two copies become different over time.

Incorrect:

```markdown
## Overview
This skill helps you write great tests. Remember to always use Vitest.

## Rules
- Use Vitest.

## Summary
In short: use Vitest for all tests.
```

Correct:

```markdown
## Rules
- Write tests with Vitest. The project has no Jest configuration.
```

## Move long material to reference files, 1 level deep

Keep `SKILL.md` under 500 lines. Move schemas, API lists and long examples to `references/<topic>.md`. Link each reference file from `SKILL.md` with a sentence that says when to read it. Do not link from one reference file to another.

Reason: the model loads a reference file only when the task needs it, so the main file stays short. The model can stop before it follows a second link.

Incorrect:

```markdown
## References
- `references/overview.md` (links to the other reference files)
```

Correct:

```markdown
## References
- `references/schema.md`: read before you write a migration.
- `references/errors.md`: read if the build fails with an error code.
```
