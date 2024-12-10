# Automated Test Framework (ATF)

- You can create and run automated tests to confirm that your instance works after making a change.
- Leveraging the power of ATF, you will be able to reduce the amount of manual testing required to deliver ServiceNow instance changes quickly and effectively.

> On average, manual testing consumed 43% of time and resources spent on upgrades

## Configure and Run Automated Tests

### Configure Automated Testing

- Before you can start creating your first tests and test suites, you need to enable Automated Test Framework (ATF) in the instance.

> **NOTE**: ATF should not be run in Production instances.

## Building Blocks

The important elements used to develop ATF testing include:

- Test suites
- Tests
- Test steps

### Test suites

- A test suite groups tests together that are typically performed one after the other.
- Test suites represent a grouping of one or more tests or other test suites.
- The same test can be included within multiple test suites. 
- However, a test suite can only be assigned one parent test suite.

### Tests

- An ATF test represents a set of actions and assertions to verify an expected end result.
- Each action or assertion within the test is represented by an individual test step.
- Although there is no limit to the number of test steps contained within a test, it is typically better to create smaller focused tests, rather than larger broad tests.

> When using an Agile development approach, an ATF test can be assigned to a Story.
>
> It represents an individual test case with a given acceptance criteria.

### Test steps

- A test step represents a single action or assertion within a test, such as creating a user, opening a form, or validating a field value.
- Different types of test steps are created using different test step configurations.
- A test step configuration defines the behavior and characteristics of each type of action or assertion.

#### Client and server

- Some of the test steps within a test are performed on the client user interface, while others are performed on the server.
- A single test can include both client and server actions and assertions.
- Test steps performed on the client simulate user interactions, such as opening a form.
- Test steps performed on the server execute in the background and cover a wide range of possibilities.
- The test steps within a test are run on the client or the server.
- Test steps performed on the client user interface are executed by a client test runner within a separate browser window.
- Test steps performed on the server execute against the instance.

- ATF test suites can either be run on-demand or scheduled.
- ATF tests are only run on-demand.


