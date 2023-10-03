# repo-auto-patch
A place to store workflows, scripts and configuration files used to patch repositories.

## Current major version
[v1](https://github.com/UKHO/repo-auto-patch/tree/v1)

## Examples
|Function                                           |Description                                                                                           |
|:--------------------------------------------------|:-----------------------------------------------------------------------------------------------------|
|[`update-dotnet-sdk`](./examples/update-dotnet-sdk)|Check the version of the .NET SDK used in a project and update it if a new minor version is available.|
|[`create-pr-work-item`](./examples/create-pr-work-item)|Create a work item in Azure DevOps, in this example triggered by a Dependabot PR.|
|[`dependabot-nuget-version-update`](./examples/dependabot-nuget-version-update)|An example script used to get Dependabot to perform NuGet package version updates.|
|[`update-libman`](./examples/update-libman)|Check for updates using [LibMan](https://learn.microsoft.com/en-us/aspnet/core/client-side/libman).|
