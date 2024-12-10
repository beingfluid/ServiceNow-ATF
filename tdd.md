# What is Test-Driven Development (TDD)?

> Programmers shouldn’t have to guess whether software is working correctly.
>
>They should be able to prove it, every step of the way.

## TDD Approach

> Test-Driven Development (TDD) is a software development approach where tests are written before the actual code.

- In TDD, you write test cases before writing the actual code.
- The main goal of TDD is to improve the quality of the software and ensure that the code meets the requirements.

## Test-Code-Refactor Cycle

> Test-driven development (TDD) is a software development process that relies on the repetitive cycle of writing

- The fundamental cycle in TDD involves writing a failing test case, writing code to pass the test, and then refactoring the code to improve its structure without changing its behavior.

- These three steps, often referred to as the "Red-Green-Refactor" cycle:
    1. **Red**: Write a test for a new feature or functionality.
        - The test should fail because the feature is not yet implemented.
    2. **Green**: Write the minimum amount of code necessary to make the test pass.
        - The focus is on getting the test to pass, not on writing perfect code.
    3. **Refactor**: Clean up the code while ensuring that all tests still pass.

    > Refactoring is a disciplined technique for restructuring an existing body of code, altering its internal structure without changing its external behavior.
    >
    > ~ Martin Fowler (Author, Refactoring)

    - This step involves improving the code structure, readability, and performance without changing its behavior.
    - Refactoring aims to make code easier to read and cheaper to modify.
    - It's done when code becomes redundant, hard to read, or doesn't feel right.
    - For instance, if a method's implementation does more than its intention, it should be refactored.
        - **Duplicated Code**: Same code appears in multiple places.
        - **Long Methods**: Methods that are too long and do too much.
        - **Large Classes**: Classes that have too many responsibilities.
        - **Inconsistent Naming**: Variables and methods that are not named clearly or consistently.
    - This technique involves splitting a method into smaller methods to align its implementation with its intention, improving code maintainability.
    - There are no strict rules on when to refactor.

    > I often notice that the resulting code, while it works, isn't as clear as it could be. I then refactor it into a better shape so that when I (or someone else) return to this code in a few weeks time, I won't have to spend time puzzling out how this code works.
    >
    > If I struggle to understand this code, I refactor it so I won't have to struggle again next time I look at it.
    >
    > If there's some functionality buried in there that I need, I refactor so I can easily use it.
    >
    > ~ Martin Fowler (Author, Refactoring)

    > "Any fool can write code that a computer can understand. Good programmers write code that humans can understand."
    >
    > ~ Martin Fowler (Author, Refactoring)

## Unit Testing Focus

- TDD primarily involves unit testing, which tests the smallest units of code.
- It does not cover higher-level testing like system or integration testing.

### Regression testing

- The comprehensive testing plans to ensure existing processes still work as expected following an upgrade or development cycle.
- ATF is great for regression testing.

## Benefits of TDD

- **Improved Code Quality**: Writing tests first helps clarify requirements and leads to better-designed code.
- **Fewer Bugs**: Since tests are written for every piece of functionality, TDD helps catch bugs early in the development process.
- **Documentation**: Tests serve as documentation for the code, making it easier for new developers to understand the intended behavior.
- **Confidence in Refactoring**: With a comprehensive suite of tests, developers can refactor code with confidence, knowing that any regressions will be caught by the tests.

## TDD Best Practices

> Test-Driven Development is a powerful methodology that can lead to higher-quality software and a more efficient development process.
>
>By following the Red-Green-Refactor cycle and adhering to best practices, developers can create robust, maintainable code that meets user requirements.

- **Write Small Tests**: Focus on small, specific tests that cover individual pieces of functionality.
- **Keep Tests Independent**: Ensure that tests do not depend on each other to avoid cascading failures.
- **Run Tests Frequently**: Run the test suite often to catch issues early and ensure that new changes do not break existing functionality.
- **Use Descriptive Test Names**: Write clear and descriptive names for tests to convey their purpose and expected behavior.
- **Starting with a Failing Test**: TDD begins with writing a failing test case based on the acceptance criteria of the use cases or user stories.

## Test structure

### Test Class and Code Class Relationship

- **One Test Class per Code Class**: Initially, start with one test class per code class.
- **Splitting Classes**: As your code grows, you might need to split your code class into multiple classes to keep it manageable.

### Arrange, Act, Assert Pattern in Test Cases

Each test case typically follows this pattern:

1. **Arrange**: Set up the elements required for the test.
2. **Act**: Perform the action you want to test.
3. **Assert**: Verify that the result is as expected.
    - Assertions are used in programming to ensure that a certain condition is true at a specific point in the program.
    - If the condition is false, an assertion error is thrown.

### Avoiding Code Repetition

#### Test Fixtures

To avoid repeating setup code across multiple test cases, you can use test fixtures.  
These are methods that set up a fixed state for your tests:

- **@BeforeEach**: An annotation for code that is to be executed before every test
- **@AfterEach**: An annotation for code that is to be executed after every test

#### Shared Fixtures

- **@BeforeAll**: An annotation for code that is to be executed before all tests
- **@Afterall**: An annotation for code that is to be executed after all tests are completed

## Test doubles

> Test doubles are used to simulate the behavior of external dependencies (like databases or web services) that may not be available or ready during testing.

- They help ensure your tests can run independently of these dependencies.
- They are used to replace dependencies in your code with a mock object that behaves like the real dependency.

### Mock Objects

- These are special test doubles pre-programmed with expectations about the calls they should receive.
- They simulate the behavior of external dependencies.
- **Example**: If your code relies on a database, a mock object can simulate the database's behavior, such as returning specific values for certain keys or throwing exceptions for invalid inputs.

### Spy

- A spy is a test double that records the calls it receives and allows you to verify the behavior
- A test double variable used to check that, for example, some event has occurred or count how many times something has happened

### Stub

> A test double method that returns a value, feeding desired inputs into the tests rather than reflecting real behavior

- A stub is used to replace a dependency with a fixed value
- **Example**: If your code relies on a
A test double method that returns a value, feeding desired inputs into the tests rather than reflecting real behavior
- **Exammple**: A method that simply returns true.

#### Dependency Injection

> This is a design pattern that allows you to pass dependencies to your code instead of hardcoding them

- Pass the mock object to your code under test to simulate the external dependency.
- **Example**: If your code relies on a database, you can pass a mock database object to your code instead of using the real database.

## Further Reading

1. [Programming Foundations: Test-Driven Development](https://www.linkedin.com/learning/programming-foundations-test-driven-development-3)
2. [Refactoring](https://martinfowler.com/books/refactoring.html)
3. [Continuous Integration and Continuous Delivery (CI/CD) Fundamentals - v](https://nowlearning.servicenow.com/lxp/en/now-platform/continuous-integration-and-continuous-delivery-ci-cd?id=learning_course_prev&course_id=40079838934bf5582fac74096cba1046)
4. [Source Control Fundamentals](https://nowlearning.servicenow.com/lxp/en/now-platform/source-control-fundamentals?id=learning_course_prev&course_id=e55406cc47fa7954db63fb25126d43dc)
5. [Setting up a pipeline using CI/CD APIs (GitHub example)](https://developer.servicenow.com/connect.do#!/event/knowledge2021/CCL1049-K21)
6. [Automate your CI/CD Pipeline using Github Actions](https://developer.servicenow.com/blog.do?p=/post/cicd-pipeline-github-actions)
