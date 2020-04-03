# Github deploy action

A GitHub action that notifies deploys to [Rollbar](https://rollbar.com).


## Usage

This action requires that you set the `ROLLBAR_ACCESS_TOKEN` environment variable with a token that must have the `post_server_item` scope.
You can find it under your project's settings in the Project access token section.

When notifiying deploys in two stages, for sending to Rollbar when a deploy starts and the status of its result (succeeded or failed ) you need
also to set the 'DEPLOY_ID' environment variable with the ouput of the previous step.


### Inputs

| Input          | Required  | Default      | Description                                      |
| -------------- | --------- | ------------ | ------------------------------------------------ |
| `environment`  | `true`    |              | The environment where the deploy is being done.  |
| `version`      | `true`    |              | The version being deployed.                      |
| `status`       | `false`   | `succeeded`  | The status of the deploy.                        |


### Ouputs

| Input        | Description           |
| ------------ | --------------------- |
| `deploy_id`  | The id of the deploy. |


### Example

```yaml
steps:
  - name: Notify deploy to Rollbar
    uses: rollbar/github-deploy-action@1.0.0
    id: rollbar_deploy
    with:
      environment: 'production'
      version: '1.0.0'
    env:
      ROLLBAR_ACCESS_TOKEN: ${{ secrets.ROLLBAR_ACCESS_TOKEN }}
```


### Example with deploy update

```yaml
steps:
  - name: Notify start deploy to Rollbar
    uses: rollbar/github-deploy-action@1.0.0
    id: rollbar_pre_deploy
    with:
      environment: 'production'
      version: '1.0.0'
      status: 'started'
    env:
      ROLLBAR_ACCESS_TOKEN: ${{ secrets.ROLLBAR_ACCESS_TOKEN }}

...

steps:
  - name: Notify finish deploy to Rollbar
    uses: rollbar/github-deploy-action@1.0.0
    id: rollbar_post_deploy
    with:
      environment: 'production'
      version: '1.0.0'
      status: 'succeeded'
    env:
      ROLLBAR_ACCESS_TOKEN: ${{ secrets.ROLLBAR_ACCESS_TOKEN }}
      DEPLOY_ID: ${{ steps.rollbar_pre_deploy.outputs.deploy_id }}
```