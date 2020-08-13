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
- [Adding a new service](#adding-a-new-service)
- [Writing Tests](#writing-tests)
- [Running Tests](#running-tests)
- [Encrypting secrets](#encrypting-secrets)
- [Code Coverage](#code-coverage)
- [Generating Javadocs](#generating-javadocs)
- [Publishing build artifacts](#publishing-build-artifacts)
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

## Adding a new service

This section will guide you through the steps to generate the Java code for a service
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
The value of this property should be the base package name associated with your
SDK project (e.g. `com.ibm.cloud.platform_services`).  All the generated service and model
classes for the project will be contained in packages whose names start with this base package name.
Here's an example:
```yaml
  info:
    x-codegen-config:
      java:
        apiPackage: 'com.ibm.cloud.platform_services'
```

By adding this configuration property to your API definition, you can avoid using the `--api-package`
command line option when running the SDK generator.

More details about SDK generator configuration properties can be found
[here](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Config-Options).

3. Next, run the SDK generator to process your API definition and generate the service and unit test
code for the service.

You'll find instructions on how to install and run the SDK generator on the
[generator repository wiki](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Usage-Instructions).

Set the output location for the generated files to be the `./modules` directory of the project.

If you did not configure the `apiPackage` configuration property in your API definition file(s), then
be sure to use the `--api-package <base-package-name>` command line option when running the generator to
ensure that source files are generated correctly.

Here is an example of how to generate the Java code for an API definition.
Suppose your API definition file is named `my-service.json` and contains the definition of the "My Service"
service.
To generate the code into your project, run these commands:
```sh
cd <project-root>

openapi-sdkgen.sh generate -g ibm-java -i my-service.json -o . --api-package <base-package-name>

```

The generated service and unit test code will be written to a **module** directory
underneath `./modules`.
The name of the module directory will reflect the service name.
For the example above, the module directory would be named `my-service`.
You will have one module directory underneath `./modules` for each service contained in your
project (plus the `common` and `coverage-reports` modules).

Hint: you can generate an initial integration test for your service by including the `--genITs` option 
when running the SDK generator. This will generate an integration test along with the service and 
unit test code within the module directory for the service. 
It is expected that the generated integration test is a starting point which will need manual editing 
to form an effective integration test for the service.

4. Copy the `service-pom.xml` file to `modules/<module-name>/pom.xml`, where `<module-name>` is
the name of the new module directory.
Edit this file and make these changes:
  - Replace `MODULE-ARTIFACTID` with the new module's artifactId (e.g. my-service)
  - Replace `MODULE-DESCRIPTION` with a suitable description for the module (e.g. "My Service").

5. Update the service table in the `README.md` file to add an entry for the new service.

6. Next, modify the parent `pom.xml` file to add an entry for the new service to the
`<modules>` element.

7. Next, modify the `coverage-reports` module's pom.xml to add a dependency entry for the new module so that its
test coverage information will be accounted for in the aggregated coverage reports.

8. Repeat the steps in this section for each service to be included in your project.

## Writing Tests
The Java integration tests use the TestNG testing framework.
With TestNG, you write methods containing your test code and annotate these methods with @Test.
You can create methods for setup and teardown of test infrastructure and annotate these
with a variety of `@Before` or `@After` annotations.
See the [TestNG Docs](https://testng.org/doc/) for full details.

## Running Tests
By default, when you run `mvn verify` (or `mvn package`), both unit and integration tests are run.

To run only the unit tests, run `mvn test -DskipITs=true`

To run the tests in a specific test class, use the `-Dtest` flag when invoking `mvn test`,
for example:  

```
mvn test -Dtest=SdkCommonTest
```

To run a specific test, add the name of the test method, for example:

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

## Publishing build artifacts
It is recommended that each Java SDK project publish its build artifacts (jars, pom.xml files, etc.) on
[JCenter](https://bintray.com/bintray/jcenter) and [Maven Central](https://central.sonatype.org/),
which are both public maven artifact repositories.

For details on how to accomplish this within your Java SDK project's build, look [here](JavaDeploy.md).

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
