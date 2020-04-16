# Contributing

This file provides general guidance for anyone contributing to IBM Cloud Python SDK projects produced
by the IBM OpenAPI SDK Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 CONTRIBUTING_python.md
  -->

<!-- toc -->

- [Questions](#questions)
- [Coding Style](#coding-style)
- [Commit Messages](#commit-messages)
- [Pull Requests](#pull-requests)
- [Running the Tests](#running-the-tests)
  * [Unit tests](#unit-tests)
  * [Integration tests](#integration-tests)
- [Encrypting secrets](#encrypting-secrets)
- [Code Coverage](#code-coverage)
- [Developer's Certificate of Origin 1.1](#developers-certificate-of-origin-11)
- [Additional Resources](#additional-resources)

<!-- tocstop -->

## Questions
If you are having problems using the APIs or have a question about IBM Cloud services,
please ask a question at
[Stack Overflow](http://stackoverflow.com/questions/ask?tags=ibm-cloud).

## Coding Style
This SDK follows a coding style based on the [PEP8 Style Guide for Python](https://www.python.org/dev/peps/pep-0008/),
with the following modifications:
- four spaces instead of tabs for indentation

All non-trivial methods should have docstrings.
Docstrings should follow the [PEP257 guidelines](https://www.python.org/dev/peps/pep-0257/).
For more examples, see the [Google style guide](https://google.github.io/styleguide/pyguide.html#381-docstrings)
regarding docstrings.

Use [PyLint](https://www.pylint.org/) to adhere to these guidelines.

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
If you want to contribute to the repository, here's a quick guide:
  1. Fork the repository and clone your fork to your local environment
  2. (recommended) Install and activate a [virtual environment](https://docs.python.org/3/tutorial/venv.html):  
     * `python -m venv <venv-dir>`, where `<venv-dir>` indicates where to install the virtual environment
     * `. <venv-dir>/bin/activate`
  3. Install the project as an editable package using the current source:  
     * `pip install -e .`
  4. Install dependencies:  
      * `pip install -r requirements.txt`
      * `pip install -r requirements-dev.txt`
  5. Develop and test your code changes:  
      * Please add one or more tests to validate your changes.
      * To run all the tests: `pytest`
      * You can find more details about running the tests below.
  6. Check and fix code style: `./pylint.sh`
  7. Make sure everything builds/tests cleanly
  8. Commit your changes  
     * Make sure your commit messages follow the Angular Commit Message Guidelines (see below).
  9. Push to your fork and submit a pull request to the **master** branch


## Running the Tests

To run all of the tests (both unit and integration tests):  
* `pytest`

Note that integration tests require credentials - see below.

You can run a specific test like this:
* `pytest <path-to-test>/mytest.py`

### Unit tests

Unit tests use a mock service endpoint, so they do not need any credentials.
To run the unit tests:  
* `pytest test/unit`

### Integration tests
Integration tests use an actual service endpoint, so you need to provide credentials to the integration test framework.

The integration test framework will skip integration tests for any service that does not have credentials.

To provide credentials for the integration tests, export your credentials as environment variables.

To run only the integration tests:
* `pytest test/integration`

## Encrypting secrets
To run integration tests within a Travis build, you'll need to encrypt the file containing the
required external configuration properties.
For details on how to do this, please see [Encrypting Secrets](EncryptingSecrets.md).

## Code Coverage
This project uses [coverage](https://pypi.org/project/coverage/) to measure code coverage.
To obtain a code coverage report, run `coverage run` from the root of the project,
and then view the coverage report from the `coverage report` command.

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
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-guidelines)
- [Virtual Environment](https://docs.python.org/3/tutorial/venv.html)
- [PEP8 Style Guide for Python](https://www.python.org/dev/peps/pep-0008/)
- [PEP257 Guidelines](https://www.python.org/dev/peps/pep-0257/)
- [Google Style Guide](https://google.github.io/styleguide/pyguide.html#381-docstrings)
- [PyLint](https://www.pylint.org/)
- [coverage](https://pypi.org/project/coverage/)
- [semantic-release](https://github.com/semantic-release/semantic-release)
- [commitizen](https://github.com/commitizen/cz-cli)
- [commitlint](https://github.com/conventional-changelog/commitlint)
