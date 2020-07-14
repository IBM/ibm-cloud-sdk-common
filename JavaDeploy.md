# Publishing build artifacts on JCenter and Maven Central
This document provides information on how to successfully deploy
maven build artifacts (jars, pom.xml files, etc.) to make them available for
customers on the [JCenter](https://bintray.com/bintray/jcenter) and
[Maven Central](https://central.sonatype.org/) public maven repositories.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 JavaDeploy.md
  -->

<!-- toc -->

- [Overview](#overview)
- [Deployment details for IBM Cloud Java SDKs](#deployment-details-for-ibm-cloud-java-sdks)
  * [1. Request a new Bintray repository](#1-request-a-new-bintray-repository)
  * [2. Update your SDK project's maven build](#2-update-your-sdk-projects-maven-build)
    + [2.1: Update your SDK project's parent pom.xml](#21-update-your-sdk-projects-parent-pomxml)
    + [2.2: Update `build/.travis.settings.xml`](#22-update-buildtravissettingsxml)
    + [2.3: Update `modules/coverage-reports/pom.xml`](#23-update-modulescoverage-reportspomxml)
  * [3. Request that your Bintray packages be linked to JCenter.](#3-request-that-your-bintray-packages-be-linked-to-jcenter)
  * [4. Synchronize your JCenter artifacts to Maven Central.](#4-synchronize-your-jcenter-artifacts-to-maven-central)
- [References:](#references)
  * [Java SDK Build lifecycle overview](#java-sdk-build-lifecycle-overview)

<!-- tocstop -->

## Overview
This section provides general information about Bintray, JCenter and Maven Central.

First let's define some terms:
- Bintray: [Bintray](https://www.jfrog.com/confluence/display/BT/Welcome+to+JFrog+Bintray) is
a platform that can be used to distribute software of various types.  Bintray allows you to
organize your artifacts within organizations, repositories and packages.  Bintray
provides a rudimentary [download site](https://dl.bintray.com) where projects deployed to bintray
are available, although this download mechanism is not extremely usable for maven builds.
It is simply a location from which build artifacts (including artifact types other than maven) can be downloaded.

- Bintray organization: the top-level grouping of artifacts; this is a collection of related repositories.
For IBM Cloud Java SDK projects, we have a single Bintray organization named `ibm-cloud-sdks`.

- Bintray repository: a grouping of related Bintray packages.  We'll standardize on one Bintray repository
for each Java SDK project, and the repository will be named after the github repository for the project
(e.g. `platform-services-java-sdk`).

- Bintray package: a grouping of artifacts (files) associated with a single module within a maven project.
A Bintray package is further sub-divided into versions, where each version represents a maven version.

- JCenter: [JCenter](https://bintray.com/bintray/jcenter) is JFrog's public maven repository where
maven artifacts can be made available to users.  

- Maven Central: [Maven Central](https://central.sonatype.org/) is Sonatype's public maven repository where
maven artifacts can be made available to users, similar to JCenter.  This is the *legacy*
public maven repository.

- "tagged-release" build: a Travis build that is triggered by the addition of a git tag to the repository.
Tags are used to identify specific versions of a particular project.  For each distinct version (release)
of your project, there should be an equivalent tag (e.g. 0.0.1, 0.0.2, 0.1.0, 0.2.1, 1.0.3, etc.)

It is recommended that each IBM Cloud Java SDK project publish its build artifacts (jars) on both JCenter and Maven Central
to allow customers to choose which repository to use for obtaining those artifacts.
To accomplish this, follow these high-level steps:
1. Set up your project's build to deploy your build outputs to bintray packages.
2. Request that your bintray packages are linked (mirrored) to JCenter.
3. Synchronize your artifacts from JCenter to Maven Central.

## Deployment details for IBM Cloud Java SDKs
This section will outline the steps for setting up an IBM Cloud Java SDK project to deploy artifacts
to both JCenter and Maven Central.

Here is a brief overview of these steps:
1. Request a Bintray repository for your Java SDK project.
This repository will contain all of the Bintray packages associated with the SDK project.
2. Update your SDK project's maven build to deploy artifacts into your project's Bintray repository.
Each module within the project (including the parent) will be deployed to its own Bintray package within
your project's Bintray repository.
These Bintray packages will be created automatically as needed by your project's deploy step.
3. Request that your Bintray packages be linked to JCenter.
4. Synchronize your JCenter artifacts to Maven Central.

These steps are detailed below.

### 1. Request a new Bintray repository
Within Bintray, the [`ibm-cloud-sdks` organization](https://bintray.com/ibm-cloud-sdks) will serve
as a container for all Bintray repositories associated with IBM Cloud Java SDK projects.
Each Java SDK project should have a Bintray repository whose name reflects the github.com repository name
(e.g. platform-services-java-sdk).
To request that a repository be created for your project, perform these steps:
1. If you do not yet have an id on bintray.com, then create one, along with an API key.
You can login using your github.com id.
2. Open an issue in the SDK squad's [issues repository](https://github.ibm.com/arf/planning-sdk-squad/issues)
with summary "Request new bintray repository" and in
the description, include the following:  
    1. a link to your Java SDK project's github.com repository
    2. your bintray.com id.
A new repository will be created within the `ibm-cloud-sdks` org named after your github.com repository, and your
bintray.com id will be configured as a member of the org.

### 2. Update your SDK project's maven build
Most of the build configuration required in this step is available via the Java SDK template repository.
If you created your Java SDK project from a recent version of the template repo that contains these changes,
then your project's pom.xml files should already have the required configuration.  If not, then this section
should provide an overview of how to incorporate these changes into your Java SDK project.

#### 2.1: Update your SDK project's parent pom.xml
In this step, you'll modify the parent pom.xml file to configure the
[`bintray-maven-plugin`](https://github.com/random-maven/bintray-maven-plugin) to be used **instead of**
the default `maven-deploy-plugin`:  

  - Add the following settings to the `<properties>` element:  
  ```
  <bintray-plugin-version>1.5.20191113165555</bintray-plugin-version>
  <maven-deploy-plugin-version>3.0.0-M1</maven-deploy-plugin-version>

  <bintray.org>ibm-cloud-sdks</bintray.org>
  <bintray.repo>YOUR BINTRAY REPOSITORY NAME</bintray.repo>
  <bintray.package.url>YOUR PROJECT'S GITHUB.COM URL</bintray.package.url>
  ```

  - Comment out (or remove) the `<distributionManagement>` element as it's not needed.

  - Add these two entries to the `<build>/<pluginManagement>/<plugins>` element:  
  ```
  <plugin>
      <groupId>com.carrotgarden.maven</groupId>
      <artifactId>bintray-maven-plugin</artifactId>
      <version>${bintray-plugin-version}</version>
  </plugin>
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-deploy-plugin</artifactId>
      <version>${maven-deploy-plugin-version}</version>
  </plugin>
  ```

  - Add these two entries to the `<build>/<plugins>` element:
  ```
  <!-- Disable default maven-deploy-plugin -->
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-deploy-plugin</artifactId>
      <configuration>
          <skip>true</skip>
      </configuration>
  </plugin>

  <!-- Enable alternate bintray-maven-plugin -->
  <plugin>
      <groupId>com.carrotgarden.maven</groupId>
      <artifactId>bintray-maven-plugin</artifactId>
      <configuration>
          <skip>false</skip>

          <!-- Bintray oranization name. -->
          <subject>${bintray.org}</subject>

          <!-- Bintray target repository. -->
          <repository>${bintray.repo}</repository>

          <!-- Bintray credentials in settings.xml. -->
          <serverId>bintray</serverId>

          <!--  We'll use the maven coordinates for the bintray package name -->
          <bintrayPackage>${project.groupId}:${project.artifactId}</bintrayPackage>
          
          <!-- Use the project's github url when creating each module's package in the bintray repo -->
          <packageVcsUrl>${bintray.package.url}</packageVcsUrl>

          <performDestroy>false</performDestroy>
          <performCleanup>false</performCleanup>
          <performDeploy>true</performDeploy>
          <performEnsure>true</performEnsure>
          <retryFailedDeploymentCount>2</retryFailedDeploymentCount>
      </configuration>
      <executions>
          <!-- Activate "bintray:deploy" during "deploy" -->
          <execution>
              <goals>
                  <goal>deploy</goal>
              </goals>
          </execution>
      </executions>
  </plugin>

  ```
  
  - The [`bintray-maven-plugin` github.com repository](https://github.com/random-maven/bintray-maven-plugin) and
  [its gh-pages documentation](https://random-maven.github.io/bintray-maven-plugin/plugin-info.html)
  contain more details about the plugin.

#### 2.2: Update `build/.travis.settings.xml`
Make sure the maven settings file used during your SDK project's Travis builds (i.e. `build/.travis.settings.xml`) contains this
`<server>` entry:
```
    <server>
      <id>bintray</id>
      <username>${env.BINTRAY_USER}</username>
      <password>${env.BINTRAY_APIKEY}</password>
    </server>
```

The `<id>` element's value must match the value of the `bintray-maven-plugin`'s `<serverId>`
element within your SDK project's pom.xml file.

Also, make sure that your Travis build settings (i.e. `travis-ci.com/IBM/<github-project-name>/settings`)
contain these two environment variables:
- `BINTRAY_USER`: the bintray username; this user MUST have `write` access to the
Bintray repository used by your project's build.
- `BINTRAY_APIKEY`: the API key associated with the bintray username

#### 2.3: Update `modules/coverage-reports/pom.xml`
You should update the `coverage-reports` module's pom.xml to disable the maven `deploy` goal.  This is recommended
because there's no need to deploy the build outputs for this module on JCenter/Maven Central
since the project's test code coverage info should be stored on `codecov.io`.
To disable the `deploy` goal for this module, add this to the pom.xml in the `<build>/<plugins>` element:  
```
<!-- No need to deploy this artifact since it contains only coverage info -->
<plugin>
    <groupId>com.carrotgarden.maven</groupId>
    <artifactId>bintray-maven-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
```

After the changes in this step (along with step 1) have been completed, you can commit the changes and merge in
the associated PR into your project's master branch.
**Do not merge your PR until after the Bintray repository for your project has been created.**

When committing your changes, be sure to use a commit message that will trigger a new
release when semantic-release is unleashed on your PR's commits
(e.g. `git commit -m "feat(build): add bintray deployment config to build"` or something similar).

### 3. Request that your Bintray packages be linked to JCenter.
The next step is a one-time action that is required for each package within your project.

However, before you can request that your packages are linked to JCenter, you must have deployed at least one
version within each of your Bintray packages.
This means that this step cannot be performed until you have completed at least one successful tagged-release
build of your project in which the `deploy` goal is built.

The maven `deploy` goal is built during the `deploy` stage of a tagged-release build in Travis.
The recommendation here is to complete steps 1 and 2 above, then after merging
in the associated PR containing the changes for step 2, your project should trigger a Travis tagged-release build
which should result in the successful deployment of an initial version (reflecting the maven version #)
of each of your project's modules within the corresponding Bintray packages.

To verify this, you should log into bintray.com and view your repository to ensure that each of the expected
Bintray packages exist, and that there is at least one version within each package (the version should reflect
the maven version # and should be the same across all the packages within your Bintray repository).

After you have **visually inspected** your various Bintray packages to ensure that the build artifacts have been
successfully deployed to each package, you can then request that your packages are linked to JCenter.
Within the Bintray UI dialog for each package, click on `Actions` then `Add to JCenter`.  For your project's main
package (the parent) be sure to check the "Pom project" checkbox, but leave it empty for all other packages.  Also,
leave the snapshot-related option empty as well, since we will not be deploying snapshot releases to Bintray.
The time for JFrog to complete each request varies, but is usually within a few hours.
If you encounter any problems with this step, you can email `support@jfrog.com` to request help.

If you later add a new service (module) to your SDK project, you will need to perform this step for the new module's
artifact after you have completed the first tagged-release build that includes the new module.  In other words,
whenever your project starts to publish a new artifact as part of the "mvn deploy" step, that should result in a
new package within your bintray repository and you'll need to request that the new package be included in JCenter.


### 4. Synchronize your JCenter artifacts to Maven Central.
After you have completed steps 1 thru 3 and your Bintray packages are available on JCenter,
you can then add a "sync" step to the "deploy" stage within your Travis build in order to
synchronize your packages (artifacts) from JCenter to Maven Central.

To do this, follow these steps:
- The [Java SDK Template repository](https://github.ibm.com/CloudEngineering/java-sdk-template)
includes files `build/bintraySync.sh` and `build/sync2MC.sh`.  If your Java SDK project does not already
contain these files, then add (copy) them to your project.

- Modify the `build/bintraySync.sh` file so that the "package_names" variable is set to the list of
bintray packages that should be sync'd from JCenter to Maven Central.  In general, this list should include
the main (parent) artifact for your SDK project, the artifact associated with the SDK project's "common" module,
plus the artifact associated with each service contained in your SDK project.   If you later add a new service
to your SDK project, be sure to update this script to include the package name associated with the new service.

- Make sure that the "mvn deploy" deployment step within your `.travis.yml` file
invokes the `build/bintraySync.sh` script, as in this example:
```
deploy:
...

  # Publish jars on bintray for a tagged release.
  - provider: script
    script:
    - >-
          mvn deploy $MVN_ARGS -DskipTests 
          && build/bintraySync.sh $BINTRAY_USER $BINTRAY_APIKEY $BINTRAY_ORG $BINTRAY_REPO $TRAVIS_TAG
    skip_cleanup: true
    on:
      tags: true
      jdk: openjdk8
```

- If not already present, update the `.travis.yml` file's "env" section to add
settings for the `BINTRAY_ORG` and `BINTRAY_REPO` environment variables, as in this example:
```
env:
  global:
    - BINTRAY_ORG=ibm-cloud-sdks
    - BINTRAY_REPO=<your project's bintray repository name>   (e.g. platform-services-java-sdk)
```

- Your SDK project's Travis build settings should already include the following environment variables
(see section 2.2 above):
  - `BINTRAY_USER`
  - `BINTRAY_APIKEY`


With these changes in place, when a tagged-release build of your SDK project is performed, the "deploy" stage
should do the following:
1. execute the `mvn deploy` command which builds the new version of each artifact and publishes
them to your project's bintray repository
2. invokes the `build/bintraySync.sh` script that syncs those newly-published artifacts from JCenter to Maven Central


## References:
- [Enjoy Bintray and use it as pain-free gateway to Maven Central](https://jfrog.com/blog/bintray-as-pain-free-gateway-to-maven-central/)
- [Syncing with Third-Party Platforms](https://www.jfrog.com/confluence/display/BT/Syncing+with+Third-Party+Platforms)
- [OSSRH Guide](https://central.sonatype.org/pages/ossrh-guide.html)
- [Manual Staging Bundle Creation and Deployment](https://central.sonatype.org/pages/manual-staging-bundle-creation-and-deployment.html)

### Java SDK Build lifecycle overview

While performing release management of a Java SDK project, there are several different types of builds that are
typically performed with different types of deployment steps included in each one.   This section attempts to provide
an overview by describing a typical workflow.

1. A developer creates one or more commits in his/her local copy of the SDK project's git repository, then pushes those commits to
a feature branch and submits a pull request (PR).   The submission of the PR triggers a "PR build".

2. Assuming the PR build triggered in step 1 is clean and the PR is approved, it is then
merged into the SDK project's master branch.   The merge into the master branch triggers a
"branch build" of the master branch.

3. The "branch build" of the master branch will build and test the project, then the "semantic-release" deploy stage
is triggered.   The semantic-release deployment tool will do the following:
    - Analyze the commit messages associated with the commits that were merged into the master branch
      since the most recent tagged-release of the project to determine if a new version (tagged-release)
      of the project is warranted.  If a new version is needed, semantic-release determines what the next version
      should be.
    - If a new version is needed, semantic-release will compile a changelog entry for the new version, perform some
      additional release management steps and will eventually push one or more commits back to the github repository along
      with a tag to represent the new version (tagged-release) of the project.

4. Once the new tag is pushed back to the github repository in step 3 above, a new tagged-release build is triggered
in Travis.  You can think of a tagged-release build as a special branch build where the name
of the branch is the tag (e.g. 0.5.3).
The tagged-release build will perform the following steps:
    - [before_script]: The maven "versions" plugin is used to change the maven version # of the project to match
      the tag value (e.g. 0.5.3).
    - [script]: The project is built and tested.
    - [deploy]: The `mvn deploy` command is invoked which will publish the artifacts for each module on bintray/JCenter
    - [deploy]: The `build/bintraySync.sh` script is invoked which will sync the freshly-published artifacts to Maven Central.
    - [deploy]: The `build/publish-javadoc.sh` script is invoked which will build the javadocs for the new version of the project,
      then publish them on the "gh-pages" branch of the SDK project's git repo.
