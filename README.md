# Restrict Renovate Bot Workflow GitHub Action

This repository demonstrates a proof of concept for restricting specific GitHub workflows to run based on the actor initiating the workflow. This can be particularly useful when using automation tools like Renovate Bot and wanting to limit their ability to trigger certain workflows, thereby giving finer control over automated processes in your project.

## Overview

We have defined two jobs within the workflow:

1. **test-allow**: This job runs only if the actor initiating the workflow is either `raphael-arce` or `ff6347`. This shows how to allow specific users to trigger a workflow.

2. **test-deny**: In contrast, this job runs for any actor _except_ `renovate[bot]`. This demonstrates how to exclude an automated bot or specific user from triggering a workflow.

An important observation made during the setup is that the user to restrict in the second job (`test-deny`) **needs to be specified in single quotes** to work correctly.

## Workflow File

```yaml
name: testing action restrictions
on:
  workflow_dispatch:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test-allow:
    # Allows only specific users
    if: contains('["raphael-arce","ff6347"]', github.actor)
    runs-on: ubuntu-latest
    steps:
      - name: test it
        run: echo "Hello, ${{ github.actor }}"

  test-deny:
    # Denies workflow run for renovate[bot]
    if: github.actor != 'renovate[bot]'
    runs-on: ubuntu-latest
    steps:
      - name: test it
        run: echo "Hello, ${{ github.actor }}"
```

## Setup Instructions

To implement similar restrictions in your workflows, follow these instructions:

1. Navigate to your GitHub repository's `.github/workflows` directory.
2. Create or edit an existing workflow YAML file.
3. Add conditions to your jobs using the `if:` field. Use the following syntax as a guideline:
   - To allow specific users: `if: contains('["user1","user2"]', github.actor)`
   - To deny specific users: `if: github.actor != 'specific_user'`
   - Remember to enclose usernames in single quotes (`'`) when checking for inequality.
4. Commit the changes to your repository.

## Important Notes

- The `github.actor` context variable contains the username of the person or app that initiated the workflow run.
- Remember that workflow conditions (`if:`) are case-sensitive and must exactly match the GitHub username or bot name.
- The use of single quotes is crucial when specifying the username in the condition to deny workflow runs.

## Contributing

Contributions, issues, and feature requests are welcome! Feel free to discuss any bugs or feature requests in the issues section of this repository.
