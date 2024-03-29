# Contributing

This file provides general guidance for anyone contributing to IBM Cloud Node.js SDK projects produced
by the IBM OpenAPI SDK Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 CONTRIBUTING_nodejs.md
  -->

<!-- toc -->

  * [Questions](#questions)
  * [Coding Style](#coding-style)
  * [Commit Messages](#commit-messages)
  * [Pull Requests](#pull-requests)
  * [Adding a new service](#adding-a-new-service)
  * [Running Tests](#running-tests)
    + [Unit Tests](#unit-tests)
    + [Integration Tests](#integration-tests)
      - [Running all Integration Tests](#running-all-integration-tests)
      - [Running a Single Integration Test](#running-a-single-integration-test)
- [Developer's Certificate of Origin 1.1](#developers-certificate-of-origin-11)
- [Additional Resources](#additional-resources)

<!-- tocstop -->

## Questions
If you are having problems using the APIs or have a question about IBM Cloud services, please ask a question on
[dW Answers](https://developer.ibm.com/answers/questions/ask/?topics=ibm-cloud)
or [Stack Overflow](http://stackoverflow.com/questions/ask?tags=ibm-cloud).

## Coding Style
This SDK project follows a coding style based on the [Airbnb conventions](https://github.com/airbnb/javascript).
This repository uses `tslint` for linting the TypeScript code and `eslint` for linting the JavaScript test files.
The rules for each are defined in `tslint.json` and `test/.eslintrc.js`, respectively.
It is recommended that you do not change these files, since the automatically generated code complies with the defined rules.

You can run the linter with the following commands. Replacing “check” with “fix” will cause the linter to automatically fix any linting errors that it can.
- `npm run tslint:check`
- `npm run eslint:check`

## Commit Messages
Commit messages should follow the [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines).
This is because our release tool - [semantic-release](https://github.com/semantic-release/semantic-release) -
uses this format for determining release versions and generating changelogs.
Tools such as [commitizen](https://github.com/commitizen/cz-cli) or [commitlint](https://github.com/conventional-changelog/commitlint)
can be used to help contributors and enforce commit messages.
Here are some examples of acceptable commit messages, along with the release type that would be done based on the commit message:

| Commit message                                                                                                                                                              | Release type               |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
| `fix(example service): fix integration test to use correct credentials`                                                                                                 | Patch Release              |
| `feat(global catalog): add global-catalog service to project`                                                                                                               | ~~Minor~~ Feature Release  |
| `feat(global search): re-gen service code with new v3 API definition`<br><br>`BREAKING CHANGE: The global-search service has been updated to reflect version 3 of the API.` | ~~Major~~ Breaking Release |

## Pull Requests
If you want to contribute to the repository, follow these steps:  
1. Fork the repo.
2. Install dependencies: `npm install`
3. Build the code: `npm run build`
4. Verify the build before beginning your changes: `npm run test-unit`
5. Develop and test your code changes.
6. Commit your changes. Remember to follow the correct commit message guidelines.
7. Push to your fork and submit a pull request to the **main** branch.
8. Be sure to sign the CLA.

## Adding a new service

This section will guide you through the steps to generate the Node.js code for a service
and add the generated code to your SDK project.

1. Validate the API definition - before trying to process the API definition with the SDK generator, we strongly
recommend that you validate the API definition with the
[IBM OpenAPI Validator ](https://github.com/IBM/openapi-validator).
Example:
```
lint-openapi -s example-service.yaml
```
This command will display a list of errors and warnings found in the API definition
as well as a summary at the end.
It's not required that you fix all errors and warnings before trying to use the SDK generator, but
this step should identify any critical errors that will need to be fixed prior to the generation step.

2. Next, run the SDK generator to process your API definition and generate the service and unit test
code for the service.

You'll find instructions on how to install and run the SDK generator on the
[generator repository wiki](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Usage-Instructions).

Set the output location for the generated files to be the root directory of the project.

Here is an example of how to generate the Node.js code for an API definition.
Suppose your API definition file is named `my-service.json` and contains the definition of the "My Service"
service.
To generate the code into your project, run these commands:
```sh
cd <project-root>

openapi-sdkgen.sh generate -g ibm-node -i my-service.json -o .

```

The generated service code is placed in a source directory named after the service
(`my-service` for this example) under the project root.
You should have one source directory per service.

The unit test code is placed in `test/unit` with a filename that reflects the service
(`my-service.v1.test.js` for this example).

Hint: you can generate an initial integration test for your service by including the `--genITs` option
when running the SDK generator.   This will generate an integration test in the `test/integration` directory.
It is expected that the generated integration test is a starting point which will need manual editing to form 
an effective integration test for the service.

3. Update the service table in the `README.md` file to add an entry for the new service.

4. Update `.gitignore` to add an entry for your service to the `service-specific tsc outputs`, like this:  
```
# service-specific tsc outputs (js files)
my-service/*.js
```
This will ensure that the typescript build outputs are not inadvertently committed to the
git repository.

5. Repeat the steps in this section for each service to be included in your project.


## Running Tests
Out of the box, `npm run test` runs linting, unit tests, and integration tests (which require credentials).

### Unit Tests
To run only the unit tests (sufficient for most cases), use `npm run test-unit`.

### Integration Tests

#### Running all Integration Tests
To run all of the project's integration tests, you can run `npm run test-integration`.
Note that in order to run all of an SDK project's integration tests, you'll need to first make sure
that the required credentials files (one per service) are present and located in the project's root directory. 

#### Running a Single Integration Test
To run only a specific test, pass the file name to `jest`, like this example:

```
jest test/integration/example-service.v1.test.js
```

Note: if you do not yet have jest installed locally, you can run `npm i -g jest` to install it.

See [the jest documentation](https://jestjs.io/docs/en/cli) for all the options you can use to further configure `jest`.

# Developer's Certificate of Origin 1.1
By making a contribution to this project, I certify that:

(a) The contribution was created in whole or in part by me and I
   have the right to submit it under the open source license
   indicated in the file; or

(b) The contribution is based upon previous work that, to the best
   of my knowledge, is covered under an appropriate open source
   license and I have the right under that license to submit that
   work with modifications, whether created in whole or in part
   by me, under the same open source license (unless I am
   permitted to submit under a different license), as indicated
   in the file; or

(c) The contribution was provided directly to me by some other
   person who certified (a), (b) or (c) and I have not modified
   it.

(d) I understand and agree that this project and the contribution
   are public and that a record of the contribution (including all
   personal information I submit with it, including my sign-off) is
   maintained indefinitely and may be redistributed consistent with
   this project or the open source license(s) involved.

# Additional Resources
- [General GitHub documentation](https://help.github.com/)
- [GitHub pull request documentation](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)
- [Airbnb conventions](https://github.com/airbnb/javascript)
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)
- [Jest Documentation](https://jestjs.io/docs/en/cli)
