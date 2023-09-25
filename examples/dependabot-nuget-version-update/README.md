# Example Dependabot update script
This is an example configuration file that will get Dependabot to scan for NuGet package updates within a repository.

For a full description of options see [here](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file).

## How to use
1. Create a folder in your repository called `.github`, if it doesn't already exist.
2. Create a file in the folder called dependabot.yml. For an example see [`dependabot.yml`](dependabot.yml).

## Overview of example
```yaml
version: 2
updates:
```
Mandatory keys (see documentation linked above).

---

```yaml
  - package-ecosystem: "nuget"
    directory: "/DotNetUpdate"
```
Look for NuGet package updates. Look in the specified directory for a solution or project file to scan.

---

```yaml
    schedule:
      interval: "weekly"
```
Schedule the check to run weekly.

---

```yaml
    ignore:
      - dependency-name: "*"
        update-types: ["version-update:semver-major"]
```
Ignore major version updates. In other words, limit updates to minor and patch versions only.

---

```yaml
    groups:
      all-packages:
        patterns:
        - "*"
```
Group all updates together in a single PR, in a group called `all-packages`.
