# action-project-manager

This is a GitHub Action that can be used to manage issues in a project.

## Usage

```yaml
# .github/workflows/update-issue.yaml
name: Update issue

on:
  issues:
  pull_request:
    types: [opened, closed, reopened]

jobs:
  update-issue:
    runs-on: ubuntu-latest
    steps:
      - uses: theintern/action-project-manager@v0.1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          project: Development
          auto-add: true
          triage-column: Triage
          triage-label: needs-triage
          todo-column: To do
          working-column: In progress
          done-column: Done
```

## Configuration

| Property         | Required | Value                                                       |
| ---------------- | -------- | ----------------------------------------------------------- |
| `github-token`   | Yes      | A GitHub API token (`GITHUB_TOKEN` is available by default) |
| `project`        | Yes      | The project being managed                                   |
| `auto-add`       | No       | If true, all new issues are added to the project            |
| `triage-column`  | No       | Column name for new, unassigned issues                      |
| `triage-label`   | No       | Label to apply to new issues                                |
| `todo-column`    | No       | Column name for triaged issues                              |
| `working-column` | No       | Column name for in-progress issues                          |
| `done-column`    | No       | Column name for completed issues                            |

## How it works

Only issues should be added to a project, not PRs. Linked PRs are accessible
through the issue cards.

Note that any rule below that mentions an optional config property will only be
executed if that property is defined.

Rules:

- If `auto-add` is true, new issues will be added to `triage-column` and have
  `triage-label` set
- Issues in `triage-column` will be moved to `todo-column` when `triage-label`
  is removed
- Issues that are in `todo-column` or `triage-column` will be moved to
  `working-column` when they are assigned
- When a PR is opened that links to an issue in `triage-column` or
  `todo-column`, that issue will be moved to `working-column`
- An issue in `working-column` that is unassigned with be moved to
  `todo-columnn`
- Closed issues are moved to `done-column`
- Re-opened issues are moved to `working-column` if they are assigned, or to
  `todo-column` if not
