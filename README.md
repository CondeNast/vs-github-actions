# Description
Creating a central repo where reusable workflows are being served in parameterised manner.Keeping all of them in `.github/workflows` folder/path. And to access those, we can call it as below. 

`uses: CondeNast/vs-github-actions/.github/workflows/e2e-test.yaml@main`

# Workflow types
We have the following workflows:

## unit test
This workflow installs dependencies, checks for audit and runs the unit tests.
* name: `unit-test.yaml`
* required params: 
    - `NPM_TOKEN` npm token for @condenast

## cypress (e2e) test
This workflow builds the application, starts the mock server and runs cypress tests as per parameterised command.
* name: `e2e-test.yaml`
* required params: 
  - `NPM_TOKEN` npm token for @condenast
  - `CYPRESS_RUN` command in your repo that run cypress tests, e.g. `yarn run test:e2e --headless --browser chrome` 

## docker build
Docker workflow builds the docker image by logging into quay
* name: `docker.yaml`
* required params: 
  - `NPM_TOKEN` npm token for @condenast
  - `QUAY_USER` User name to login in quay
  - `QUAY_TOKEN` quay password/token

## Serverless deploy and remove

`serverless-deploy.yaml` deploys a Serverless stack, and `serverless-remove.yaml` removes one. Both use short-lived AWS credentials obtained through GitHub OIDC. Callers must grant `id-token: write` and `contents: read` permissions and configure the selected GitHub environment for OIDC access.

Both workflows require these `with` inputs:

| Input | Purpose |
| --- | --- |
| `env` | Serverless stage and AWS profile name, such as `dev`, `staging`, or `prod`. |
| `account_id` | AWS account containing the stack. |
| `aws_region` | AWS region for the stack. |
| `tenant_name` | AWS tenant used to select the deployment role. |
| `custom_env` | Optional GitHub environment name. Defaults to `env` when omitted. |

Pass `NPM_TOKEN` as a secret. The Zephr and Sailthru secrets remain optional. AWS access keys are no longer workflow inputs. The workflows write the temporary OIDC credentials, including the session token, into the stage's AWS profile inside the Serverless action container. A caller's `provider.profile` must match `env`, as it did with the previous `sls config credentials --profile` command.

For example, a caller deploying the Vogue Services staging stage can use:

```yaml
permissions:
  id-token: write
  contents: read

jobs:
  deploy_staging:
    uses: CondeNast/vs-github-actions/.github/workflows/serverless-deploy.yaml@main
    with:
      env: staging
      custom_env: vogue-services-nonprod
      account_id: "130392784807"
      aws_region: eu-central-1
      tenant_name: vogue-services
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Use the production account and protected GitHub environment for production stages. Before changing a caller to the OIDC workflow, verify that its environment can assume the deployment role and remove its `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` secret mappings.
