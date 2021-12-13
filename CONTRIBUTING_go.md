# Contributing

This file provides general guidance for anyone contributing to IBM Cloud Go SDK projects produced
by the IBM OpenAPI SDK Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 CONTRIBUTING_go.md
  -->

<!-- toc -->

- [Questions](#questions)
- [Coding Style](#coding-style)
- [Commit Messages](#commit-messages)
- [Pull Requests](#pull-requests)
- [Adding a new service](#adding-a-new-service)
- [Running tests](#running-tests)
  * [Unit tests](#unit-tests)
  * [Integration tests](#integration-tests)
- [Developer's Certificate of Origin 1.1](#developers-certificate-of-origin-11)
- [Additional Resources](#additional-resources)

<!-- tocstop -->

## Questions
If you are having problems using the APIs or have a question about IBM Cloud services,
please ask a question at
[Stack Overflow](http://stackoverflow.com/questions/ask?tags=ibm-cloud).

## Coding Style
The SDK follows the Go coding conventions documented [here](https://golang.org/doc/effective_go.html).
You can run the [linter](https://github.com/golangci/golangci-lint) with the following command:
- `golangci-lint run`

## Commit Messages
Commit messages should follow the [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines).
This is because our release tool - [semantic-release](https://github.com/semantic-release/semantic-release) -
uses this format for determining release versions and generating changelogs.
Tools such as [commitizen](https://github.com/commitizen/cz-cli) or [commitlint](https://github.com/conventional-changelog/commitlint)
can be used to help contributors and enforce commit messages.
Here are some examples of acceptable commit messages, along with the release type that would be done based on the commit message:

| Commit message                                                                                                                                                              | Release type               |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
| `fix(resource controller): fix integration test to use correct credentials`                                                                                                 | Patch Release              |
| `feat(global catalog): add global-catalog service to project`                                                                                                               | ~~Minor~~ Feature Release  |
| `feat(global search): re-gen service code with new v3 API definition`<br><br>`BREAKING CHANGE: The global-search service has been updated to reflect version 3 of the API.` | ~~Major~~ Breaking Release |

## Pull Requests
If you want to contribute to the repository, follow these steps:  
  1. Fork the repository
  2. Develop and test your code changes:  
    - To build/test: `go test ./...`
  3. Please add one or more tests to validate your changes.
  4. Make sure everything builds/tests cleanly
  5. Commit your changes  
  6. Push to your fork and submit a pull request to the **main** branch

## Adding a new service

This section will guide you through the steps to generate the Go code for a service
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

2. **Recommended**: Modify your API definition to configure the `apiPackage` property.
The value of this property should be the module import path for your SDK project
(e.g. `github.com/IBM/platform-services-go-sdk`).
Here's an example:
```yaml
  info:
    x-codegen-config:
      go:
        apiPackage: 'github.com/IBM/platform-services-go-sdk'
```

By adding this configuration property to your API definition, you can avoid using the `--api-package`
command line option when running the SDK generator.

More details about SDK generator configuration properties can be found
[here](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Config-Options).

3. Next, run the SDK generator to process your API definition and generate the service and unit test
code for the service.

You'll find instructions on how to install and run the SDK generator on the
[generator repository wiki](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Usage-Instructions).

Set the output location for the generated files to be the root directory of the project.

If you did not configure the `apiPackage` configuration property in your API definition file(s), then
be sure to use the `--api-package <module-import-path>` command line option when running the SDK generator to
ensure that source files are generated correctly for your project.

Here is an example of how to generate the Go code for an API definition.
Suppose your API definition file is named `my-service.json` and contains the definition of the "My Service"
service.
To generate the code into your project, run these commands:
```sh
cd <project-root>

openapi-sdkgen.sh generate -g ibm-go -i my-service.json -o . --api-package <module-import-path>

```
The generated service and unit test code will be written to a **package** directory under your
project root directory that reflects the Go package name associated with the service.
For the example above, the package directory would be named `myservicev1`.  You should have a service
package directory for each of the services contained in your project, plus the `common` directory for
the "common" package.

Hint: you can generate an initial integration test for your service by including the `--genITs` option
when running the SDK generator.   This will generate an integration test along with the service and
unit test code within the package directory for the service.  It is expected that the generated integration
test is a starting point which will need manual editing to form an effective integration test for the service.

4. Update the service table in the `README.md` file to add an entry for the new service.

5. Repeat the steps in this section for each service to be included in your project.

## Running tests
The tests within the SDK consist of both unit tests and integration tests.

### Unit tests
Unit tests exercise the SDK function with local "mock" service endpoints.

To run all the unit tests contained in the project:
- `go test ./...`

To run a unit test for a specific package within the SDK project:
- `cd <package-dir>` (e.g. `cd globalsearchv2`)
- `go test`

### Integration tests
Integration tests use actual service endpoints deployed in IBM Cloud, and therefore require the appropriate
credentials.

To run all integration tests contained in the project:
- Make sure you have provided the appropriate credentials for all the services
- `go test ./... -tags=integration`

To run the integration test for a single service:
- Make sure you have provided the appropriate credentials for the service being tested
- `cd <package-dir>` (e.g. `cd globalsearchv2`)
- `go test -tags=integration`

## Developer's Certificate of Origin 1.1
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

## Additional Resources
- [General GitHub documentation](https://help.github.com/)
- [GitHub pull request documentation](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)
- [Effective Go](https://golang.org/doc/effective_go.html)
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)
