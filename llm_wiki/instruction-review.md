---
description: The review checklist for agent instructions and the cases where the rules matter most. Read when you review a prompt, a skill or other instruction text.
---

# Review of agent instructions

The checklist for text that a model reads as instructions, and the cases where the rules matter most.

The rules are on 3 pages: [instruction-wording.md](instruction-wording.md), [agent-procedures.md](agent-procedures.md) and [skill-packaging.md](skill-packaging.md). Each rule makes the writer supply information that the model would otherwise invent: a criterion, a reason, a default, a priority, a limit or an end state.

## Checklist

Each line links to the rule, its reason and an example.

Wording:

- [ ] [No instruction depends on a vague word.](instruction-wording.md#give-the-criterion-not-a-vague-word) Each has its criterion, condition or number.
- [ ] [Each `Always`, `Never` and non-obvious rule has a reason.](instruction-wording.md#give-a-reason-for-each-rule-that-is-not-obvious)
- [ ] [Strength comes from the fixed wordings only.](instruction-wording.md#show-strength-with-fixed-words-not-with-emphasis) No capitals, bold or `CRITICAL` for emphasis.
- [ ] [`Always` and `Never` appear only on rules for severe failures.](instruction-wording.md#keep-always-and-never-for-severe-failures)
- [ ] [Each prohibition has an alternative.](instruction-wording.md#give-the-alternative-with-each-prohibition)
- [ ] [Each input that can be missing has a default or `ask the user`.](instruction-wording.md#give-a-default-for-each-input-that-can-be-missing)
- [ ] [Rules that can conflict have a stated priority](instruction-wording.md#state-the-priority-where-rules-can-conflict), also against user instructions.

Procedures:

- [ ] [Numbered steps are used for a mechanical task, a goal description for a judgment task.](agent-procedures.md#when-to-write-a-procedure)
- [ ] [Each loop has an exit condition and a maximum.](agent-procedures.md#give-each-loop-an-exit-condition-and-a-maximum)
- [ ] [The last step gives the done state and what to report.](agent-procedures.md#end-with-the-done-state)
- [ ] [Each step that can fail has a failure action.](agent-procedures.md#give-a-failure-action-for-each-step-that-can-fail)

Packaging:

- [ ] [The description says what the skill does and `Use when`, in the user's words.](skill-packaging.md#write-the-description-in-the-users-words)
- [ ] [The description names a neighbor skill if one exists.](skill-packaging.md#name-the-neighbor-skill)
- [ ] [Examples are varied if the model must generalize.](skill-packaging.md#give-2-different-examples-if-the-model-must-generalize)
- [ ] [Negative examples are labeled and corrected.](skill-packaging.md#label-a-negative-example-and-correct-it)
- [ ] [No general knowledge.](skill-packaging.md#leave-out-what-the-model-already-knows)
- [ ] [No repeated rule or filler text.](skill-packaging.md#write-each-rule-once-with-no-filler)
- [ ] [The main file is under 500 lines, and reference files are linked from the main file only.](skill-packaging.md#move-long-material-to-reference-files-1-level-deep)

## Where the rules matter most

- Largest effect: procedural tasks where a wrong step breaks something, and text that a smaller model or a subagent reads.
- Smallest effect: a one-time prompt in a conversation, where the user sees the result and corrects it.
- For a task that depends on judgment, use the [goal style](agent-procedures.md#when-to-write-a-procedure). The wording rules still apply to its constraints.

The rules come from reasoning about model behavior, not from measurements. If a rule makes a skill worse in a test, the test result has priority.
