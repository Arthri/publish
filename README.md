# Publish
Reusable workflows for publishing to various targets.

## Installation
1. Add a new workflow under `.github/workflows/` with the following contents.
    ```yml
    name: Publish

    on:
      release:
        types:
          - published

    jobs:
      publish-nuget:
        uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
        secrets:
          NUGET-API-KEY: ${{ secrets.NUGET_API_KEY }}
    ```
1. Create a new environment named `NuGet (Stable)` with a secret named `NUGET-API-KEY` containing the API key used to publish packages to NuGet.
  1. Go to the repository's settings tab.
  1. Navigate to `Environments` under the `Code and automation` section.
  1. Create a new environment named `NuGet (Stable)`.
  1. Obtain a NuGet API key.
  1. Add a secret named `NUGET-API-KEY` containing the NuGet API key.
  1. Configure environment as appropriate.

## Usage
1. Create and publish a GitHub release
1. Wait for the workflow to create and push the NuGet package.

### Set Release Notes or Changelog
NuGet supports release notes (for example, [Belp.Build.Packinf@0.6.0](https://www.nuget.org/packages/Belp.Build.Packinf/0.6.0#releasenotes-body-tab)), a tab dedicated to the changes introduced in a given version. By default, the NuGet publish workflow doesn't build packages with release notes, but it can be configured to.

```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      changelog: |
        - Added this
        - Removed that
```

Optionally, to source the release notes from the GitHub release,

```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      changelog: ${{ github.event.release.body }}
```

### Change Environment Name
The workflow acquires the NuGet API key from an environment named `NuGet (Stable)`, by default. The environment name can be changed using the `environment-name` input parameter.
```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      environment-name: Custom Environment Name
```

### Specify Project
The workflow, by default, builds the singular solution or project at the root of the repository. If there are multiple solutions and/or projects at the root of the repository, a project must be specified explicitly using the `project-path` input parameter.
```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      project-path: ./src/Test.App/Test.App.csproj
```

### Set Version
By default, the package builds with the version specified in the project. A different version can be set by using the `version` input parameter.
```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      version: v1.0.0
      # Alternatively, use the tag of the release.
      # version: ${{ github.event.release.tag_name }}
```

### Disable Version Sanitization
If a version is specified explicitly, then it will be sanitized to remove the `v` prefix and any directories. For example, `a/b/c/v1.0.0` becomes `1.0.0`, and `v1.2.3` becomes `1.2.3`. To disable version sanitization, set the `sanitize-version` input parameter to `false`
```yml
jobs:
  publish-nuget:
    uses: Arthri/publish/.github/workflows/publish-nuget.yml@v1
    with:
      sanitize-version: false
```
