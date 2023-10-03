# Add .NET SDK patching to a repository
This is an example workflow to show how to add a GitHub action to patch a repository. This action can be scheduled to run and automatically pick up any minor version updates to the .NET SDK, based on the current value in the `global.json` file in your repository. If it finds an update it will generate a PR for it, and create a work item for the PR on Azure DevOps.

If, and only if, it finds an SDK update it can optionally update NuGet packages within the repository.

## How to use
1. Create a folder in your repository called `.github/workflows`, if it doesn't already exist.
2. Create a yaml workflow in the folder. You can call it whatever you want. For an example see [`auto-patch-sdk.yml`](auto-patch-sdk.yml).

## Workflow overview
```yaml
name: auto-patch-sdk
```
The name that will appear in the Actions tab for your repository.

---

```yaml
on:
  schedule:
    - cron: '00 21 * * TUE'
  workflow_dispatch:
```
The `schedule` event is used to trigger the action. In this example the action will run every Tuesday at 21:00.

The `workflow_dispatch` event allows the action to be triggered on request, from the repository's Actions tab for example.

---

```yaml
jobs:
  update-sdk:
    uses: UKHO/repo-auto-patch/.github/workflows/update-dotnet-sdk.yml@v1
```
Call the latest major version of the update workflow.

---

```yaml
    with:
      include-nuget-packages: "System,Auto"
```
This is the only section that may need to be updated per repository.

- `update-nuget-packages`

  Default `true`. Specify whether or not to check for NuGet package updates if the SDK is updated.

- `include-nuget-packages`

  Default `Microsoft.AspNetCore,Microsoft.EntityFrameworkCore,Microsoft.Extensions,System.Text.Json`. A comma separated list of packages to update. This includes packages with names that contain one of these values as a substring. The default list covers Microsoft packages as these typically get updated when a new SDK version is released.

- `exclude-nuget-packages`

  Default `''`. A comma separated list of packages to exclude from the update.

- `user-email` and `user-name`

  Default `github-actions[bot]@users.noreply.github.com` and `github-actions[bot]` respectively. The email address and user name to be attached to the commits.

- `dry-run`

  Default `false`. Used for testing. If true the workflow will check for a new SDK version but not push any changes to GitHub.

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

|Output                 |Description                                                                     |
|:----------------------|:-------------------------------------------------------------------------------|
|`sdk-updated`          |Whether or not (`true` or `false`) the .NET SDK was updated.                    |
|`branch-name`          |If `sdk-updated`, the name of the new branch.                                   |
|`pull-request-html-url`|If `sdk-updated`, the URL of the PR.                                            |
|`pull-request-number`  |If `sdk-updated`, the number of the PR.                                         |
|`sdk-version`          |If `sdk-updated`, the new version number of the SDK.                            |
|`security`             |Whether or not (`true` or `false`) the .NET SDK update contained security fixes.|
