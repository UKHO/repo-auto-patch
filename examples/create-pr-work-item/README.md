# Example Azure DevOps work item creation
This is an example workflow used to create a work item in Azure DevOps. In this example it is triggered by the creation of a PR by Dependabot.

## How to use
1. Create a folder in your repository called `.github/workflows`, if it doesn't already exist.
2. Create a yaml workflow in the folder. You can call it whatever you want. For an example see [`dependabot-work-item.yml`](dependabot-work-item.yml).

## Workflow overview
```yaml
name: dependabot-work-item
```
The name that will appear in the Actions tab for your repository.

---

```yaml
on:
  pull_request:
    branches:
      - main
```
The `pull_request` event is used to trigger the action on the creation of a PR. This is limited to PRs against the `main` branch here.

---

```yaml
jobs:
  create-work-item:
    if: ${{ github.actor == 'dependabot[bot]' }}
    uses: UKHO/repo-auto-patch/.github/workflows/create-pr-work-item.yml@v1
```
Call the latest major version of the create work item workflow. The `if` statement here means that it will only be called if the PR was created by Dependabot.

---

```yaml
    with:
      pr-url: ${{ github.event.pull_request.html_url }}
      work-item-title: "HB Dependabot PBI Create Test"
      work-item-description: "Review this PR."
      work-item-tags: "Maritime Safety Information; TD2; Technical Debt"
```
This is the only section that may need to be updated per repository.

- `pr-url`

  Mandatory field. This is the URL for the newly created PR and will be added to the description in the new work item.

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
Mandatory field, used to authorise the creation of a new work item in Azure DevOps. This is an Azure DevOps PAT stored as a Dependabot secret called ADO_TOKEN. This PAT must have work item read and write access.
