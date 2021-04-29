# Publishing build artifacts on Maven Central
This document provides information on how to configure your Java SDK project
to successfully publish build artifacts (jars, pom.xml files, etc.) on the
[Maven Central](https://central.sonatype.org/) public maven artifact repository.

## Table of Contents
<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      markdown-toc -i --maxdepth 4 JavaDeploy.md
  -->

<!-- toc -->

- [Overview](#overview)
- [1: Choose the maven group id for your project](#1-choose-the-maven-group-id-for-your-project)
- [2: Create a JIRA ticket with Sonatype](#2-create-a-jira-ticket-with-sonatype)
- [3: Create a public/private key pair for signing artifacts](#3-create-a-publicprivate-key-pair-for-signing-artifacts)
  * [3.1: Generate a new public/private key pair](#31-generate-a-new-publicprivate-key-pair)
  * [3.2: List keys](#32-list-keys)
  * [3.3: Publish your public key](#33-publish-your-public-key)
  * [3.4: Export your key](#34-export-your-key)
- [4: Modify your Java SDK project to publish artifacts on Maven Central](#4-modify-your-java-sdk-project-to-publish-artifacts-on-maven-central)
  * [4.1: Copy files from the Java SDK template repository](#41-copy-files-from-the-java-sdk-template-repository)
  * [4.2: Update your .travis.xml file](#42-update-your-travisxml-file)
  * [4.3: Add your signing key to your git repository.](#43-add-your-signing-key-to-your-git-repository)
  * [4.4: Update the parent pom.xml](#44-update-the-parent-pomxml)
  * [4.5: Update the coverage-reports module pom.xml](#45-update-the-coverage-reports-module-pomxml)
  * [4.6: Update your Travis build settings](#46-update-your-travis-build-settings)
  * [4.7: Commit your changes](#47-commit-your-changes)
- [5: Java SDK Build lifecycle overview](#5-java-sdk-build-lifecycle-overview)
- [References:](#references)

<!-- tocstop -->

## Overview
This document will assist you in configurating your Java SDK project to publish artifacts on
Maven Central.

The [OSSRH Guide](https://central.sonatype.org/pages/ossrh-guide.html) should be considered
to be the authoritative source of information regarding the proper steps to be followed
for publishing artifacts on Maven Central. Please familiarize yourself
with the steps documented in the OSSRH Guide before following the instructions in this document.

The intent of this document is to provide specific instructions to guide IBM Cloud Java SDK maintainers in
performing the steps outlined in the OSSRH Guide.

## 1: Choose the maven group id for your project
It is recommended that all IBM Cloud Java SDK projects use a maven group id of `com.ibm.cloud`.
This provides some consistency for IBM Cloud customers wishing to use the artifacts
produced by various SDK projects (i.e. IBM Cloud services).   This is not an absolute requirement, but
is a recommendation.  Whatever you decide to use for a group id within your SDK project, please
make sure it is properly documented in your SDK project's `README.md` file.

A Java SDK project's main artifact (the "parent" project) would have an artifact id that represents
the service category (e.g. `platform-services`), and each module within the project would have an
artifact id that reflects the name of the service contained in that module (e.g. `resource-controller`,
`global-catalog`, `configuration-governance`, etc.).

An artifact's group id, artifact id, and version form the maven coordinates needed by users to
define a dependency on the artifact (e.g. `com.ibm.cloud:global-catalog:1.3.1`).

Before moving to the next step, choose the group id that you will use within your SDK project.

## 2: Create a JIRA ticket with Sonatype
In this step, you'll create a JIRA ticket with Sonatype to obtain the proper access to allow
you to publish artifacts using your chosen group id.
Note: If your Java SDK project has previously been configured to publish artifacts on bintray/jcenter
and then synchronize them to maven central, then you likely already have a Sonatype account and
the necessary access to publish artifacts on maven central.

Follow the instructions in the "Create a ticket with Sonatype" section of
the [OSSRH Guide](https://central.sonatype.org/pages/ossrh-guide.html).

When creating your Sonatype account, keep in mind that this account
will also be used by your Java SDK project's Travis build
to perform the publishing steps, so consider using a functional id rather than
a personal id.

When creating the "New Project" ticket, you will be requesting that Sonatype
provides you with the necessary authority to publish artifacts using your
chosen group id.
If you will be using the `com.ibm.cloud` group id as recommended above, then
add this comment to your ticket to request approval from Phil Adams:
`[~padamstx] please approve this request.`.

## 3: Create a public/private key pair for signing artifacts
One of the requirements for publishing artifacts on Maven Central is that
each file (jar, pom.xml, etc.) must be signed, and this requires the use
of a public/private key pair.

Detailed instructions on how to create a new signing key can be found
[here](https://central.sonatype.org/pages/working-with-pgp-signatures.html).

In order to automatically sign artifacts during your automated builds,
you'll need to export your key, encrypt it, and then add the encrypted file
to your SDK project's git repository so that it can be used during your automated builds.
See the instructions below on how to do this.

### 3.1: Generate a new public/private key pair

Use the [detailed instructions](https://central.sonatype.org/pages/working-with-pgp-signatures.html)
to generate a new key pair:
```
gpg --gen-key
```
Use `RSA` for the kind and 2048-bit as the size of the key.
Be sure to specify a non-trivial passphrase and save it somewhere secure
(add it to your password manager, etc.).  Omitting the passphrase makes it easier to forge artifact
signatures.

This command creates the new key within your local keystore managed by the gpg command
(called `$HOME/.gnupg/pubring.kbx` or something similar).

### 3.2: List keys
To list your keys:
```
gpg --list-keys
```
Example:
```
$ gpg --list-keys
/home/padams/.gnupg/pubring.kbx
-------------------------------
pub   rsa2048 2020-11-22 [SC] [expires: 2022-11-22]
      B265BB91D80124E463C63119EC4158B05D352E09
uid           [ultimate] Phillip C. Adams <phil_adams@us.ibm.com>
sub   rsa2048 2020-11-22 [E] [expires: 2022-11-22]
```

### 3.3: Publish your public key
In order to publish artifacts on Maven Central, you will need to publish
your public key on a key server so that Sonatype can verify your artifacts
before releasing them to Maven Central.  Use this command to do this:
```
$ gpg --keyserver hkp://keys.gnupg.net --send-keys B265BB91D80124E463C63119EC4158B05D352E09
gpg: sending key EC4158B05D352E09 to hkp://hkps.pool.sks-keyservers.net

```
Be sure to use your key's id in place of `B265BB91D80124E463C63119EC4158B05D352E09` :).

Next, you can [verify that your key was published](http://keys.gnupg.net/).
Open that page, enter the email address associated with your key,
then click `Search Key`.

Publishing your public key also allows users of your artifacts to verify them with the
gpg command:
```
$ gpg --verify <jar_filename>.asc
```

### 3.4: Export your key
After following the steps above, you should have a new signing key within your local keystore.
This key will need to be exported and then added to your project's git repository
so that your Travis build can import the key into the Travis build agent's local
keystore to allow the maven build running on that agent to automatically sign your artifacts
with your signing key.

So, while we're dealing with the gpg-related steps here, we might as well export it now:
```
gpg --export-secret-key -a B265BB91D80124E463C63119EC4158B05D352E09 > signing.key
```
Of course, replace `B265BB91D80124E463C63119EC4158B05D352E09` with your key's id
(found in the output of the `gpg --list-keys` command above).
You should now have a text file named `signing.key` that looks like this:
```
-----BEGIN PGP PRIVATE KEY BLOCK-----

<contents of private key>

-----END PGP PRIVATE KEY BLOCK-----
```

Next, we'll update your Java SDK project so that it can
automatically sign and publish your build artifacts.


## 4: Modify your Java SDK project to publish artifacts on Maven Central

In this section, you will be making updates to your Java SDK project, so be sure to
create a new feature branch from your current "master" (or "main") branch.

Note: If you created a new Java SDK project from the
[Java SDK Template repository](https://github.ibm.com/CloudEngineering/java-sdk-template)
on or after 02/06/2021, then your project should already have the changes outlined in this
section of the document.   This section is intended mainly for those that have created
their project from an earlier version of the template repository and need to update their
project to remove the bintray publishing step and replace it with a maven central publishing
step.

### 4.1: Copy files from the Java SDK template repository
Copy the following files from the
[Java SDK Template repository](https://github.ibm.com/CloudEngineering/java-sdk-template)
into the `build` directory of your Java SDK project:
- `build/generateJavadocIndex.sh`
- `build/publishCodeCoverage.sh`
- `build/publishJavadoc.sh`
- `build/setMavenVersion.sh`
- `build/setupSigning.sh`
- `build/.travis.settings.xml`

Next, within your Java SDK project, modify the `build/generateJavadocIndex.sh` file to reflect
your Java SDK project (i.e. the `title`, `h1` heading, `<service-category>`, and `<github-repo-url>`).

Next, if you will be building your project on the public travis-ci.com server, modify
the `build/.travis.settings.xml` file to remove the `<server>` entry with id `na-artifactory-ibmcloud-sdks`.
Leave the `<server>` entry with id `ossrh` there.


### 4.2: Update your .travis.xml file
Next, replace your project's `.travis.yml` file with the contents of
`.travis.yml.sdk` from the [Java SDK Template repository](https://github.ibm.com/CloudEngineering/java-sdk-template).
If you have project-specific settings in your `.travis.yml` file, be sure to back up the file first,
then copy any project-specific settings to your new `.travis.yml` file.
You should end up with a new `.travis.yml` file in the root directory of your project.
Within the `.travis.yml` file, remove the comments from the `stages` and `jobs` sections
(there are instructions inside the file).


### 4.3: Add your signing key to your git repository.
In this step, you'll encrypt the `signing.key` file that you previously
exported and add it to your git repo within the `build` directory.

Detailed instructions on how to encrypt a file and add it to your repository can be
found [here](EncryptingSecrets.md), so the instructions below will be kept brief:

```
$ cd <project-root>

# Copy your signing.key file to your project's "build" directory. 
# Replace "<dir>" with the name of the directory containing the signing.key file
# that you exported previously.
$ cp <dir>/signing.key ./build

$ travis login --github-token <your-public-github-token> --com

$ travis encrypt-file build/signing.key build/signing.key.enc --com
```

The `travis encrypt-file` command will display an `openssl` command as part of its output.
Here is an example:
```
openssl aes-256-cbc -K $encrypted_4b7d603e7466_key -iv $encrypted_4b7d603e7466_iv -in build/signing.key.enc -out build/signing.key -d
```
Next, modify the `openssl` command found inside the `build/setupSigning.sh` script to reflect the
names of the environment variables from the `openssl` command displayed by the
`travis encrypt-file` command.
In the example above, the environment variable names are
`$encrypted_4b7d603e7466_key` and `$encrypted_4b7d603e7466_iv`.
The `travis encrypt-file` command added these environment variables to your project's Travis build settings.

Finally, remove the `build/signing.key` file (remove only the plain-text file and
keep the `build/signing.key.enc` file):
```
$ rm build/signing.key
```

### 4.4: Update the parent pom.xml

In this step, you'll modify your Java SDK project's parent pom.xml file (in the project root directory)
to add the necessary configuration to allow your project to sign and publish your build artifacts.
As a reminder, if you created your project from a recent version of the template repository, you
likely already have these changes reflected in your parent pom.xml.

The most straightforward way to perform this step might be to "diff" your pom.xml file with the parent pom.xml
in the [Java SDK Template repository](https://github.ibm.com/CloudEngineering/java-sdk-template)
and apply the necessary changes, while retaining your project-specific settings.
However, I'll attempt to summarize the changes here:

- In the `<properties>` section, remove the `<bintray-plugin-version>` property, then add these properties:
```
<nexus-staging-plugin-version>1.6.8</nexus-staging-plugin-version>
<maven-gpg-plugin-version>1.6</maven-gpg-plugin-version>
```

- Also in the `<properties>` section, remove these properties:  
  * `<bintray.org>`
  * `<bintray.repo>`
  * `<bintray.package.url>`
  
- Within the `<modules>` section, make sure that your `examples` module is listed last,
after the `coverage-reports` module.

- In the `<repositories>` section, remove the `<repository>` entry with id `jcenter` (if present).

- In the `<build>/<pluginManagement>/<plugins>` section, remove the `<plugin>` entry for "bintray-maven-plugin",
and add the following:
```
<plugin>
    <groupId>org.sonatype.plugins</groupId>
    <artifactId>nexus-staging-maven-plugin</artifactId>
    <version>${nexus-staging-plugin-version}</version>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-gpg-plugin</artifactId>
    <version>${maven-gpg-plugin-version}</version>
</plugin>
```
- In the `<build>/<plugins>` section, remove the `<plugin>` entry for "bintray-maven-plugin".

- After the `<developers>` section (i.e. immediately before the file's closing `</project>` line),
add the following `<profiles>` section:
```
    <profiles>

        <!-- "central" is used to deploy artifacts on maven central -->
        <profile>
            <id>central</id>

            <!-- For this profile, we'll get dependencies from maven central -->
            <repositories></repositories>

            <distributionManagement>
                <snapshotRepository>
                    <!-- We don't deploy snapshot releases -->
                </snapshotRepository>
                <repository>
                    <!-- This is where the nexus staging plugin will publish artifacts -->
                    <id>ossrh</id>
                    <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>

            <build>
                <plugins>
                    <plugin>
                        <groupId>org.sonatype.plugins</groupId>
                        <artifactId>nexus-staging-maven-plugin</artifactId>
                        <extensions>true</extensions>
                        <configuration>
                            <serverId>ossrh</serverId>
                            <nexusUrl>https://oss.sonatype.org/</nexusUrl>
                            <autoReleaseAfterClose>true</autoReleaseAfterClose>
                            <keepStagingRepositoryOnCloseRuleFailure>true</keepStagingRepositoryOnCloseRuleFailure>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>sign-artifacts</id>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                        <configuration>
                            <gpgArguments>
                                <gpgArgument>--batch</gpgArgument>
                                <gpgArgument>--yes</gpgArgument>
                                <gpgArgument>--no-tty</gpgArgument>
                            </gpgArguments>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
            <properties>
                <!-- Configure the gpg plugin to use the env vars defined in the Travis build settings -->
                <gpg.keyname>${env.GPG_KEYNAME}</gpg.keyname>
                <gpg.passphrase>${env.GPG_PASSPHRASE}</gpg.passphrase>
            </properties>
        </profile>
    </profiles>
```
Note: if you already have a `<profiles>` section, then simply add the `<profile>` entry with id "central" to it.


### 4.5: Update the coverage-reports module pom.xml

In this step, you'll modify the `modules/coverage-reports/pom.xml` file to prevent the coverage-reports
module from being published on maven central.

- Add this `<properties>` section immediately before the `<dependencies>` section:
```
    <properties>
	<!-- There is no need to publish this module's artifacts on maven central -->
        <maven.deploy.skip>true</maven.deploy.skip>
        <skipNexusStagingDeployMojo>true</skipNexusStagingDeployMojo>
    </properties>
```

- In the `<build>/<plugins>` section, remove the `<plugin>` entry with id "bintray-maven-plugin".

### 4.6: Update your Travis build settings

Open the Travis build settings page for your SDK project
(i.e. `https://travis-ci.com/github/IBM/<repo-name>`),
and add the following environment variables to your build configuration:

- `OSSRH_USERNAME`: the Sonatype username (account) that you created in step 2 and used
to create the "New Project" ticket with Sonatype.

- `OSSRH_PASSWORD`: the password associated with your Sonatype account.

- `GPG_KEYNAME`: this is the name/id of the signing key that will be used by the maven gpg plugin
to sign your artifacts.  The value used here should be the last 8 characters of the key name.
For example, when I run `gpg --list-keys`, I see this output:  

```
$ gpg --list-keys
/home/padams/.gnupg/pubring.kbx
-------------------------------
pub   rsa2048 2020-11-22 [SC] [expires: 2022-11-22]
      B265BB91D80124E463C63119EC4158B05D352E09
uid           [ultimate] Phillip C. Adams <phil_adams@us.ibm.com>
sub   rsa2048 2020-11-22 [E] [expires: 2022-11-22]
```
In this case, I would set the `GPG_KEYNAME` variable to `5D352E09`.

- `GPG_PASSPHRASE`: this is the passphrase associated with your signing key.

Note: when entering the values of environment variables, be sure to escape any special characters
as instructed by the Travis build settings UI.


### 4.7: Commit your changes
After you have completed the above changes to your Java SDK project, you can commit the changes
and push your feature branch, then open a pull request, etc.  Feel free to tag Phil Adams as
a reviewer on your pull request.

When committing your changes, be sure to use a commit message that will trigger a new
release when your pull request is merged and semantic-release processes your PR's commits
(e.g. `git commit -m "feat(build): deploy artifacts to maven central"` or something similar).

Do not merge your PR until the JIRA ticket you opened in step 2 is resolved
(i.e. the Sonatype personnel have
given your Sonatype account the necessary permission to publish artifacts).

## 5: Java SDK Build lifecycle overview

This section describes the various types of builds and build stages associated with
the travis build configuration that you copied from the Java SDK Template repository.

Within the `.travis.yml` file, there are three main stages defined:
```
stages:
  - name: Build-Test
  - name: Semantic-Release
    if: branch = master AND type = push AND fork = false
  - name: Publish-Release
    if: tag IS present
```
When you push a feature branch or open a pull request, the resulting travis build will run
only the `Build-Test` stage, which builds and unit tests the project.

When a pull request is merged into the master branch, the `Build-Test` stage is run,
and (assuming that was successful), the `Semantic-Release` stage is then run.
The `Semantic-Release` stage will run the semantic-release tool which will:
- Analyze the commit messages associated with the commits that were merged into the master branch
since the most recent tagged-release of the project to determine if a new version (tagged-release)
of the project is warranted.
- If a new version is needed, semantic-release will compile a changelog entry for the new version, perform some
additional release management steps and will then push one or more commits back to the github repository along
with a tag to represent the new version of the project.

The tag pushed by the semantic-release tool will trigger a "tagged-release"
Travis build.   This build will run the `Build-Test` stage, then the `Publish-Release` stage.
The `Publish-Release` stage will then run two jobs in parallel:
- the `Publish-Javadoc` job will produce javadocs from the project source, and then
publish them on the git repository's `gh-pages` branch.
- the `Publish-To-Maven-Central` job will publish the project's artifacts on Maven Central
through the use of the nexus-staging-maven-plugin.


## References:
- [OSSRH Guide](https://central.sonatype.org/pages/ossrh-guide.html)
- [Deploying to OSSRH with Apache Maven](https://central.sonatype.org/pages/apache-maven.html)
- [Requirements (related to artifact publishing)](https://central.sonatype.org/pages/requirements.html)
- [Working with PGP Signatures](https://central.sonatype.org/pages/working-with-pgp-signatures.html)
- [Nexus Staging Maven Plugin for Deployment and Release](https://central.sonatype.org/pages/apache-maven.html#nexus-staging-maven-plugin-for-deployment-and-release)
- [Manual Staging Bundle Creation and Deployment](https://central.sonatype.org/pages/manual-staging-bundle-creation-and-deployment.html)
- [General GitHub documentation](https://help.github.com/)
- [Travis CI Documentation](https://docs.travis-ci.com/)
- [Travis CI Encrypting Files](https://docs.travis-ci.com/user/encrypting-files/)
- [Travis CI Command Line Interface (CLI) Documentation](https://github.com/travis-ci/travis.rb#readme)
