# Actions_Deployments
GitHub reusable Workflows definition used to automate project deployments.

It checks whether desired runner is available and performs the Docker compose application in specified version.

To deploy particular version you need to have this version tagged and released.
Preferred way to automate Releases process is [Actions_Releases](https://github.com/HornaHomeLab/Actions_Releases)

### Requirements
Deployment workflow requires following variables configured for each deployment environment:
- `HOST` - name of the runner which is installed on the server to which deploy to (this runner has to have same string as label and it should be the only runner with this label)
- `APP_ROOT_DIR` - path where to create App's root directory
- `RUNNER_LABEL` - label for in-containers runners assigned for the same environment.

## Deployment process (`Run_docker_compose.yml`)
1. In parallel:
   - Checks if runner specified for chosen environment is available
   - Verifies if given version exists and collects necessary details about deployment
2. Checks existing folder structure on deployment server.
3. Stops currently running app's version (if it has been found).
4. Clones or checkouts to the specified version.
5. Builds Compose images
6. Starts Compose containers
7. Waits for containers HealthChecks

# Sample workflow

```YAML
name: Deployment
run-name: Deployment ${{ inputs.version }} to ${{ inputs.environment }}

on: 
  workflow_dispatch:
    inputs:
      environment:
        type: choice
        default: "development"
        options:
          - development
          - production
      version:
        type: string
        required: false
        default: "latest"


jobs:
  Deploy:
    uses: HornaHomeLab/Actions_Deployments/.github/workflows/Run_docker_compose.yml@main
    with:
      environment: ${{ inputs.environment }}
      version: ${{ inputs.version }}
    secrets: inherit
```