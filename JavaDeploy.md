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

      markdown-toc -i --maxdepth 4 BintrayDeploy.md
  -->

<!-- toc -->

- [Overview](#overview)
- [Deployment details for IBM Cloud Java SDKs](#deployment-details-for-ibm-cloud-java-sdks)
  * [1. Request a new Bintray repository](#1-request-a-new-bintray-repository)
  * [2. Update your SDK project's maven build](#2-update-your-sdk-projects-maven-build)
    + [2.1: Update your SDK project's parent pom.xml](#21-update-your-sdk-projects-parent-pomxml)
    + [2.2: Update `build/.travis.settings.xml`](#22-update-buildtravissettingsxml)
    + [2.3: Update `module/coverage-reports/pom.xml`](#23-update-modulecoverage-reportspomxml)
  * [3. Request that your Bintray packages be linked to JCenter.](#3-request-that-your-bintray-packages-be-linked-to-jcenter)
  * [4. Synchronize your JCenter artifacts to Maven Central.](#4-synchronize-your-jcenter-artifacts-to-maven-central)

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
For IBM Cloud Java SDK projects, we have a single Bintry organization named `ibm-cloud-sdks`.

- Bintray repository: a grouping of related Bintray packages.  We'll standardize on one Bintray repository
for each Java SDK project.

- Bintray package: a grouping of artifacts (files) associated with a single module within a maven project.
A Bintray package is further sub-divided into versions, where each version represents a maven version.

- JCenter: [JCenter](https://bintray.com/bintray/jcenter) is JFrog's public maven repository where
maven artifacts can be made available to users.  

- Maven Central: [Maven Central](https://central.sonatype.org/) is Sonatype's public maven repository where
maven artifacts can be made available to users, similar to JCenter.  This is the *legacy*
public maven repository.

For projects that would like to make their build artifacts available on both JCenter and Maven Central
(to allow customers to choose which repository to use for obtaining those artifacts), the recommended
path is to:
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
2. Open an issue in the SDK squad's issues repository with summary "Request new bintry repository" and in
the description, include (1) a link to your Java SDK project's github.com repository and (2) your bintray.com id.
A new repository will be created within the `ibm-cloud-sdks` org named after your github.com repository and your
bintray.com id will be configured as an admin for the new repository.

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

#### 2.3: Update `module/coverage-reports/pom.xml`
You should update the `coverage-reports` module's pom.xml to disable the maven `deploy` goal.  This is recommended
because there's no need to deploy the build outputs for this module on JCenter/Maven Central
because the project's test code coverage info should be stored on `codecov.io`).
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
(e.g. `feat(build): add bintray deployment config to build` or something similar).

### 3. Request that your Bintray packages be linked to JCenter.
The next step is a one-time action that is required for each package within your project.

However, before you can request that your packages are linked to JCenter, you must have deployed at least one
version within each of your Bintray packages.
This means that this step cannot be performed until you have completed at least one successful build of your
project in which the `deploy` goal is built.

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

### 4. Synchronize your JCenter artifacts to Maven Central.
After you have completed steps 1 thru 3 and your Bintray packages are available on JCenter,
you can then synchronize your packages from JCenter to Maven Central.

Details for this step will be added soon!
