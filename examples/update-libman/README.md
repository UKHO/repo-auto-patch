# Add LibMan patching to a repository
This is an example workflow to show how to update libraries controlled by [LibMan](https://learn.microsoft.com/en-us/aspnet/core/client-side/libman). This can be scheduled to run and automatically pick up any updates to LibMan controlled libraries, based on the `libman.json` file in a project. If it finds an update it will generate a PR for it, and create a work item for the PR on Azure DevOps.

## How to use
1. Create a folder in your repository called `.github/workflows`, if it doesn't already exist.
2. Create a yaml workflow in the folder. You can call it whatever you want. For an example see [`auto-patch-libman.yml`](auto-patch-libman.yml).

## Workflow overview
```yaml
name: auto-patch-libman
```
The name that will appear in the Actions tab for your repository.

---

```yaml
on:
  schedule:
    - cron: '00 10 * * FRI'
  workflow_dispatch:
```
The `schedule` event is used to trigger the action. In this example the action will run every Friday at 10:00.

The `workflow_dispatch` event allows the action to be triggered on request, from the repository's Actions tab for example.

---

```yaml
jobs:
  update-libman:
    uses: UKHO/repo-auto-patch/.github/workflows/update-libman.yml@v1
```
Call the latest major version of the update workflow.

---

```yaml
    with:
      project-folder: "/DotNetUpdate/WebProject"
      libraries: "jquery,Chart.js,epub.js"
      work-item-title: "HB LibMan PBI Create Test"
      work-item-description: "Review this PR."
      work-item-tags: "Maritime Safety Information; TD2; Technical Debt"
```
This is the only section that may need to be updated per repository.

- `project-folder`

  Mandatory. Specify the path in your repository to the folder containing `libman.json`.

- `libraries`

  Mandatory. A comma separated list of libraries to update.

- `user-email` and `user-name`

  Default `github-actions[bot]@users.noreply.github.com` and `github-actions[bot]` respectively. The email address and user name to be attached to the commits.

- `ado-organisation`

  Default `ukhydro`. The Azure DevOps organisation to use for the new work item.

- `ado-project`

  Default `Abzu`. The Azure DevOps project to use for the new work item

- `work-item-title`

  Default `PR created - [URL of PR]`. A title to use for the new work item.

- `work-item-area-path`

  Default `Abzu\Abzu Overall`. The area path for the new work item.

- `work-item-iteration-path`

  Default `Abzu\Abzu Overall\Abzu Overall Kanban`. The iteration path for the new work item.

- `work-item-description`

  Default `''`. A description for the new work item. Note that a link to the PR will always be added to the top of the description, even if the blank default is used.

- `work-item-tags`

  Default `''`. Tags to be added to the new work item, supplied as a semicolon seperated list.

---

```yaml
    secrets:
      ado-pat: ${{ secrets.ADO_TOKEN }}
```
Mandatory field, used to authorise the creation of a new work item in Azure DevOps. This is an Azure DevOps PAT stored as a repository secret called ADO_TOKEN. This PAT must have work item read and write access.

### Outputs
The following outputs are available:

|Output                 |Description                                                                                                                             |
|:----------------------|:---------------------------------------------------------------------------------------------------------------------------------------|
|`update-skipped`       |Whether or not (`true` or `false`) processing was skipped because no updates were found, or a branch for a previous update still exists.|
|`branch-name`          |The name of the new branch.                                                                                                             |
|`pull-request-url`     |The URL of the PR, if created.                                                                                                          |
