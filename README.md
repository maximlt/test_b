# Upstream

## Useful links

* [Github Actions - trigger another action after one action is completed](https://stackoverflow.com/questions/62750603/github-actions-trigger-another-action-after-one-action-is-completed)
* [Trigger Another Repository's Github Action Workflow and Wait for Result](https://keithweaver.ca/lessons/trigger-another-repositorys-github-action-workflow-wait-for-result/?s=ytktc)
* [workflow_run](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_run) event
* [A GitHub Action for triggering workflows, using the `workflow_dispatch` event](https://github.com/benc-uk/workflow-dispatch)
* [Use `gh` to create a `workflow_dispatch` event](https://cli.github.com/manual/gh_workflow_run)

## Permission

The [`GITHUB_TOKEN`](https://docs.github.com/en/actions/security-guides/automatic-token-authentication) secret used by default is scoped to the current repository. A personal access token (PAT) must be [created](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) and used to get access to a different repository.

## Trigger a workflow in another repo with a Github action

See https://github.com/convictional/trigger-workflow-and-wait

```
    - uses: convictional/trigger-workflow-and-wait@v1.5.0
      with:
        owner: maximlt
        repo: downstream_1
        github_token: ${{ secrets.MY_PAT }}
        workflow_file_name: test.yaml
        ref: main
        wait_interval: 10
        inputs: '{"caller": "from upstream"}'
        propagate_failure: true
        trigger_workflow: true
        wait_workflow: true
```

Hopefully this [PR](https://github.com/convictional/trigger-workflow-and-wait/pull/37) will get merged soon, it fixes potential race conditions and makes more robust the run_id discovery mechanism.

## Trigger a workflow with a GitHub script

```yaml
    - uses: actions/github-script@v6
      with:
        github-token: ${{ secrets.MY_PAT }}
        script: |
          await github.rest.actions.createWorkflowDispatch({
            owner: 'maximlt',
            repo: 'test_a',
            workflow_id: 'test.yaml',
            ref: 'main',
            inputs: {"mode": "from another workflow"}
          })
```

See:
    * the [action docs](https://github.com/actions/github-script)
    * the [octokit API docs](https://octokit.github.io/rest.js/v18).

## Run a workflow with `gh`.

From a repo with a `test.yaml` workflow file:

```
gh workflow run test.yaml -f mode=prod
```

From outside the repo (with `gh` auth setup):

```
gh workflow run test.yaml -f mode=prod --repo maximlt/downstream_a
