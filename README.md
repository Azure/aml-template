# ML Ops with GitHub Actions and AML

<p align="center">
  <img src="docs/images/aml.png" height="80"/>
  <img src="https://i.ya-webdesign.com/images/a-plus-png-2.png" alt="plus" height="40"/>
  <img src="docs/images/actions.png" alt="Azure Machine Learning + Actions" height="80"/>
</p>

This template shows the more extensive capabilities of using [GitHub Actions](https://github.com/features/actions) with [Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/) managing a machine learning project with automated training and deployment. For a more simplified version of this automated pipeline, see the [ml-template-azure](https://github.com/machine-learning-apps/ml-template-azure) repository. 

## Contents

| File/folder       | Description                                |
|-------------------|--------------------------------------------|
| `src`             | Sample source code.                        |
| `.github/workflows`| Workflow for automated ML training and deployment  |
| `.ml/azure`       | configuration files for azure              |
| `CONTRIBUTING.md` | Guidelines for contributing to the templates. |
| `README.md`       | This README file.                          |
| `LICENSE`         | The license for the sample.                |

## What is MLOps?

<p align="center">
  <img src="docs/images/ml-lifecycle.png" alt="Azure Machine Learning Lifecycle" height="250"/>
</p>

MLOps empowers data scientists and machine learning engineers to bring together their knowledge and skills to simplify the process of going from model development to release/deployment. ML Ops enables you to track, version, test, certify and reuse assets in every part of the machine learning lifecycle and provides orchestration services to streamline managing this lifecycle. This allows practitioners to automate the end to end machine Learning lifecycle to frequently update models, test new models, and continuously roll out new ML models alongside your other applications and services.

Since the deployment process with a machine learning model is not always an automated process and requires different checks during what would be the "build" process, we have two separate workflows that make up the end to end ML Ops workflow. These follow the common GitHub Pull Request process. 

Below is a diagram showing the entirety of the workflow. The workflow is as follows. A user edits the model training code (`src/train.py`), opens a Pull Request with these changes, the Pull Request will contain the output of the training run for the reviewers to understand how the model changed. The user can then deploy this model directly from the PR with familiar slash commands. 

<img src="/docs/images/ML Ops Workflow (1).png"/>

## Azure Machine Learning Actions

The template uses the following open source Azure certified Actions:
- Creation of or attachment to an AML Workspace with [azure/aml-workspace](https://github.com/Azure/aml-workspace)
- Managing Azure compute resources with [azure/aml-compute](https://github.com/Azure/aml-compute): 
- Managing Azure Machine Learning experimentation and pipeline runs with [azure/aml-run](https://github.com/Azure/aml-run)
- Model Registration in Azure Machine Learning [azure/aml-registermodel](https://github.com/Azure/aml-registermodel)
- Deployment to Azure Container Instances or Azure Kubernetes Service with [azure/aml-deploy](https://github.com/Azure/aml-deploy)

## Prerequisites

The following prerequisites are required to make this repository work:
- Azure subscription
- Contributor access to the Azure subscription
- Access to the [GitHub Actions](https://github.com/features/actions)

If you don’t have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://aka.ms/AMLFree) today.

## Getting Started

To get started with the template you will need to first create a repo based off of this template.

### Configuring Azure Credentials

Install the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and execute the following command to generate the credentials:

```sh
# Replace {service-principal-name}, {subscription-id} and {resource-group} with your Azure subscription id and resource group and any name
az ad sp create-for-rbac --name {service-principal-name} \
                         --role contributor \
                         --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                         --sdk-auth
```

This will generate the following JSON output:

```sh
{
  "clientId": "<GUID>",
  "clientSecret": "<GUID>",
  "subscriptionId": "<GUID>",
  "tenantId": "<GUID>",
  (...)
}
```

Add the JSON output as [a secret](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets#creating-encrypted-secrets) with the name `AZURE_CREDENTIALS` in the GitHub repository.

### Modify the Code

You will need to modify the code in the <a href="/src">`src` folder</a> with your python code that will train your model. Where required, modify the environment yaml so that the training and deployment environments will have the correct packages for your training and deployment.

### Training your Model

In order to train your model, open a PR with your changes. This will trigger the `train.yml` workflow, when the training is complete, the metrics from your run will be written back to the PR. 

```
outputs:
  run_metrics - markdown table of metrics
  model_id - <model name>:<model version>
 ```

### Deploying your model

Slash commands are used to deploy your model in the pull request comments. In the pull request that you opened these commands can be used to run tests of your model as well as deploy the model to dev or prod infrastructure.

deploy slash command:
```
/deploy model_name=<model name> model_version=<model version> test=<true or false> dev=<true or false> prod=<true or false>
```

#### deploy inputs

| Argument   | Required | Allowed Values   | Default    | Description |
| -----------| -------- | ---------------- | ---------- | ----------- |
| model_name | yes      | string           |            | model name written to pr as `<model name>:<model id>` |
| model_id   | yes      | string           |            | model version written to pr as `<model name>:<model id>` |
| test       | no | boolean: true or false | false      | whether to test the model prior to deployment using Azure Container Instances |
| dev       | no | boolean: true or false  | false      | dev environment deployment |
| prod      | no | boolean: true or false  | false      | prod environment deployment |

Upon commenting in the PR, actions will kick off your deployment run and the run output information will be written back as a comment to the PR.


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
