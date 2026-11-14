---
description: Steps that an agent runs with tools, with retry limits, done states and failure actions, and when to use a goal description instead. Read when instruction text has steps or covers a multi-step task.
---

# Procedures

Rules for multi-step instructions that an agent runs with tools.

The two common failures of an agent in a procedure are a loop with no end and a stop before the task is complete. Each rule below removes one cause.

Each step and each constraint also follows the wording rules in [instruction-wording.md](instruction-wording.md). The checklist is in [instruction-review.md](instruction-review.md).

## When to write a procedure

Write numbered steps if the task is mechanical and a wrong order or a skipped step breaks something: a release, a migration, a commit workflow, a scaffold.

For a task that depends on judgment (a review, a design, a piece of writing), state the goal, the constraints and the output. Do not force numbered steps on that task.

Reason: fixed steps make a capable model follow the script in cases where the script does not fit.

Procedure style, for a mechanical task:

```markdown
1. Run `git fetch origin`.
2. Create the branch `release/<version>` from `origin/main`.
3. Run `./scripts/bump-version.sh <version>`.
```

Goal style, for a judgment task:

```markdown
Review the diff for bugs that a user would hit. Report at most 5 findings,
most severe first. For each finding, give the file, the line, and the input
that triggers the bug. Leave style comments out, because the linter covers style.
```

## Give each loop an exit condition and a maximum

For each loop or retry, write the condition that ends it and the maximum number of attempts.

Set the maximum from the cost of each attempt. A maximum that is too low makes the agent stop on a problem that one more attempt would solve.

Reason: without a limit, an agent can repeat a failing step until the context is full.

Incorrect:

```markdown
5. Run the tests and fix any failures until everything passes.
```

Correct:

```markdown
5. Run `npm test`.
6. If a test fails, edit the code and go to step 5. Maximum 3 attempts.
7. If a test still fails, stop and report the failing test names and output.
```

## End with the done state

In the last step, write what is true when the procedure is complete and what to report to the user.

Reason: without a done state, the agent decides for itself when the work is finished, and it can stop early or continue into work that nobody asked for.

Incorrect:

```markdown
6. Commit the changes.
```

Correct:

```markdown
6. Run `git commit` with the message from step 4.
7. Run `git status`. Done state: the working tree is clean and `npm test`
   passes. Report the commit hash and the number of changed files.
```

## Give a failure action for each step that can fail

For each step that can fail, write one of these actions: retry with a maximum, ask the user, or stop and report. Put the actions in a section `## If something fails`.

Reason: without a failure action, the agent finds a workaround without telling the user, for example it deletes a failing test or skips a check.

If a failure action forbids something, [give the alternative](instruction-wording.md#give-the-alternative-with-each-prohibition).

Incorrect:

```markdown
3. Run `./gradlew build`.
4. Open the pull request.
```

Correct:

```markdown
3. Run `./gradlew build`.
4. Open the pull request.

## If something fails

- `./gradlew build` fails with a compile error: edit the code and run step 3
  again. Maximum 3 attempts, then stop and report the error output.
- `./gradlew build` fails with a network error: stop and report the error
  output. Leave the build files as they are.
- The pull request command fails: report the error and the branch name, and
  ask the user how to continue.
```
