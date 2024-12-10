# Continuous Integration and Continuous Delivery (CI/CD)

> Continuous Integration and Continuous Delivery (CI/CD) is a software development practice that aims to improve the quality and speed of software development by integrating and testing code changes frequently.

- Cl and CD stands for Continuous Integration and Continuous Delivery.
    1. **Continuous Integration (CI)**: This involves automating the build and test process for code changes, ensuring that the code is integrated and tested frequently.
    2. **Continuous Delivery (CD)**: This involves automating the deployment of software changes to production environments, ensuring that the software is delivered quickly and reliably.

> In the software world, the CI/CD pipeline refers to the automation that enables incremental code changes from developers’ desktops to be delivered quickly and reliably to production.

## Why is CI/CD Important?

- CI/CD allows organizations to release software quickly and efficiently.
- CI/CD facilitates an effective process for getting products to market faster than ever before, continuously delivering code into production, and ensuring an ongoing flow of new features and bug fixes via the most efficient delivery method.

## ServiceNow CI/CD

- Continuous Integration and Continuous Delivery (CI/CD) allows you to develop apps at scale with source control and continuous integration and continuous delivery (CI/CD) toolsets on the Now Platform.
- ServiceNow CI/CD GitHub Actions create CI/CD pipelines that automates your validation and deployment of your applications based on Source Control branching mechanics!
- GitHub Action pipeline automatically run your ATF tests!
- In ServiceNow we define a Continuous Integration (CI) as deploying the codebase (updates) to a test system and executing automated test runs via Automated Test Framework.

## Source Control

- Source control is a key component of the CI/CD pipeline.
- It allows developers to work on different versions of the codebase without affecting the production environment.
- Developers use source control to track and manage changes to an application.
- The **source** in source control refers to the files that make up an application.
- In ServiceNow, application files are database tables, scripts, flows, access control records, and other files that make up an application.
- **Control** is the management of the application files.
- Ultimately, source control enables you to leverage **branching** best practices to enable better work in code isolation for your developers as they develop on the Now platform.

### Source Control Benefits

- **Backup**: Store application files separately from the development platform.
- **Change tracking**: View changes and access past versions of an application file.
- **Version control**: Revert to a previous version of an application.
- **Parallel development**: Teams of developers can work on an application at the same time.
- **Code organization**: Maintain in-progress work separately from a released application and merge completed application files when development is complete.

## Environment Development Model

### With Update Sets

- One approach to deploying code changes and configuration changes in the ServiceNow environment is using updates sets down your instance chain and cloning back the instances once you have done that to match the environment.
- All of this is a fairly time-consuming process and to a certain extent, cumbersome, since there are a lot of manual steps such as previewing update sets, committing update sets, working through collisions, etc.
- If you have a large development team it can be hard to really separate out, or branch out, different features that will be built in parallel, which slows down the development lifecycle.

### With Continuous Integration and Continuous Delivery (CI/CD)

- For a model using source control branching, as well as leveraging the CI and CD pipeline, we will use a GitHub Flow.

#### Source Control Branching

- The master or main branch of your code represents a version of the application that is deployed to a production environment.
- Every time you are ready to work on a new story from a backlog, without a feature or fix, you will be checking out a new branch from the master branch as a feature branch and doing your work on that feature branch.
- Once your feature branch has been merged into your master branch, you can trigger another build and deploy, so this time you use a continuous delivery pipeline to again validate all the changes on your master branch before deploying to production if your build passes.

## CI/CD APIs

- ServiceNow delivers CI/CD APIs covering a variety of mechanics to enable customers to build out their pipelines.
- Starting from the source control capabilities, you can apply remote changes to ensure the branch on an instance reflects all of the commits that have already been pushed up to the repo regardless of which instance, or where those changes are coming from for that branch.
- There are also API mechanics, so you can also publish to the app repo, install from an app repo to an instance, and even roll back an application to a prior version using the APIs.
- Lastly, in order to run your ATF test suites to get automated validation, as well as to run instance scans, there are APIs to trigger both of these as long as they get progress results, so that you can inform the build whether it was passing or failing, based on the results of the ATF test and instance scan suites.
- By chaining all of these together, you can enable a CI/CD pipeline that matches your particular organization and team's workflows and processes.

## GitHub

> GitHub is a **code hosting platform** for **version control and collaboration**.
>
> It lets you and others **work together on projects** from anywhere.

- Git is one of the most popular source control platforms available.
- ServiceNow supports Git source control repositories.
- You will link your scoped application code with a Git repository (repo).
- A GitHub repository stores the application files that have been committed for an application.
- GitHub serves as a comprehensive platform that not only allows you to store your code as source control but also enables you to execute actions and pipelines.
    - GitHub Actions: build steps
    - Workflows: pipelines
- By utilizing these distinct actions, GitHub offers the flexibility to have multiple pipelines.
- For example, you can have two GitHub action pipelines.
    - The first pipeline is for continuous integration, where tests will be run against your Dev instance.
    - The second pipeline is for continuous delivery, which involves publishing and deploying your application to the production environment.
- Additionally, having your GitHub repository stored in one location allows for easier configuration of triggers and connecting pipelines to specific branches and requests.

### Git Terminology

- **Repository**: The location where the source files are stored.
    - It's a place where you can store your code, your files, and each file's revision history.
    - **Remote repository**: The remote Git repository where source files are stored and managed.
    - **Local repository**: The working repository in Studio where developers create and change application files.
- **Branch**: A set of code changes in a repository with a unique name.
- **Main**: The default branch, or trunk, of an application.

> Applications are created in Studio, the ServiceNow Integrated Development Environment (IDE).
>
> Developers manage the local repository and can link an application to a remote repository in Studio.
