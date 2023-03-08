# MongoDB Atlas CLI GitHub Action (unofficial)

With MongoDB Atlas CLI GitHub Action, you can automate your workflow by executing [Atlas CLI](https://www.mongodb.com/docs/atlas/cli/stable/) commands to manage Atlas resources inside of your GitHub workflow.

The action executes the Atlas CLI on a user defined version. If the user does not specify a version, latest CLI version is used. Read more about various Atlas CLI versions [here](https://www.mongodb.com/docs/atlas/cli/stable/atlas-cli-changelog/).

## This is a personal project. MongoDB is not involved and does not provide support for this GitHub Action

## Requirements
1. You need to provide MongoDB Atlas programmatic keys. See [configure API keys](https://www.mongodb.com/docs/atlas/configure-api-access/#std-label-atlas-admin-api-access).
2. You need to add your MongoDB Atlas programmatic keys as [Encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) to your GitHub Repository.

## Limitations
1. Atlas CLI GitHub Action has been tested only with `ubuntu-latest`, `macos-latest`, `windows-latest`.
2. Atlas CLI GitHub Action does not support `atlas auth login`.


## Usage

### Basic
```yaml
# File: .github/workflows/workflow.yml

on: [push]

name: Atlas CLI Action Sample

jobs:
  use-atlas-cli:
    runs-on: ubuntu-latest
    
    steps:
      - name: Configure Atlas CLI
        uses: andreaangiolillo/atlas-cli-github-action@v1.0.0
        with:
         public-key: ${{ secrets.PUBLIC_KEY }}
         private-key: ${{ secrets.PRIVATE_KEY }}
         
      - name: Use AtlasCLI
        shell: bash
        run: atlas --version # Print Atlas CLI Version

```
### Run Atlas CLI with default Organization and Project
```yaml
# File: .github/workflows/workflow.yml

on: [push]

name: Atlas CLI Action Sample

jobs:
  use-atlas-cli:
    runs-on: ubuntu-latest
    
    steps:
      - name: Configure Atlas CLI
        uses: andreaangiolillo/atlas-cli-github-action@v1.0.0
        with:
         version: v1.5.0 # optional
         public-key: ${{ secrets.PUBLIC_KEY }}
         private-key: ${{ secrets.PRIVATE_KEY }}
         org-id: ${{ vars.ORG_ID }} # optional
         project-id: ${{ vars.PROJECT_ID }} # optional
         
      - name: Use AtlasCLI
        shell: bash
        run: atlas project ls # List projects available in your Atlas organization

```

### Quickstart - Create a new Atlas Project, a new Free Cluster, a new DBUser to use in your GitHub workflow

```yaml
# File: .github/workflows/workflow.yml

on: [push]

name: Atlas CLI Action Sample

jobs:
  atlas-cli-quickstart:
    runs-on: ubuntu-latest
    
    steps:
      - name: Atlas CLI Quickstart
        id: atlas-cli
        uses: andreaangiolillo/atlas-cli-github-action@main
        with:
         public-key: ${{ secrets.PUBLIC_KEY }}
         private-key: ${{ secrets.PRIVATE_KEY }}
         org-id: ${{ vars.ORG_ID }} # Required with Quickstart
         quickstart: true # Run Quickstart
      
      - name: Retrieve the Cluster info
        shell: bash
        run: |
          echo "${{ steps.atlas-cli.outputs.quickstart-connection-string }}" # Retrieve the connection string of the Atlas Cluster
          echo "${{ steps.atlas-cli.outputs.quickstart-db-username }}" # Retrieve the DBuser to access the Atlas Cluster
          echo "${{ steps.atlas-cli.outputs.quickstart-db-username-password }}" # Retrieve the DBuser password to access the Atlas Cluster
      
      - name: Atlas CLI Cleanup # Delete the Atlas Project and the Atlas Cluster created by Quickstart
        uses: andreaangiolillo/atlas-cli-github-action@main
        with:
         public-key: ${{ secrets.PUBLIC_KEY }}
         private-key: ${{ secrets.PRIVATE_KEY }}
         org-id: ${{ vars.ORG_ID }}
         delete-cluster-name: ${{ steps.atlas-cli.outputs.quickstart-cluster-name }} # Cluster Name to delete. Here we are using the output from Quickstart
         delete-project-id: ${{ steps.atlas-cli.outputs.quickstart-project-id }} # Project to delete. Here we are using the output from Quickstart

```



Other examples available in [workflow/test.yml](.github/workflows/test.yml). 
## Configuration options

| Option                | Usage                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Default value |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| `version`             | Define the Atlas CLI version to use in the workflow. **Optional**                                                                                                                                                                                                                                                                                                                                                                                                                                                | `v1.5.1`     |
| `public-key`          | The MongoDB Atlas Public Key. **Required**                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | none         |
| `private-key`         | The MongoDB Atlas Private Key. **Required**                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | none         |
| `org-id`              | The MongoDB Atlas Organization to use in your workflow. **Optional**                                                                                                                                                                                                                                                                                                                                                                                                                                             | none         |
| `project-id`          | The MongoDB Atlas Project to use in your workflow. **Optional**                                                                                                                                                                                                                                                                                                                                                                                                                                                  | none         |
| `quickstart`          | Quickstart simplifies the process of creating a new testing environment by automating the setup of a Atlas Project, a Free Atlas Cluster and a new DB user for accessing the cluster. Additionally, the tool allows you to quickly open the Atlas Cluster to the public internet for easy access. After the process is completed, you will have access to all the necessary information to use these resources in your subsequent steps in the outputs. This configuration requires `org-id`.   **Optional**     | false        |
| `create-project-name` | The MongoDB Atlas Cluster to create. The projectId is provided in the outputs. This configuration requires `org-id`. **Optional**                                                                                                                                                                                                                                                                                                                                                                                | none         |
| `delete-project-id`   | The MongoDB Atlas Project to delete. This configuration requires `org-id`. **Optional**                                                                                                                                                                                                                                                                                                                                                                                                                          | none         |
| `delete-cluster-name` | The MongoDB Atlas Cluster to delete. This configuration requires `delete-project-id`. **Optional**                                                                                                                                                                                                                                                                                                                                                                                                               | none         |

## Outputs Properties

| Outputs                           | Input Configuration Option to provide to get the output | Usage                                                                                                                                     |
|-----------------------------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `create-project-id`               | `create-project-name`                                   | This property contains the project Id of the project created by `create-project-id`,                                                      |
| `quickstart-cluster-name`         | `quickstart`                                            | This property contains the cluster name created by `quickstart`.                                                                          |
| `quickstart-db-username`          | `quickstart`                                            | This property contains the dbuser username to use to access the cluster created by `quickstart`.                                          |
| `quickstart-db-username-password` | `quickstart`                                            | This property contains the dbuser password to use to access the cluster created by `quickstart`.                                          |
| `quickstart-connection-string`    | `quickstart`                                            | This property contains the connection string of the cluster created by `quickstart`  that can be used to connect to the MongoDB database. |
| `quickstart-project-id`           | `quickstart`                                            | This property contains the project ID of the project created by `quickstart`.                                                             |


## Feature Requests / Questions / Issues
Please, file a query directly on the [GitHub repository](https://github.com/andreaangiolillo/atlas-cli-github-action/issues) if you encounter an issue related to this action or if you have questions or feature requests.


## License
The scripts and documentation in this project are released under the [Apache License](LICENSE).
