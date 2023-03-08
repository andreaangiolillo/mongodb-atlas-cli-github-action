# Atlas CLI GitHub Action (not-official)

With Atlas CLI GitHub Action, you can automate your workflow by executing Atlas CLI commands to manage Atlas resources inside of an Action.

The action executes the Atlas CLI Bash script on a user defined version. If the user does not specify a version, latest CLI version is used. Read more about various Atlas CLI versions [here](https://www.mongodb.com/docs/atlas/cli/stable/atlas-cli-changelog/).

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
  build-and-deploy:
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
### Provide optional configurations
```yaml
# File: .github/workflows/workflow.yml

on: [push]

name: Atlas CLI Action Sample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Configure Atlas CLI
        uses: andreaangiolillo/atlas-cli-github-action@v1.0.0
        with:
         version: v1.5.0 # optional
         public-key: ${{ secrets.PUBLIC_KEY }}
         private-key: ${{ secrets.PRIVATE_KEY }}
         org-id: ${{ env.ORG_ID }} # optional
         project-id: ${{ env.PROJECT_ID }} # optional
         
      - name: Use AtlasCLI
        shell: bash
        run: atlas project ls # List projects available in your Atlas organization

```

Other examples available in [workflow/test.yml](.github/workflows/test.yml). 
## Configuration options

| Option       | Usage                                                               | Default value |
|--------------|---------------------------------------------------------------------|--------------|
| `version`    | Define the Atlas CLI version to use in the workflow. **Optional**   |`v1.5.1`      |
| `public-key` | The MongoDB Atlas Public Key. **Required**                          | none         |
| `private-key`| The MongoDB Atlas Private Key. **Required**                         | none         |
| `org-id`     | The MongoDB Atlas Organization to use in your workflow. **Optional**| none         |
| `project-id` | The MongoDB Atlas Project to use in your workflow. **Optional**     | none         |

## License
The scripts and documentation in this project are released under the [Apache License](LICENSE).
