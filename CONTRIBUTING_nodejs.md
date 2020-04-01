# Contributing

This file provides general guidance for anyone contributing to IBM Cloud Node.js SDK projects produced
by the IBM OpenAPI SDK Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      npx markdown-toc -i README.md
  -->

<!-- toc -->

  * [Questions](#questions)
  * [Coding Style](#coding-style)
  * [Commit Messages](#commit-messages)
  * [Pull Requests](#pull-requests)
  * [Running Tests](#running-tests)
    + [Unit Tests](#unit-tests)
    + [Integration Tests](#integration-tests)
    + [Running a Single Test](#running-a-single-test)
  * [Encrypting secrets](#encrypting-secrets)
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
7. Push to your fork and submit a pull request.
8. Be sure to sign the CLA.

## Running Tests
Out of the box, `npm run test` runs linting, unit tests, and integration tests (which require credentials).

### Unit Tests
To run only the unit tests (sufficient for most cases), use `npm run test-unit`.

### Integration Tests
To run integration tests, copy `test/resources/auth.example.js` to `test/resources/auth.js` and fill in
credentials for the service(s) you wish to test.

Currently this enables integration tests for all services so, unless all credentials are supplied, some tests will fail.
(This will be improved eventually.)

### Running a Single Test
To run only a specific test, pass the file name to `jest`. For example:

```
npm i -g jest
jest test/integration/example-service.v1.test.js
```

See [the jest documentation](https://jestjs.io/docs/en/cli) for all the options you can use to further configure `jest`.

## Encrypting secrets
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
- [Airbnb conventions](https://github.com/airbnb/javascript)
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)
- [Jest Documentation](https://jestjs.io/docs/en/cli)