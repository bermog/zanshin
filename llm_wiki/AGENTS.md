# LLM wiki schema

This file is the schema of the wiki in this directory. It states what the wiki holds, how a page is written, and how to ingest a source, answer a question and check the wiki. Read it before you change a wiki file.

The wiki is the project's source of truth for agents. Knowledge goes into the wiki once and is kept current there, so that an agent does not derive it again from the sources for each task.

## Layers

| Layer | Location | Who writes it | Rule for a wiki operation |
|-------|----------|---------------|---------------------------|
| Sources | `raw/`, and the project's own files (code, configuration, documentation) | The user and the developers | Read only. Never edit, move or delete a source, because the wiki cites the sources as evidence. |
| Wiki | The pages in this directory, `index.md` and `log.md` | Agents | Create and update pages by the workflows below. The user reads the pages and can correct them. |
| Schema | This file | The user and agents together | Edit it only if the user asks or approves, because a change here alters each later operation. |

## Files

- `index.md`: the catalog. 1 line for each page, in groups. Read it first for each task.
- `log.md`: the record of each operation, oldest first. Append only.
- `raw/`: documents that the user adds as sources, for example specifications, meeting notes, exported tickets and articles. Images go in `raw/assets/`.
- All other `.md` files: the pages.

## Pages

- A page covers 1 subject. The file name is the subject in lowercase words joined with hyphens, with the extension `.md`.
- A page starts with frontmatter that holds its description on 1 line:

  ```yaml
  ---
  description: <what the page covers>. Read when <the task that needs it>.
  ---
  ```

- The line of a page in `index.md` is a copy of its `description`. If you edit one, edit the other in the same change, because the index is the only way an agent finds a page.
- Link to another page where a statement depends on it. Use a relative link, with the heading anchor if the target is 1 section: `[file organization](architecture.md#file-organization)`. When you reword a heading, find the links to its old anchor with `grep -rn '#<old-anchor>' llm_wiki/` and edit them in the same change.
- A page that holds statements from sources ends with a section `## Sources`: 1 line for each source, with its path from the project root and the date of the ingest.
- A section that holds only a `<!-- fill in: ... -->` comment has no content yet. Do not take a fact from that section. Read the sources or ask the user instead. When you write the content, remove the comment.
- If a source contradicts a statement on a page, keep both statements under a line that starts with `Conflict:` and name the source of each. Do not select one, because the user decides which statement is true.

Groups in `index.md`:

| Group | Pages |
|-------|-------|
| `## Project` | Subject pages about the project: `product.md`, `technology.md`, `architecture.md` and each page that an ingest or a query adds. |
| `## Sources` | 1 summary page for each ingested document in `raw/`, with the name `source-<document name>.md`. |
| `## Agent instructions` | The rules for text that a model reads as instructions. |

If a new page fits no group, add a group heading (`## <Subject>`) to `index.md` and a row to this table.

## Ingest

Use this workflow to file 1 source into the wiki. A source is a document in `raw/` or a file or folder of the project.

1. Read the source to its end.
2. Read `index.md`, then each page whose description matches a subject of the source.
3. If the source is a document in `raw/`, write its summary page `source-<document name>.md`: what the document is, its date, a maximum of 10 statements that matter for the project, and a link to each page that step 4 changes. If the summary page exists, replace its content. If the source is a file or folder of the project, write no summary page.
4. Update each page that the source changes:
   - Replace a `<!-- fill in: ... -->` comment with content if the source gives the content.
   - Add each statement that the page does not have.
   - Correct each statement that the source shows to be out of date, if the source is a file of the project. For a document in `raw/`, use a `Conflict:` line.
   - Add the source to `## Sources`.
5. If the source gives 3 or more statements about a subject that has no page, create a page for the subject.
6. Add a link between 2 changed pages where a statement of one depends on the other.
7. In `index.md`, add a line for each new page and edit the line of each page whose description changed.
8. Run the index check. For each line of output, edit `index.md` and run the check again. Maximum 2 attempts.
9. Append an entry to `log.md` with the operation `ingest` and the path of the source from the project root as the title. List each created and each updated page.
10. Report the created pages, the updated pages, a maximum of 5 statements that the source added, and each `Conflict:` line. Done state: the index check prints nothing and `log.md` has the entry.

Ingest 1 source at a time, because an ingest changes the pages that the next ingest reads.

## Query

Use this workflow to answer a question about the project.

1. Read `index.md`, then each page whose description matches the question.
2. If the pages do not answer the question, read the sources.
3. Answer, and name each page and each source that the answer uses.
4. If step 2 was necessary, or if the answer joins statements from 2 or more pages into a comparison or a conclusion, file the answer in the wiki. Add it to the page of its subject, or create a page if no page fits. Then do steps 7 to 9 of the ingest workflow, with the operation `query` and the question as the title. Reason: an answer that stays in a conversation is lost for the next agent.
5. If 1 page answered the question, change no file.

## Lint

Use this workflow to check the wiki. Do each check, then the action in its row.

| Check | Action |
|-------|--------|
| The index check prints a line. | Edit `index.md` or the page description. |
| A link points to a page or an anchor that does not exist. | Correct the link if 1 target fits. If not, report the link. |
| A page has no link from another page, apart from `index.md`. | Add a link from the page whose subject is nearest. If no page fits, report the page. |
| A page has a `Conflict:` line, or 2 pages make statements that cannot both be true. | Report both statements with their sources. Do not select one. |
| A statement names a project file as its source, and the file changed or does not exist. | Correct the statement from the file. If the file does not exist, report the statement. |
| A section holds only a `<!-- fill in: ... -->` comment. | Report the page and the section. |
| 3 or more pages mention a subject that has no page. | Report the subject as a suggested page. |
| A file in `raw/` is in no `ingest` entry of `log.md`. | Report the file as not ingested. |

After the checks, append an entry to `log.md` with the operation `lint` and the title `<number> fixed, <number> reported`. List each fix.

## Log

Append each entry at the end of `log.md` in this format. Get the date with `date +%F`.

```markdown
## [<YYYY-MM-DD>] <operation> | <title>

- <created, updated or fixed>: <page>
```

- The operation is `create`, `ingest`, `query` or `lint`.
- Do not edit or delete an earlier entry. If an entry is wrong, append a new entry that corrects it, because the log is the record of how the wiki changed.
- To read the last 5 entries, run `grep "^## \[" llm_wiki/log.md | tail -5`.

Example:

```markdown
## [2026-11-13] ingest | llm_wiki/raw/payment-provider-contract.md

- created: source-payment-provider-contract.md
- updated: technology.md
- updated: architecture.md
```

## Index check

Run this script from the project root after each change to a page or to `index.md`. No output means that the index matches the pages.

```sh
for f in llm_wiki/*.md; do
  case "$(basename "$f")" in AGENTS.md|index.md|log.md) continue;; esac
  d=$(sed -n 's/^description: //p' "$f" | head -1)
  if [ -z "$d" ]; then echo "no description: $f"; continue; fi
  grep -qF "$(basename "$f")): $d" llm_wiki/index.md || echo "index out of date: $f"
done
```
