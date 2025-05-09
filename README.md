# UiPath-Test

GitHub Action for running all publishable test cases in UiPath projects. Detailed test results are provided in json format from this action. They can also be found by navigating to the Testing tab in UiPath Orchestrator. Built as a wrapper around the [UiPath CLI task for running tests from a UiPath project.](https://docs.uipath.com/cicd-integrations/standalone/2024.10/user-guide/testing-a-packagerunning-a-test-set)

The action triggers test cases to be run by robots through UiPath Orchestrator and writes the test result outputs to the [GitHub Actions job summary](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#adding-a-job-summary). See the setup section below on requirements for running test cases.

## Setup

This action requires the following things to be set up in UiPath Orchestrator:

- UiPath CLI installed on GitHub Actions Runner. This can be done by running the [setup-uipath action](https://github.com/Mikael-RnD/setup-uipath) before this action
- Test robots connected to the target folder/tenant in UiPath Orchestrator
- [An external application created in Orchestrator](https://docs.uipath.com/automation-cloud/automation-cloud/latest/admin-guide/managing-external-applications) with the access scopes specified in the [UiPath CLI documentation](https://docs.uipath.com/cicd-integrations/standalone/2024.10/user-guide/executing-uipath-cli-tasks#api-access-application-scopes). With the credentials passed to this actions input from GitHub Secrets (or other safe credential stores)

## Example usage

### Minimum required inputs

Using the minimum required inputs to this action assumes that the action is intended to run tests for all projects in the repository, targeting a tenant/organization within UiPath Automation Cloud, with the credentials for an external application that has been configured with the default application scopes noted [here.](https://docs.uipath.com/cicd-integrations/standalone/2024.10/user-guide/executing-uipath-cli-tasks#api-access-application-scopes)

```yml
      # Run all publishable unit tests from UiPath projects in this repository, targeting an organization and tenant in UiPath Automation Cloud
      - name: UiPath Test
        uses: RPA-Global/UiPath-Test@v1
        with:
          orchestratorTenant: TestTenant
          orchestratorFolder: Finance/SE
          orchestratorApplicationId: ${{ secrets.ORCHESTRATOR_APP_ID }}
          orchestratorApplicationSecret: ${{ secrets.ORCHESTRATOR_APP_SECRET }}
          orchestratorLogicalName: testorg
```

### All inputs used

The example below illustrates how the action can be used for a repository of multiple UiPath projects, where two specific projects are intended to be tested. This example also illustrates the inputs needed when targeting a non-Automation Cloud Orchestrator setup.

```yml

      # Run all publishable unit tests from the projects Perfomer/project.json and Dispatcher/project.json, targeting an Orchestrator instance in an internal environment 
      - name: UiPath Test
        uses: RPA-Global/UiPath-Test@v1
        with:
          projectFilePaths: |
            Performer/project.json
            Dispatcher/project.json
          orchestratorUrl: https://mycompany.orchestrator.com/
          orchestratorTenant: TestTenant
          orchestratorFolder: IT/Special
          orchestratorApplicationId: ${{ secrets.ORCHESTRATOR_APP_ID }}
          orchestratorApplicationSecret: ${{ secrets.ORCHESTRATOR_APP_SECRET }}
          orchestratorApplicationScope: "OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSets OR.TestSetExecutions OR.TestSetSchedules OR.Users.Read"
          orchestratorLogicalName: myorg

```

## Inputs

|Name|Description|Required|Default value|Example value|
|:--|:--|:--|:--|:--|
|**projectFilePaths**|Multiline input containing a list of projects to perform the operations on. If left empty, the action scans for any project.json files in the repository|False||TheProject/project.json|
|**orchestratorUrl**|Base URL to Orchestrator instance|False|<https://cloud.uipath.com/>|<https://mycompany.orchestrator.com/>|
|**orchestratorTenant**|Name of the Orchestrator tenant|True||TestTenant|
|**orchestratorLogicalName**|Id of the UiPath organization|True||testorg|
|**orchestratorFolder**|The fully qualified name of the Orchestrator folder where processes are deployed to|True||Finance/SE|
|**orchestratorApplicationId**|Application ID for the CLI to authenticate with UiPath Orchestrator|True||${{ secrets.ORCHESTRATOR_APP_ID }}|
|**orchestratorApplicationSecret**|Application Secret for the CLI to authenticate with UiPath Orchestrator|True||${{ secrets.ORCHESTRATOR_APP_SECRET }}|
|**orchestratorApplicationScope**|External application scope|False|"OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSets OR.TestSetExecutions OR.TestSetSchedules OR.Users.Read"||

## Outputs

|Name|Description|
|:--|:--|
|**testExecutionLinks**|Comma-separated list of URLs to directly access the test execution(s) triggered by this action|
|**testResults**|Markdown formatted table listing the tests that have been run and whether they passed or failed. Each project tested gets its table with a link to the test execution(s) in Orchestrator as part of its header. If no test cases were found for a project, nor set as publishable, a warning message is added.|
|**containsPublishableTestCases**|Boolean value indicating whether any test cases set as publishable were found in the repository|
|**testResultsFolder**|Path to the folder where the test result JSON files created by the UiPath CLI are stored. Can be used for further processing, such as uploading to Artifacts or sending to another action.|
