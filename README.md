# reusable-workspace

This GitHub action will help you to persist your workspace from job to job on your workflow to enable parallel jobs and steps!

> Note: This action uses [actions/cache](https://github.com/actions/cache) under the hood.

## Usage

A common use case is to save the workspace in one job and restore it in another job. This is useful for parallel jobs or steps that need to share many costly steps.

> ❗Warning: The save action only includes files present at the time of the step unlike [actions/cache](https://github.com/actions/cache). If you need to save files generated later in the job, use this action after those steps!

```yml
name: CI

on:
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      # Build steps (save should be after this)
      - uses: jpoehnelt/reusable-workspace/save@v1
  preview:
    needs: [build]
    runs-on: ubuntu-latest
    steps:
      - uses: jpoehnelt/reusable-workspace/restore@v1
      # Additional steps (restore should be before this)
  test:
    needs: [build]
    runs-on: ubuntu-latest
    steps:
      - uses: jpoehnelt/reusable-workspace/restore@v1
        with:
          fail-on-miss: true
      # Additional steps (restore should be before this)
```

## Inputs

| Input          | Type              | Default | Description                                                                                                                                                            |
| -------------- | ----------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fail-on-miss` | `true` or `false` | `false` | Reuse an existing workspace and fail if it is missing. If set to `false`, the workspace may be empty. Note this does not apply to `jpoehnelt/reusable-workspace/save`. |
| `prefix`       | `string`          | `''`    | The prefix to use for the workspace key. The key already incorporates values such as `os`, `sha`, and `workflow-sha`.                                                  |
