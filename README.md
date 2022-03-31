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

## serverless deploy

Deploys a [serverless](https://www.serverless.com/) app to AWS.  

* name: `serverless-deploy.yaml`
* required params:
   - `NPM_TOKEN` npm token for @condenast
   - `AWS_ACCESS_KEY_ID` aws access key id to deploy serverless stack
   - `AWS_SECRET_ACCESS_KEY` aws secret key to deploy serverless stack
