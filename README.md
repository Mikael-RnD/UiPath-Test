# UiPath-Test
GitHub Action for running all publishable test cases in UiPath projects. Detailed test results are provided in json format from this action. They can also be found by navigating to the Testing tab in UiPath Orchestrator. Built as a wrapper around the [UiPath CLI task for running tests from a UiPath project](https://docs.uipath.com/test-suite/automation-suite/2022.10/user-guide/executing-tasks-cli#testing-a-package%2Frunning-a-test-set)

Outputs: 
- testExecutionLinks - Comma-separated list of URLs to directly access the test execution(s) triggered by this action
- testResults - Markdown formatted table listing the tests that have been run and whether they passed or failed

Example usage:

      # Run all publishable unit tests from UiPath in this repository
      - name: UiPath Test
        uses: RPA-Global/UiPath-Test@main
        with:
          # All inputs are required
          orchestratorUrl: # Link to UiPath Orchestrator instance
          orchestratorTenant: # Name of tenant where packages are deployed
          orchestratorFolder: # Orchestrator Folder path where packages are deployed
          orchestratorApplicationId: # Applicaiton id for external application
          orchestratorApplicationSecret: # Application secret for external application
          orchestratorApplicationScope: # External application scope
          orchestratorLogicalName: # Logical name for Orchestrator tenant
