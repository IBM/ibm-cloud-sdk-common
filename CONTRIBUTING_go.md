This file provides general guidance for anyone contributing to IBM Cloud Go SDK projects produced
by the IBM OpenAPI SDK Generator.

# Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      npx markdown-toc -i README.md
  -->

<!-- toc -->

- [Questions](#questions)
- [Code](#code)
  * [Coding Style](#coding-style)
  * [Commit Messages](#commit-messages)
- [Pull Requests](#pull-requests)
- [Running tests](#running-tests)
  * [Unit tests](#unit-tests)
  * [Integration tests](#integration-tests)
- [Encrypting secrets](#encrypting-secrets)
- [Developer's Certificate of Origin 1.1](#developers-certificate-of-origin-11)
- [Additional Resources](#additional-resources)

<!-- tocstop -->

# Questions
If you are having problems using the APIs or have a question about IBM Cloud services, please ask a question on
[dW Answers](https://developer.ibm.com/answers/questions/ask/?topics=ibm-cloud)
or [Stack Overflow](http://stackoverflow.com/questions/ask?tags=ibm-cloud).

# Code
## Coding Style
The SDK follows the Go coding conventions documented [here](https://golang.org/doc/effective_go.html).
You can run the linter with the following command:
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

# Pull Requests
If you want to contribute to the repository, follow these steps:  
  1. Fork the repository
  2. Develop and test your code changes:  
    - To build/test: `go test ./...`
  3. Please add one or more tests to validate your changes.
  4. Make sure everything builds/tests cleanly
  5. Commit your changes  
  6. Push to your fork and submit a pull request to the **master** branch

# Running tests
The tests within the SDK consist of both unit tests and integration tests.

## Unit tests
Unit tests exercise the SDK function with local "mock" service endpoints.

To run all the unit tests contained in the project:
- `go test ./...`

To run a unit test for a specific package within the SDK project:
- `cd <package-dir>` (e.g. `cd globalsearchv2`)
- `go test`

## Integration tests
Integration tests use actual service endpoints deployed in IBM Cloud, and therefore require the appropriate
credentials.

To run all integration tests contained in the project:
- Make sure you have provided the appropriate credentials for all the services
- `go test ./... -tags=integration`

To run the integration test for a single service:
- Make sure you have provided the appropriate credentials for the service being tested
- `cd <package-dir>` (e.g. `cd globalsearchv2`)
- `go test -tags=integration`

# Encrypting secrets
To run integration tests within a Travis build, you'll need to encrypt the file containing the
required external configuration properties.
For details on how to do this, please see [Encrypting Secrets](EncryptingSecrets.md).

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
- [Effective Go](https://golang.org/doc/effective_go.html)
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)