# Pre-Commit

This action uses [Pre-Commit](https://pre-commit.com/) to run a series of checks against the IAC repository.

The default configuration looks like this:

```
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.2.0
    hooks:
      - id: check-merge-conflict
      - id: detect-aws-credentials
        args: [--allow-missing-credentials]
      - id: detect-private-key
      - id: trailing-whitespace
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.70.1
    hooks:
      - id: terraform_fmt
        args:
          - --args=-recursive
      - id: terraform_docs
      - id: terraform_tflint
        args: 
          - --args=--enable-plugin=aws
          - --args=--disable-rule=terraform_module_pinned_source
          - --args=--disable-rule=aws_lambda_function_invalid_runtime
```

If you create your own `.pre-commit-config.yaml` file in the root if your repository, it will take precendece over the above default configuration.

Once the check is performed, if `pre-commit-hooks` or `terraform_fmt` finds issues that it can fix, they will be fixed and changed files will be pushed to the branch.
If the user passes personal access token to the `Token` variable (See example below), the actions associated to the PR will be re-triggered.
If a personal access token is not used, changes pushed by this action will not trigger actions associated to the PR.  

For more information about these hooks, please see [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks) and [pre-commit-terraform](https://github.com/antonbabenko/pre-commit-terraform)


## Example

_Standard configuration_

In the below configuration, please change the `TerraformVersion` to the version that your stack is using. This is the only required parameter:

```
name: Pre-Commit Checks

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  pre-commit:
    runs-on: [self-hosted, cko-transformation-default-runners-github-runners]

    steps:
      - name: Run pre-commit
        uses: cko-transformation/actions/pre-commit@add-pre-commit-action
        with:
          TerraformVersion: '0.13.5'
```

_Non-standard configuration_

The following example shows all of the optional parameters should you need to change them:

```
name: Pre-Commit Checks

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  pre-commit:
    runs-on: [self-hosted, cko-transformation-default-runners-github-runners]

    steps:
      - name: Run pre-commit
        uses: cko-transformation/actions/pre-commit@add-pre-commit-action
        with:
          TerraformVersion: '0.13.5'
          TflintVersion: '0.34.1'
          TerraformDocsVersion: '0.16.0'
          PythonVersion: '3.9'
          NodeVersion: '12'
          Token: ${{ secrets.MY_GITHUB_SECRET }}
```

## Inputs

|Name|Description|Default Value|Required
|---|---|---|---|
|TerraformVersion|The version of Terraform to use.|1.1.9|Yes|
|TflintVersion|Optional version of tflint to use.|0.34.1|No|
|TerraformDocsVersion|Optional version of terraform_docs to use|0.16.0|No|
|PythonVersion|Optional version of Python to use.|3.9|no|
|NodeVersion|Optional version of Node to use.|12|no|
|Token|Optional Personal access token (PAT) used to fetch the repository.|github.token|no|