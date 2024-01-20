# Table of Content

 - [What to do](#what-to-do)
 - [Sub-task 1: Testing strategy](#sub-task-1-testing-strategy)
 - [Sub-task 2: Perform different types of testing](#sub-task-2-perform-different-types-of-testing)

## What to do

In this module it is needed to adjust services with adding tests.

## Sub-task 1: Testing strategy

1) For solving this task, come up with a testing strategy and describe approach on how to ensure application stability and testing strategies:
 - Unit tests
 - Integration tests
 - Component tests
 - Contract tests
 - End-to-end tests
2) Describe it in a short document what approach was chosen and how the combination of the strategies would help to solve task, e.g., either it's going to be 100% **unit tests** and **integration tests** or something else.

## Sub-task 2: Perform different types of testing

1) _Unit tests_: choose JUnit or Spock and choose module that need to be tested.
2) _Integration tests_: choose JUnit or Spock and cover integration layers.
3) _Component tests_: cover component scenarios on a business level, specifying exact scenario or scenarios and expected outcomes in a natural language, preferably using the Cucumber framework.
4) _Contract tests_: cover all contracts that are used in a specific scenario, preferably using the [Spring Cloud Contract](https://spring.io/projects/spring-cloud-contract) or Pact (contract tests should cover BOTH communication styles: synchronous HTTP and messaging, including stubs propagation).
5) _End-to-end tests_: all scenarios should be described in a natural language. Focus is on coverage on the API layer. Cucumber testing framework can be used in this case with the component tests from above.

**Note**

 - At least one test should be executed for each test type. 
 - All tests should be integrated into the build process for later inclusion into the CI process.
