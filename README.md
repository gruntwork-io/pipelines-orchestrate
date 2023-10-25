# Pipelines Orchestrator GitHub Action

This GitHub Action, named "Pipelines Orchestrate," is designed to automate the orchestration of Terragrunt plan/apply/destroy jobs using the Gruntwork Pipelines infrastructure-as-code (IaC) deployment framework. This action helps determine which actions need to take place based on changes to a repository's Terraform or Terragrunt folders.

## Inputs

### `repository-path`

The relative path to the infra-live code repository. Defaults to "."

### `repository-url`

The GitHub URL for the repository. This input is required.

### `source-ref`

The commit to use as the basis against which a diff will be generated. This input is required.

### `target-ref`

The most recent commit. This input is required.

### `event-type`

The type of event that triggered the workflow. Choose between "push" (for direct pushes to the main branch) or "pr-synched-created" (for pull request synchronization/creation). This input is required.

### `gruntwork-config`

Base64 encoded content of the Gruntwork Config YAML file. This input is required.

### `token`

GitHub Personal Access Token (PAT) required for retrieving the pipelines binary. This input is required.

## Outputs

### `jobs`

An array of jobs to be dispatched to the Pipelines Execute step.

## Usage

```yaml
name: Pipelines Orchestration Workflow

on:
  push:
    branches:
      - main
  pull_request:
    types:
      - synchronize
      - opened

jobs:
  orchestrate_pipelines:
    name: Orchestrate Pipelines Jobs
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v2

      - name: Run Pipelines Orchestrate
        id: orchestrate
        uses: gruntwork-io/pipelines-orchestrate@v1.0.0
        with:
          repository-path: '.'
          repository-url: ${{ github.repository }}
          source-ref: ${{ github.event.before }}
          target-ref: ${{ github.sha }}
          event-type: ${{ github.event_name }}
          gruntwork-config: "cGlwZWxpbmVzO...VudF92ZXJzaW9uOiAwLjQ4LjEK" # Base64 encoded content of the Gruntwork Config YAML file
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Dispatch Pipelines Jobs
        run: |
          # Use the output 'jobs' from the orchestrate step to dispatch jobs

```
