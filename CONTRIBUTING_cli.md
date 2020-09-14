# Contributing

This file provides general guidance for anyone contributing to IBM Cloud CLI Plugin projects produced
by the IBM OpenAPI SDK/CLI Generator.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 CONTRIBUTING_cli.md
  -->

<!-- toc -->

- [Questions](#questions)
- [Coding Style](#coding-style)
- [Pull Requests](#pull-requests)
- [Adding a new service](#adding-a-new-service)
- [Running tests](#running-tests)
  * [Unit tests](#unit-tests)
- [Releasing a new version](#releasing-a-new-version)
  * [Generating binaries and checksums](#generating-binaries-and-checksums)
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

## Pull Requests
If you want to contribute to the repository, follow these steps:  
  1. Fork the repository
  2. Develop and test your code changes:  
    - To build/test: `go test ./...`
  3. Please add one or more tests to validate your changes.
  4. Make sure everything builds/tests cleanly
  5. Commit your changes  
  6. Push to your fork and submit a pull request to the **master** branch

## Adding a new service

This section will guide you through the steps to generate the Go code for a service
and add the generated code to your CLI plugin project.

**Important** In order to create a CLI plugin for a service, there must already be a _generated_ Go SDK for the same service. If there is not, [add one](https://github.com/IBM/ibm-cloud-sdk-common/blob/master/CONTRIBUTING_go.md#adding-a-new-service) before moving forward with these steps.

1. Validate the API definition - before trying to process the API definition with the SDK generator, we strongly
recommend that you validate the API definition with the
[IBM OpenAPI Validator ](https://github.com/IBM/openapi-validator).
Example:
```
lint-openapi -s example-service.yaml
```
This command will display a list of errors and warnings found in the API definition
as well as a summary at the end.
It's not required that you fix all errors and warnings before trying to use the CLI generator, but
this step should identify any critical errors that will need to be fixed prior to the generation step.

2. **Recommended**: Modify your API definition to configure the `apiPackage` and `cliPluginName` properties.
The value of the `apiPackage` property should be the name of the Go Module for the plugin, ideally in the format `cli-<plugin-name>-plugin`. The value of the `cliPluginName` property defines the top-level namespace of the CLI plugin. For example, the full command will be `ibmcloud <plugin-name> <service-name> <command>`. e.g. `ibmcloud platform-services configuration-governance-v1 rules`.
Here's an example:
```yaml
  info:
    x-codegen-config:
      cli:
        apiPackage: cli-platform-services-plugin
        cliPluginName: platform-services
```

By adding this configuration property to your API definition, you can avoid using the `--api-package`
command line option when running the SDK generator.

More details about SDK/CLI generator configuration properties can be found
[here](https://github.ibm.com/CloudEngineering/openapi-sdkgen/wiki/Config-Options).

3. Next, run the CLI generator to process your API definition and generate the service and unit test
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

openapi-sdkgen.sh generate -g ibm-cli -i my-service.json -o . --api-package <module-import-path>

```
The generated service, unit test code, and Markdown documenation will be written to a **package** directory in the `plugin/commands` directory that reflects the Go package name associated with the service.
For the example above, the package directory would be named `myservicev1`.  You should have a service
package directory for each of the services contained in your project.

4. Update the service table in the `README.md` file to add an entry for the new service.

5. Repeat the steps in this section for each service to be included in your project.

## Running tests
Currently, all tests for the generated CLI plugin are unit tests.

### Unit tests
Unit tests exercise the SDK function with local "mock" service endpoints.

To run all the unit tests contained in the project:
- `go test ./...`

To run a unit test for a specific package within the SDK project:
- `cd plugin/commands/<package-dir>` (e.g. `cd globalsearchv2`)
- `go test`

## Releasing a new version
Once the code is ready for a release, the version must be updated in `plugin/version/version.go`.

Releasing a new version of your plugin through the `ibmcloud` CLI framework requires manually publishing the binaries. All plugins must be published through [this repository](https://github.ibm.com/Bluemix/bluemix-cli-repo). For new plugin versions, use the "Publish" Jenkins job for the [Production Repository](https://github.ibm.com/Bluemix/bluemix-cli-repo#publish-to-production-repository).

Once on this page, select your plugin from the dropdown menu and fill out the `Version` field with the new version of your plugin. Make sure this matches the value in `plugin/version/version.go`. Then, upload your binaries and fill out the checksum for each binary.

### Generating binaries and checksums
If your CLI plugin was built from the [CLI Plugin Template](https://github.ibm.com/CloudEngineering/cli-plugin-template), it already contains scripts to generate binaries and checksums for Mac OS, Linux, and Windows operating systems. These examples assume you are running the script from the project root directory.

1. Generate binaries
`./scripts/generate-binaries.sh`

2. Print checksums
`./scripts/print-checksums.sh`

These files and values are what you will upload/fill in for the release UI.

Once the form is submitted, the Jenkins job will create a PR for your plugin in the formally mentioned repository. At this point, it is up to an admin to approve and merge the PR. Once merged, the new version of the plugin will be released and available for installation.

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
