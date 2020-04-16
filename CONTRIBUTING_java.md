# Contributing

This file provides general guidance for anyone contributing to IBM Cloud Java SDK projects produced
by the IBM OpenAPI SDK Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 CONTRIBUTING_java.md
  -->

<!-- toc -->

- [Questions](#questions)
- [Coding Style](#coding-style)
- [Commit Messages](#commit-messages)
- [Pull Requests](#pull-requests)
- [Running Tests](#running-tests)
- [Encrypting secrets](#encrypting-secrets)
- [Code Coverage](#code-coverage)
- [Generating Javadocs](#generating-javadocs)
- [Developer's Certificate of Origin 1.1](#developers-certificate-of-origin-11)
- [Additional Resources](#additional-resources)

<!-- tocstop -->

## Questions
If you are having problems using the SDK or have a question about IBM Cloud services,
please ask a question at
[Stack Overflow](http://stackoverflow.com/questions/ask).

## Coding Style
This SDK follows a coding style based on the [Google Java coding style][GoogleJavaStyleGuidelines],
with the following modifications:
- Max line length is 120 chars
- Only basic JavadocStyle is enforced
- Import ordering is not enforced
- VariableDeclarationUsageDistance is not enforced
- OverloadMethodsDeclarationOrder is not enforced

[GoogleJavaStyleGuidelines]: https://google.github.io/styleguide/javaguide.html

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
If you want to contribute to the repository, here's a quick guide:
  1. Fork the repository.
  2. Develop and test your code changes:
      * Follow the coding style as documented above
      * To build/test: `mvn test`
      * Please add one or more tests to validate your changes.
  3. Make sure everything builds/tests cleanly.
      * The build will run the code style checker and flag any issues.
  4. Commit your changes  
  5. Push to your fork and submit a pull request to the **master** branch.
  6. Be sure to sign the CLA.

## Running Tests
By default, when you run `mvn test` only unit tests are run.   Integration tests are skipped
because they require credentials.

To run the integration tests, you need to provide credentials to the integration test framework, then
run `mvn test -DskipITs=false`.

To run the tests in a specific test class, use the `-Dtest` flag when invoking `mvn test`, e.g.:

```
mvn test -Dtest=SdkCommonTest
```

You can run a specific test by adding the name of the test method, e.g.:

```
mvn test -Dtest=SdkCommonTest#testGetSdkHeaders
```

## Encrypting secrets
To run integration tests within a Travis build, you'll need to encrypt the file containing the
required external configuration properties.
For details on how to do this, please see [Encrypting Secrets](EncryptingSecrets.md).

## Code Coverage
This repo uses [Jacoco][jacoco] to measure code coverage. To obtain a code coverage report, run `mvn clean verify`
from the root of the project, and then view the coverage report in the `modules/coverage-reports/target` directory:
```
open modules/coverage-reports/target/site/jacoco-aggregate/index.html
```

[jacoco]: https://www.eclemma.org/jacoco/

## Generating Javadocs
To generate the Javadocs for the project, run `mvn site` and then view the docs in the `target/site/apidocs` directory:
```
open target/site/apidocs/index.html
```

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
- [Maven Getting Started](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
- [GoogleJavaStyleGuidelines][GoogleJavaStyleGuidelines]
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)