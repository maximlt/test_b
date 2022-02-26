# Upstream

## Useful links



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

## Run a workflow with `gh`.

From a repo with a `test.yaml` workflow file:

```
gh workflow run test.yaml -f mode=prod
```

From outside the repo (with `gh` auth setup):

```
gh workflow run test.yaml -f mode=prod --repo maximlt/downstream_a
