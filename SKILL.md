---
name: git-commit-writer
description: Generates structured commit messages from staged Git changes using the Conventional Commits format. Use when Codex needs to write a commit message, summarize staged changes for version control, suggest what a commit should say, or describe a staged diff before running git commit. Triggers on requests like "write a commit message", "commit my changes", "summarize my staged diff", "what should my commit say", or similar Git commit wording.
---

# Git Commit Writer

Write precise, informative commit messages by analyzing staged changes and using the Conventional Commits format.

Use standard commit types such as `feat`, `fix`, and `docs`, but write the short description and body in Russian by default unless the user clearly asks for another language.

Original source: https://www.agensi.io/. This public copy keeps the same architecture and adapts the output style for Russian-by-default commit messages.

## Workflow

1. Check staged changes with `git diff --cached --stat`.

If nothing is staged:
- tell the user that there are no staged changes;
- check `git diff --stat` for unstaged work when relevant;
- if the user asked to commit changes, mention that staging is needed first;
- do not invent a commit message.

2. Analyze the staged diff with `git diff --cached`.

For large diffs, use `git diff --cached --stat` to find the main files and inspect the most important ones individually.

3. Determine the best commit type and scope.

If the type, scope, or intent is not perfectly clear:
- make the best reasonable assumption;
- provide the commit message anyway;
- note the assumption after the output.

If the staged diff spans unrelated concerns:
- still draft the best summary possible;
- warn that the changes may be better split into separate commits;
- name the separate concerns briefly.

4. If the user explicitly asked to run `git commit`, present the message first and ask for confirmation before creating the commit.

## Output Format

Output using this structure:

~~~
type(scope): short description in Russian

- optional body bullet in Russian
- optional body bullet in Russian

optional footer
~~~

Use body bullets only when the change is not trivial.

Use a footer only when it is relevant, such as:
- `BREAKING CHANGE: ...`
- `Closes #123`

## Conventional Commits Rules

Choose the most appropriate type:

- `feat` for a new feature;
- `fix` for a bug fix;
- `docs` for documentation-only changes;
- `style` for formatting-only changes with no behavior change;
- `refactor` for internal restructuring without new behavior;
- `test` for adding or updating tests;
- `perf` for performance improvements;
- `ci` for CI or automation pipeline changes;
- `chore` for tooling, build, config, dependency, or maintenance work.

Set `scope` to the main module, directory, component, or feature area.

Omit the scope if the change clearly affects the whole repository.

Write the short description:

- in imperative form;
- under 72 characters;
- without a trailing period;
- with a lowercase first letter after the colon;
- in Russian by default;
- specific to the actual change.

Prefer `add rate limiting to API login route` translated naturally into Russian over vague text like `update API`.

## Quality Rules

- never use vague summaries such as `updated stuff`, `misc fixes`, or `various improvements`;
- explain what changed and why, not low-level implementation details;
- keep one body bullet per logical change;
- check that the first line stays under 72 characters before outputting;
- if multiple unrelated areas changed, call that out explicitly;
- if the diff shows a breaking public change, add a `BREAKING CHANGE:` footer.

## Language Rules

- keep `type` and `scope` in their standard technical form;
- write the description and body in natural Russian;
- keep wording short, concrete, and readable;
- switch to English only if the user asks for it or the repository clearly uses English commit history.

## Example

Input: staged changes add rate limiting to an API endpoint.

~~~
feat(api): добавить ограничение запросов для query endpoint

- Ограничить запросы до 100 в минуту на один IP
- Возвращать 429 с подсказкой по повторной попытке
- Добавить настройку для нового поведения лимита

Closes #47
~~~
