# aws-cfn-code-pipeline
This is a basic AWS CodePipeline for Cloudformation using GitHub as the source.

## Step 1 - GitHub repository OAuth token
Create a [New personal access token](https://github.com/settings/tokens/new) with `repo` and `admin:repo_hook` scopes, and enter the token in the `GitHubToken` field.

## Step 2 - Deploy the pipeline
Configure the [github-basic-pipeline.json](pipeline/github-basic-pipeline.json) and 
create the stack.

```bash
$ aws cloudformation create-stack --stack-name github-basic-pipeline \
  --template-body file://github-basic-pipeline.yml \   
  --parameters file://github-basic-pipeline.json \    
  --capabilities CAPABILITY_IAM
```

## Step 3 - Push a change to the source repo to trigger the pipeline
When you push a change to the source repo, the pipeline will trigger.

* The `SourceStage` will clone the repo into the `ArtifactSource` bucket.
* The `TestStage` will deploy the stack using the `TestStackConfig` file.
* The `ApproveTestStack` step is a manual approval step that sends an email notification.
* Once approved, the `DeleteTestStack` stage will delete the test stack.
* The `ProdStage` will create a changeset for the production stack using the `ProdStackConfig` file.
* The `ApproveChangeSet` stage is a manual approval to execute the changeset against the production stack.
* Once approved, the changeset will be executed against the production stack.
