# Encrypting secrets
This file provides instructions on how to properly add "secrets" to an SDK project.

## Overview
Within an SDK project, integration tests are used to validate the generated SDK code
while using an actual running instance of a particular IBM Cloud service.

This typically involves the use of external configuration properties to define various
parameters such as: IAM api key, service url, etc.
These configuration properties can be implemented as either exported environment variables
or inserted into a "credentials" file (similar to a Java properties file).

In either case, these properties need to be stored separately from the integration test code
and encrypted because they contain sensitive information (i.e. "secrets"), and the
integration tests will be run as part of the project's automated Travis build.

In the instructions that follow, we'll use examples involving a credentials file,
although the same instructions apply to a file containing a collection of "export" statements
that define environment variables.   The only difference is the precise contents of the file
being encrypted.
In the case of a credentials file, it might look like this:

```sh
EXAMPLE_SERVICE_URL=https://example-service-test.cloud.ibm.com
EXAMPLE_SERVICE_AUTHTYPE=iam
EXAMPLE_SERVICE_AUTH_APIKEY=iam-api-key
```

Whereas, an equivalent file containing exported environment variables would look like this:

```sh
export EXAMPLE_SERVICE_URL=https://example-service-test.cloud.ibm.com
export EXAMPLE_SERVICE_AUTHTYPE=iam
export EXAMPLE_SERVICE_AUTH_APIKEY=iam-api-key
```

## Prerequisites
This section contains one-time setup steps:

1. Install Ruby and RubyGems, using
[these instructions](https://www.ruby-lang.org/en/documentation/installation/)
for your platform.

2. Install the Travis CLI:

```sh
gem install travis
```

3. Verify the installation of the Travis CLI:

```sh
travis -v
```

4. The steps below assume that your project has been enabled in Travis (travis-ci.com or travis.ibm.com).


## Step-by-step Instructions

### 1. Create the "secrets" file
First, create the file containing your configuration properties (aka "credentials").
The examples above in the Overview section involve properties for the "Example Service" service
which has a default service name of "example_service",
which is why the property names start with "EXAMPLE_SERVICE_".
The default service name of a service can be seen in the service client class that was generated
for that service.   For example, the python ExampleServiceV1 class contains this:

```python
class ExampleServiceV1(BaseService):
    """The ExampleService V1 service."""

    DEFAULT_SERVICE_URL = 'http://cloud.ibm.com/mysdk/v1'
    DEFAULT_SERVICE_NAME = 'example_service'
```

It's important to use the correct service name when forming the names of the configuration properties
because the SDK will search for properties whose names start with the service name
(folded to upper case).

Let's suppose we created a file called `example-service.env` in the root of our SDK project that
contains the required configuration properties for our "Example Service" integration test.
It might look like this:

```sh
# Integration test creds for Example Service
EXAMPLE_SERVICE_URL=https://example-service-test.cloud.ibm.com
EXAMPLE_SERVICE_AUTHTYPE=iam
EXAMPLE_SERVICE_AUTH_APIKEY=iam-api-key
```

If possible, after creating this credentials file, you should verify it
by running your integration test to make sure everything is working correctly and the
integration test passes.

Once you have verified your credentials file, you're ready to encrypt it and commit it to your
SDK project's git repository.

### 2. Log into Travis via CLI
#### Travis Enterprise (travis.ibm.com)

If your project will be built on Travis Enterprise (travis.ibm.com) use this command:

```sh
travis login -X --github-token <your-github-enterprise-token> --api-endpoint https://travis.ibm.com/api
```

#### Public Travis (travis-ci.com)
If your project will be built on Public Travis (travis-ci.com) use this command:

```sh
travis login --github-token <your-public-github-token> --com
```

### 3. Encrypt the credentials file
Next, use the Travis CLI `encrypt-file` command to encrypt your credentials file:  

On Travis Enterprise:
```sh
travis encrypt-file example-service.env --api-endpoint https://travis.ibm.com/api
```

On Public Travis:
```sh
travis encrypt-file example-service.env --com
```

Notes:  
- If you are using Travis Enterprise (travis.ibm.com), be sure to include the
- If you are using Public Travis (travis-ci.com), be sure to use the `--com` option on all your travis cli commands
`--api-endpoint https://travis.ibm.com/api` command-line option.
- The `encrypt-file` command output mentions the use of the `--add` option.
Be aware that this option will re-write your `.travis.yml` file and strip out all comments
and blank lines. In other words, proceed with caution.

You should see output from the Travis CLI `encrypt-file` command like this:

```
encrypting example-service.env for phil-adams/test-python-sdk
storing result as example-service.env.enc
storing secure env variables for decryption

Please add the following to your build script (before_install stage in your .travis.yml, for instance):

    openssl aes-256-cbc -K $encrypted_c782ea63ec52_key -iv $encrypted_c782ea63ec52_iv -in example-service.env.enc -out example-service.env -d

Pro Tip: You can add it automatically by running with --add.

Make sure to add example-service.env.enc to the git repository.
Make sure not to add example-service.env to the git repository.
Commit all changes to your .travis.yml.
```

The above Travis CLI `encrypt-file` command performed the following:
- An encryption key and initialization vector were generated by Travis CLI.
- Using the generated encryption key/iv, the `example-service.env` file was encrypted
and the result was stored in `example-servie.env.enc`.
- The encryption key and initialization vector were added as environment variables to the
Travis job settings with names `encrypted_c782ea63ec52_key` and `encrypted_c782ea63ec52_iv`,
respectively (your actual environment variable names will vary, but follow a similar pattern).

Next, we'll update the `.travis.yml` file as instructed by the `encrypt-file` command output above.

### 4. Update .travis.yml
This step involves updating `.travis.yml` so that the encrypted file is decrypted during the automated
Travis build.

We'll simply copy the `openssl` command from the `encrypt-file` command output above and
add it to the `before-install` section of the `.travis.yml` file.

Here's a fragment from `.travis.yml` that shows this:
```

before_install:
- openssl aes-256-cbc -K $encrypted_c782ea63ec52_key -iv $encrypted_c782ea63ec52_iv -in example-service.env.enc -out example-service.env -d

```

**Optional**: You might want to configure `.travis.yml` so that the `openssl` command is not run during
"PR" builds, perhaps as a way to ensure that integration tests are only run during the
corresponding "push" builds that are triggered before the "PR" build.

In this case, our `before_install` section might look like this:
```
before_install:
- '[ "${TRAVIS_PULL_REQUEST}" = "false" ] && openssl aes-256-cbc -K $encrypted_c782ea63ec52_key -iv $encrypted_c782ea63ec52_iv -in example-service.env.enc -out example-service.env -d || true'
```

If you choose this approach, make sure that your integration tests are implemented such that if the
decrypted file (`example-service.env` in this example) is not present during the Travis build,
the tests are either skipped or otherwise exit gracefully to prevent build errors in a PR build.

### 5. Commit your changes
The final step is to commit the changes to your repository, then open a PR or push to remote, etc.

Notes:  
- Make sure to commit only the encrypted file (`example-service.env.enc`) and NOT the original
un-encrypted file (`example-service.env`).
- **Recommended**: add the name of the un-encrypted file to your project's `.gitignore` file to
ensure that the un-encrypted file is never committed, as in these examples:

```
# un-encrypted files:
example-service.env
```

or

```
# un-encrypted files:
*.env
```

Here are the commands we'll use to commit the changes:
1. **Optional** Create a feature branch if you'll be submitting these changes as a PR:
- `git checkout -b add-secrets`

2. Stage your changes:
- `git add .travis.yml`
- `git add example-service.env.enc`
- `git add .gitignore`  (if you updated .gitignore as recommended above)  
Note: These `git add` commands are listed individually to emphasize the point that the
un-encrypted file should not be committed.  Alternative: `git add .`

3. Commit the changes
- `git commit -m "chore(build): add encrypted credentials file for example service"

4. Push changes to remote
- `git push`


# Additional Resources
- [General GitHub documentation](https://help.github.com/)
- [Ruby Installation Instructions](https://www.ruby-lang.org/en/documentation/installation/)
- [Travis CI Documentation](https://docs.travis-ci.com/)
- [Travis CI Encrypting Files](https://docs.travis-ci.com/user/encrypting-files/)
- [Travis CI Command Line Interface (CLI) Documentation](https://github.com/travis-ci/travis.rb#readme)
