# Enabling semantic-release within Travis builds
This document provides information on how to enable the use of the `semantic-release` tool
to automate the publishing of new releases of your Go, Java, Node.js and Python SDK projects.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 SemrelEnablement.md
  -->

<!-- toc -->

- [Overview](#overview)
  * [What is semantic-release?](#what-is-semantic-release)
  * [Assumptions](#assumptions)
- [Configuring semantic-release](#configuring-semantic-release)
  * [1. Clone your SDK project](#1-clone-your-sdk-project)
  * [2. Establish baseline release](#2-establish-baseline-release)
    + [i. Prepare the baseline release](#i-prepare-the-baseline-release)
    + [ii. Create initial git tag](#ii-create-initial-git-tag)
  * [3. Set credentials in Travis build settings](#3-set-credentials-in-travis-build-settings)
    + [All SDK projects](#all-sdk-projects)
    + [Go SDK projects](#go-sdk-projects)
    + [Java SDK projects](#java-sdk-projects)
    + [Node.js SDK projects](#nodejs-sdk-projects)
    + [Python SDK projects](#python-sdk-projects)
  * [4. Enable deploy stage within .travis.yml](#4-enable-deploy-stage-within-travisyml)
- [Now what?](#now-what)
- [References:](#references)

<!-- tocstop -->

## Overview
This document will assist you in configuring your Go, Java, Node.js, and Python SDK projects 
to use the [semantic-release](https://semantic-release.gitbook.io/semantic-release/)
tool to automatically publish new releases.

### What is semantic-release?
`semantic-release` is a tool used during your SDK project's automated CI/CD builds to 
automate the creation and publishing of new releases of your project.
`semantic-release` does this by analyzing the commit messages associated with the commits
that have been merged into the project's `main` branch since the last release was published.
After analyzing the commit messages, `semantic-release` determines the next
[semantic version](https://semver.org/) number, produces a changelog and publishes the release,
including pushing commits and a tag back to your SDK project's git repository.

### Assumptions
This document assumes the following are true before you try to enable `semantic-release` in your
SDK project:

1. You have created your SDK project from one of the following SDK template repositories:
* https://github.ibm.com/CloudEngineering/go-sdk-template
* https://github.ibm.com/CloudEngineering/java-sdk-template
* https://github.ibm.com/CloudEngineering/node-sdk-template
* https://github.ibm.com/CloudEngineering/python-sdk-template

2. You have completed the initial project preparation steps outlined in the SDK template repository's
`README_FIRST.md` document (you have either (a) performed the manual steps or (b) have run the 
`prepare_project.sh` script). This implies that you have committed the preparation-related changes.
We recommend that you use this commit when establishing the initial baseline release of your
project.

3. Your SDK project builds and tests cleanly.

4. Your SDK project includes the following files which are provided by the SDK template repository:  
    * `.bumpversion.cfg` - [Go, Java, Python] this is a configuration file for the
    [`bump2version`](https://pypi.org/project/bump2version/) tool that is invoked by
    `semantic-release` to modify the current version number within selected files in your project.
    * `.releaserc` - [Go, Java, Node.js, Python] this is the `semantic-release` configuration file.
    * `package.json` - [Go, Java, Python] this file is normally used only in Node.js projects,
    but we use this for Go, Java, and Python as an easy way to manage the `semantic-release` dependency and
    its transitive dependencies.
    * `package.json` - [Node.js] this is the traditional package-level configuration file for a Node.js
    project, and includes `semantic-release` in its `devDependencies` section.

5. Your commit messages comply with the [Angular Commit Message Conventions](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format).

6. You have activated your SDK project with Travis for automated CI/CD builds. For instructions on how
to do this, please see the [Travis CI Tutorial](https://docs.travis-ci.com/user/tutorial/).
Note that your SDK project already has a `.travis.yml` build configuration file.

7. You have completed at least one successful Travis CI/CD build of the `main` branch of your SDK project.

8. You have at least a beginner's level of experience with running git commands within a shell 
environment (bash, zsh, etc.).


## Configuring semantic-release
The sections that follow will provide detailed instructions on how to get started with `semantic-release`.
Some of the instructions differ slightly, depending on the language (Go, Java, Node.js, Python). These
differences will be highlighted below.

### 1. Clone your SDK project
If you haven't already done so, clone your SDK project's git repository in your local environment and checkout
the `main` branch:  
  ```sh
  git clone <repository>
  cd <project-root>
  git checkout main
  git pull
  ```
Note: Do not use a fork for this.

At this point your local clone of your project should be located at `<project-root>` and
the `main` branch should be checked out.

### 2. Establish baseline release
Before you can start to use `semantic-release` in your CI/CD builds, you first need to create an
initial baseline release (version). This will typically be version number `0.0.1` (keep in mind that within the
semantic version numbering scheme, any version matching `0.x.y` implies that it is a
pre-release or "beta" type release). The instructions that follow will use `0.0.1` as the initial
baseline release. If your circumstances are different, then simply make the necessary adjustments to
the instructions documented below.

#### i. Prepare the baseline release
There are files within your SDK project that contain the project's version number. These will eventually be
managed automatically by semantic-release, but first you'll need to make sure that these files reflect the
appropriate initial baseline release.

For Go, Java, and Python SDK projects, examine the `.bumpversion.cfg` file within your SDK project,
and make sure that the current version is listed correctly as the initial baseline release that
you've chosen for the project (adjust as necessary):  
  ```
  [bumpversion]
  current_version = 0.0.1
  ```

For Node.js SDK projects, examine the `package.json` file within your SDK project,
and make sure that the `version` field is listed correctly as the initial baseline release that
you've chose for the project (adjust as necessary):  
  ```javascript
  {
    ...
    "version": "0.0.1",
    ...
  }
  ```

Next, examine the following files from your SDK project and make sure they also reflect the correct
initial baseline release as well (adjust as necessary):  
* Go SDK projects:  
    * `common/version.go`
    * `README.md`
* Java SDK projects:  
    * `README.md`
* Python SDK projects:  
    * `<sdk-package-name>/version.py`
    * `setup.py`
    * `README.md`

Next, examine the `.releaserc` file within your project and make sure that its contents match the
`.releaserc` file in the SDK template repository used to create your SDK project (adjust as necessary).

If you needed to make any changes for this step, then be sure to commit and push those changes now before
proceeding to the next step:  
  ```sh
  git commit -a -s -m "build: prepare initial baseline release"
  git push
  ```

#### ii. Create initial git tag
Next, an initial git tag is needed in your SDK project's git repository to establish the
initial baseline release.
Pick a commit in your git history that should serve as the initial release of the project.
If you committed changes in the previous step (prepare baseline release), use that commit.
Otherwise, use the commit that contains your project preparation changes (see the Assumptions section above):  
  ```sh
  cd <project-root>
  git tag <tag-value> <commit-id>
  ```
where:  
* `<tag-value>` should be `v0.0.1` for Go, Node.js and Python SDK projects,
and should be `0.0.1` for Java SDK projects.
* `<commit-id>` is the commit ID of the commit to use as the initial baseline release.
To find this, you can use the `git log` command and copy the commit ID for the commit you have chosen.

After creating the tag in your local clone, you'll need to push it to the git remote:  
  ```sh
  git push --tags
  ```


### 3. Set credentials in Travis build settings
In this step, you'll set the appropriate environment variables within your SDK project's
Travis build settings page to contain the credentials needed for the build.
The details of this will depend on the language associated with your SDK project.

The Travis build settings page should be located at URL: `https://app.travis-ci.com/github/IBM/<repo-name>/settings`
This assumes that you have previously activated your repository in Travis and have successfully
completed at least one build of the `main` branch. If not, then perform those steps before proceeding.

It is __strongly recommended__ that you use a functional ID to establish the
credentials used by your Travis builds for publishing. Alternatively, you can use a personal ID for these
purposes. The ID that you choose will appear as the author of commits and tags pushed to your
SDK project's git repository by the semantic-release tool.

#### All SDK projects
For all SDK projects, you must set this environment variable:  
* `GH_TOKEN`: the GitHub personal access token associated with a user that has write (push) access to the git repository

To obtain a Github access token:
1. Log into `github.com`.
2. Click on the user icon (top right), then `Settings`, then `Developer Settings`, then `Personal access tokens`.
3. Click `Generate new token, then click on `Generate new token (classic)`.
4. Set the description (e.g. "Token for CI/CD") and expiration, then select the entire `repo` scope section.
5. Scroll down and click the `Generate token` button.
6. On the next screen, copy the new token and store in a safe place.

If you are using internal Travis (v3.travis.ibm.com) for your builds, these environment variables are
also needed to indicate that your SDK project is located in GitHub Enterprise (github.ibm.com):  
* `GH_URL`: Set this to `https://github.ibm.com`
* `GH_PREFIX`: Set this to `/api/v3`

#### Go SDK projects
For Go SDK projects, no additional environment variables are needed for `semantic-release` since
you are really only publishing the release back to the project's git repository.

#### Java SDK projects
For Java SDK projects, you are presumably publishing your jars on `Maven Central`.
If so, then you'd also need to define the following environment variables in the Travis build settings:
`GPG_KEYNAME`, `GPG_PASSPHRASE`, `OSSRH_USERNAME`, `OSSRH_PASSWORD`.

These variables are explained in [JavaDeploy.md](JavaDeploy.md#46-update-your-travis-build-settings).

#### Node.js SDK projects
For Node.js SDK projects, you are presumably publishing your package on `npmjs.com`.
If so, then you'd also need to define this environment variable in the Travis build settings:  
* `NPM_TOKEN`: the access token for the NPM user that will be used to perform the publishing

To obtain an NPM access token:
1. Log into `npmjs.com`.
2. Click on the user icon (top right), then click on `Access Tokens`.
3. Click `Generate New Token`, then set a description (e.g. "Token for CI/CD") and select type `Automation`.
4. Click `Generate Token`.
5. On the next screen, copy the new token and store in a safe place.

#### Python SDK projects
For Python SDK projects, you are presumably publishing your package on `pypi.org`.
If so, then you'd also need to define these environment variables in the Travis build settings:  
* `PYPI_USER`: Set this to `__token__` (i.e. "token" surrounded by two underscores)
* `PYPI_TOKEN`: The value is the access token for the PYPI user that will be used to perform the publishing

To obtain a PYPI access token:
1. Log into `pypi.org`.
2. Click on the user icon (top right), then click on `Account settings`.
3. Scroll down to the `API tokens` section, and click on the `Add API token` button.
4. On the next screen, enter a description for `Token name` (e.g. "Token for CI/CD"), then select scope `Entire account`,
then click the `Create token` button.
5. On the next screen, copy the new token and store in a safe place.

### 4. Enable deploy stage within .travis.yml
After creating your SDK project from the SDK template repository and after performing the project preparation steps,
you should have a `.travis.yml` file in the project root directory.
This file will have certain sections commented out to disable them until you are ready to enable them.

Within your SDK project's `.travis.yml` file, locate the sections that pertain to the `semantic-release`
tool and un-comment them to enable their use:

* Go - uncomment the `before_deploy` and `deploy` stages:  
  ```yaml
  before_deploy:
    - nvm install 18
    - node --version
    - npm --version
    - npm install
    - pip install --user bump2version
  
  deploy:
    - provider: script
      script: npm run semantic-release
      skip_cleanup: true
      on:
        go: '1.19.x'
        branch: main
  ```
  Note that the `go` field in the `deploy` stage should be set to the minimum version of
  Go on which you are building the project.

* Java - uncomment the `Semantic-Release` stage definition within the `stages` field:  
  ```yaml
  stages:
    - name: Build-Test
    - name: Semantic-Release
      if: (branch = main) AND (type IN (push, api)) AND (fork = false)
  ```
  and then uncomment the `Semantic-Release` stage itself:
  ```yaml
    - stage: Semantic-Release
      install:
        - nvm install 18
        - node --version
        - npm --version
        - npm install
        - pip install --user bump2version
      script:
        - npm run semantic-release
  ```

* Node.js - uncomment the `deploy` stage:  
  ```yaml
    deploy:
      - provider: script
        skip_cleanup: true
        script: npx semantic-release
        on:
          node: 18
          branch: main
  ```

* Python - uncomment the `before_deploy` stage and the "script" provider section within the
`deploy` stage:  
  ```yaml
    before_deploy:
      - nvm install 18
      - node --version
      - npm --version
      - npm install
      - pip install bump2version
    
    deploy:
      - provider: script
        script: npm run semantic-release
        skip_cleanup: true
        on:
          python: '3.8'
          branch: main
  ```
  Note that the `python` field within the `deploy` stage should be set to the minimum
  version of Python on which you are building the project.

## Now what?
Once you have correctly configured `semantic-release` in your SDK project, new releases
will be published automatically as you merge commits (pull requests) into the `main` branch.
New commits pushed (merged) to the `main` branch will trigger a Travis build,
and the deployment stage of the build will invoke `semantic-release` to examine the
commits that have been merged into `main` since the last release of the project
(based on the git tag).

For example, if the current version of your SDK project is `0.0.1`, and you merge
into the `main` branch a commit with message something like `feat: add new service to SDK`, `semantic-release`
will pick the next version number as `0.1.0` (a new minor release) and will publish this new release.
Likewise, if the commit message was something like `fix: use correct URL`, `semantic-release` will
pick `0.0.2` (a new fix level) as the next version number.

Sometimes, `semantic-release` will determine that the new commits pushed to `main` since the last release
of the project should not produce a new release. For example, a commit message like
`build: bump dependencies to avoid vulnerabilities` would not result in a new version number.

For details on the types of commits which cause new versions of your project, please consult the
[Semantic-Release documentation](https://semantic-release.gitbook.io/semantic-release/).

Suffice it to say that it is important that within your SDK projects, you adopt a disciplined
approach for composing commit messages so that you can adhere to the guidelines required by
`semantic-release`. Remember that `semantic-release` not only analyzes the commit messages to
determine the next semantic version number of the projecct, but it also uses the commit messages
to generate `CHANGELOG.md` entries, so it's important that commit messages are clear and
convey the appropriate information to your users.

One thing to keep in mind is that it is a good practice to use `Squash and Merge` when merging a pull request.
This means that you can push any number of commits (with non-compliant commit messages) to a feature branch,
but then when you merge the pull request, you will need to do a "Squash and Merge", and enter a
compliant commit message for the single "squashed" commit that will be merged into the `main` branch.

## References:
* [Travis CI Tutorial](https://docs.travis-ci.com/user/tutorial/)
* [Semantic-Release](https://semantic-release.gitbook.io/semantic-release/)
* [Semantic Versioning](https://semver.org/)
* [Publishing build artifacts on Maven Central](https://github.com/IBM/ibm-cloud-sdk-common/blob/main/JavaDeploy.md)
* [Node Package Manager](https://www.npmjs.com/)
* [Creating and publishing scoped public packages](https://docs.npmjs.com/creating-and-publishing-scoped-public-packages)
* [The Python Package Index](https://pypi.org/)
* [How to Publish an Open-Source Python Package to PyPI](https://realpython.com/pypi-publish-python-package/)
