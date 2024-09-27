[![CLA assistant](https://cla-assistant.io/readme/badge/IBM/ibm-cloud-sdk-common)](https://cla-assistant.io/IBM/ibm-cloud-sdk-common)

# IBM Cloud SDK Common
This project contains documentation and other resources related to IBM Cloud SDKs

<details><summary>Table of Contents</summary>

<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      npx markdown-toc -i README.md --maxdepth 4
  -->

<!-- toc -->

- [Overview](#overview)
- [SDK Project Catalog](#sdk-project-catalog)
- [Using the SDK](#using-the-sdk)
  * [SDK Structure](#sdk-structure)
  * [Importing a service in your application](#importing-a-service-in-your-application)
  * [Constructing service clients](#constructing-service-clients)
    + [Setting client options programmatically](#setting-client-options-programmatically)
    + [Using external configuration](#using-external-configuration)
    + [Service URLs](#service-urls)
    + [Complete configuration-loading process](#complete-configuration-loading-process)
  * [Authentication](#authentication)
    + [1. Create an IAM authenticator programmatically](#1-create-an-iam-authenticator-programmatically)
    + [2. Create an IAM authenticator from configuration](#2-create-an-iam-authenticator-from-configuration)
    + [3. Create a Bearer Token authenticator programmatically](#3-create-a-bearer-token-authenticator-programmatically)
  * [Passing parameters to operations](#passing-parameters-to-operations)
  * [Receiving operation responses](#receiving-operation-responses)
  * [Pagination](#pagination)
  * [Sending HTTP headers](#sending-http-headers)
    + [Sending HTTP headers with all requests](#sending-http-headers-with-all-requests)
    + [Sending request HTTP headers](#sending-request-http-headers)
  * [Transaction IDs](#transaction-ids)
  * [Configuring the HTTP Client](#configuring-the-http-client)
  * [Configuring Request Timeouts](#configuring-request-timeouts)
  * [Automatic retries](#automatic-retries)
    + [Programmatically](#programmatically)
    + [With external configuration](#with-external-configuration)
  * [Disabling SSL Verification - Discouraged](#disabling-ssl-verification---discouraged)
    + [With external configuration](#with-external-configuration-1)
    + [Programmatically](#programmatically-1)
  * [Error Handling](#error-handling)
  * [Logging](#logging)
  * [Synchronous and asynchronous requests](#synchronous-and-asynchronous-requests)
- [Questions](#questions)
- [Open source @ IBM](#open-source--ibm)
- [License](#license)

<!-- tocstop -->

</details>

<!-- --------------------------------------------------------------- -->
## Overview

IBM Cloud SDK client libraries allow developers to programmatically interact with various IBM Cloud services.
SDKs are provided for the following languages:
- Go
- Java
- Node.js
- Python

## SDK Project Catalog
The following table provides links to the various IBM Cloud SDK projects associated with IBM Cloud service
categories:

Service Category | Go | Java | Node.js | Python | Mobile
--- | --- | --- | --- | --- | ---
Analytics Engine | [Go SDK](https://github.com/IBM/ibm-iae-go-sdk) | [Java SDK](https://github.com/IBM/ibm-iae-java-sdk) | [Node.js SDK](https://github.com/IBM/ibm-iae-node-sdk) | [Python SDK](https://github.com/IBM/ibm-iae-python-sdk)
API Gateway | [Go SDK](https://github.com/IBM/apigateway-go-sdk) | | | | |
App ID (Authorization v4 / Profiles) | | | [Node.js SDK](https://github.com/ibm-cloud-security/appid-serversdk-nodejs) / [JS SDK](https://github.com/ibm-cloud-security/appid-clientsdk-js) | | [Android SDK](https://github.com/ibm-cloud-security/appid-clientsdk-android)<br /> [Swift SDK](https://github.com/ibm-cloud-security/appid-clientsdk-swift)
Blockchain | [Go SDK](https://github.com/IBM-Blockchain/ibp-go-sdk) | | | | |
Cloudant | [Go SDK](https://github.com/IBM/cloudant-go-sdk) | [Java SDK](https://github.com/IBM/cloudant-java-sdk) | [Node.js SDK](https://github.com/IBM/cloudant-node-sdk) | [Python SDK](https://github.com/IBM/cloudant-python-sdk)
Cloud Object Storage | [Go SDK](https://github.com/IBM/ibm-cos-sdk-go) | [Java SDK](https://github.com/IBM/ibm-cos-sdk-java) | [Node.js SDK](https://github.com/IBM/ibm-cos-sdk-js) | [Python SDK](https://github.com/IBM/ibm-cos-sdk-python)
Cloud Object Storage Configuration | [Go SDK](https://github.com/IBM/ibm-cos-sdk-go-config) | [Java SDK](https://github.com/IBM/ibm-cos-sdk-java-config) | [Node.js SDK](https://github.com/IBM/ibm-cos-sdk-js-config) | [Python SDK](https://github.com/IBM/ibm-cos-sdk-python-config)
Cloud Pak For Data | [Go SDK](https://github.com/ibm/cloudpakfordata-go-sdk) | [Java SDK](https://github.com/IBM/cloudpakfordata-java-sdk) | [Node.js SDK](https://github.com/IBM/cloudpakfordata-node-sdk) | [Python SDK](https://github.com/IBM/cloudpakfordata-python-sdk)
Code Engine | [Go SDK](https://github.com/IBM/code-engine-go-sdk) | [Java SDK](https://github.com/IBM/code-engine-java-sdk) | [Node.js SDK](https://github.com/IBM/code-engine-node-sdk) | [Python SDK](https://github.com/IBM/code-engine-python-sdk)
Container Registry | [Go SDK](https://github.com/IBM/container-registry-go-sdk) | [Java SDK](https://github.com/IBM/container-registry-java-sdk) | [Node.js SDK](https://github.com/IBM/container-registry-node-sdk) | [Python SDK](https://github.com/IBM/container-registry-python-sdk) 
DB2 On Cloud v4 | [Go SDK](https://github.com/ibmdb/go_ibm_db) | [Java SDK](https://github.com/ibmdb/java-db2) | [Node.js SDK](https://github.com/ibmdb/node-ibm_db) | [Python SDK](https://github.com/ibmdb/python-ibmdb)
DNS Services | [Go SDK](https://github.com/IBM/dns-svcs-go-sdk) | | | | |
Key Protect | [Go SDK](https://github.com/IBM/keyprotect-go-client) | [Java SDK](https://github.com/IBM/keyprotect-java-client) | [Node.js SDK](https://github.com/IBM/keyprotect-nodejs-client) | [Python SDK](https://github.com/IBM/keyprotect-python-client) <br /> [Redstone SDK](https://github.com/IBM/redstone)
Networking Services | [Go SDK](https://github.com/IBM/networking-go-sdk) | [Java SDK](https://github.com/IBM/networking-java-sdk) | [Node.js SDK](https://github.com/IBM/networking-node-sdk) | [Python SDK](https://github.com/IBM/networking-python-sdk)
Platform Services  | [Go SDK](https://github.com/IBM/platform-services-go-sdk) | [Java SDK](https://github.com/IBM/platform-services-java-sdk) | [Node.js SDK](https://github.com/IBM/platform-services-node-sdk) | [Python SDK](https://github.com/IBM/platform-services-python-sdk)
Projects  | [Go SDK](https://github.com/IBM/project-go-sdk) | | [Node.js SDK](https://github.com/IBM/project-node-sdk) | [Python SDK](https://github.com/IBM/project-python-sdk) | |
Push Notifications  | | [Java Push Server Side SDK](https://github.com/ibm-bluemix-mobile-services/bms-pushnotifications-serversdk-java) | [Node.js Push Server Side SDK](https://github.com/ibm-bluemix-mobile-services/bms-pushnotifications-serversdk-nodejs)<br /> [JS Web Push Client SDK](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-javascript-webpush) | | [Android Push Client SDK](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-android-push)<br /> [React.js Push SDK](https://github.com/ibm-bluemix-mobile-services/bms-push-react-native)<br /> [Swift Push Client SDK](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push)<br /> [Swift Push Server Side SDK](https://github.com/ibm-bluemix-mobile-services/bms-pushnotifications-serversdk-swift)<br />
Security and Compliance Center | [Go SDK](https://github.com/IBM/scc-go-sdk) | [Java SDK](https://github.com/IBM/scc-java-sdk) | [Node.js SDK](https://github.com/IBM/scc-node-sdk) | [Python SDK](https://github.com/IBM/scc-python-sdk) 
Secrets Manager | [Go SDK](https://github.com/IBM/secrets-manager-go-sdk) | [Java SDK](https://github.com/IBM/secrets-manager-java-sdk) | [Node.js SDK](https://github.com/IBM/secrets-manager-node-sdk) | [Python SDK](https://github.com/IBM/secrets-manager-python-sdk)
Virtual Private Cloud | [Go SDK](https://github.com/IBM/vpc-go-sdk) | [Java SDK](https://github.com/IBM/vpc-java-sdk) | [Node.js SDK](https://github.com/IBM/vpc-node-sdk) | [Python SDK](https://github.com/IBM/vpc-python-sdk)
Watson | [Go SDK](https://github.com/watson-developer-cloud/go-sdk) | [Java SDK](https://github.com/watson-developer-cloud/java-sdk) | [Node.js SDK](https://github.com/watson-developer-cloud/node-sdk) | [Python SDK](https://github.com/watson-developer-cloud/python-sdk)
Watson Health Cognitive Services | [Go SDK](https://github.com/IBM/whcs-go-sdk) | [Java SDK](https://github.com/IBM/whcs-java-sdk) | [Node.js SDK](https://github.com/IBM/whcs-node-sdk) | [Python SDK](https://github.com/IBM/whcs-python-sdk)


## Using the SDK
This section provides general information on how to use the services contained in an
IBM Cloud SDK client library.
The programming examples below will illustrate how the various activities can be performed for
each of the programming languages mentioned above using a mythical service named "Example Service"
that is defined in [example-service.yaml](example-service.yaml).

### SDK Structure
When you use an IBM Cloud SDK client library within your application to interact with a
particular IBM Cloud service, your application code will appear to be invoking local functions
rather than invoking REST operations over the network.

In fact, your application _is_ invoking local functions that are provided by the SDK client library.
Each SDK client library contains a collection of one or more related IBM Cloud services, where
each service "client"  - the client-side view of a particular IBM Cloud service -
is implemented in its own class (Java, Node.js, Python) or struct (Go).
Within this class or struct, there will be methods that represent
the various operations that are defined in the API for that service.
The function contained within each of these methods handles the various steps of
invoking a REST operation over the network:
1. constructing an HTTP request message
2. sending the request message to the server via the network
3. receiving the HTTP response message from the server
4. consuming the response message and returning the results back to your application code

The mythical "Example Service" service client mentioned above is implemented as:
- Go: the `ExampleServiceV1` struct within the `exampleservicev1` package
- Java: the `ExampleService` class within the `com.ibm.cloud.mysdk.example_service.v1` package
- Node.js: the `ExampleServiceV1` class within the `example-service/v1` module 
- Python: the `ExampleServiceV1` class within the `example_service_v1` module

### Importing a service in your application
Each IBM Cloud SDK project includes a README.md file which contains a table of the services
included in that SDK project, along with language-specific information needed for you
to use these services within your application code.

Before you can use a particular service within your application, you first need to import
the correct language-specific programming constructs (package, class, struct, etc.)
from the SDK client library that contains that service.

<details><summary>Go</summary>
For Go SDK client libraries, each IBM Cloud service will exist within its own package in
the Go module that represents the SDK project as a whole.
To use the mythical "Example Service" service client in your Go application, you would add
an import to your application code like this:

```go
import {
    "github.com/IBM/mysdk/exampleservicev1"
}
```
Note: this example assumes that the "Example Service" service client exists in
the `exampleservicev1` package within the `github.com/IBM/mysdk` module.
Be sure to consult the service table for the Go SDK client library you are using
to obtain the correct module import path and service package name for the service(s) that
you will use in your application.

After you add this import to your application, you can run the command `go mod tidy`
to automatically add the necessary module import to your application's `go.mod` file.
Alternatively, you can manually add `github.com/IBM/mysdk <version>` to your `go.mod` file's
`require` section, where `<version>` represents the version of the SDK project that you
would like to use.  For more details about the `go.mod` file, please see this
[reference](https://golang.org/doc/modules/gomod-ref).

</details>
<details><summary>Java</summary>
For Java SDK client libraries, each IBM Cloud service is packaged within its own jar containing
the classes associated with that service.  Each jar is identified by its artifact coordinates,
which consist of the jar's group id, artifact id and version in the form:  

```xml
<group-id>:<artifact-id>:<version>
```
For example, the mythical "Example Service" service client might be contained within the
jar with artifact coordinates: `com.ibm.cloud:example-service:1.0.0`.
The service table within each Java SDK project's README.md file will include the
artifact coordinates for each service contained in the project.

Before you can use a service in your application, you first need to define a dependency within
your project's maven pom.xml file or gradle build script.

For maven, a dependency defined for the "Example Service" would look like this:

```xml
<dependency>
    <groupId>com.ibm.cloud</groupId>
    <artifactId>example-service</artifactId>
    <version>1.0.0</version>
</dependency>
```

For gradle, the dependency would look like this:

```
compile 'com.ibm.cloud:example-service:1.0.0'
```

Be sure to consult the service table for the Java SDK client library you are using
to obtain the correct artifact coordinates for the service(s) you will use in your application.

After defining an appropriate dependency within your build script, you would then import the
classes needed by your application.   For example, in order to construct an instance of the
"Example Service" service client in your application, you would need to import the
java class that implements that service, like this:

```java
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;
```

Your application will likely use a collection of various classes provided by the
service's jar in addition to the primary class for that service.   Be sure to include
import statements in your application for each class that you need to use from the
service's jar.

Note that some Java SDK client libraries provide a collection of javadocs for the various
classes provided by that client library (look for a link on the Java SDK project's main
github.com page).  If present, these javadocs can be consulted to explore the
set of available classes and determine which classes might be needed by your application.
</details>
<details><summary>Node.js</summary>
For Node.js SDK client libraries, each IBM Cloud service exists within its own folder inside the
package that represents the SDK project as a whole.
To use the mythical "Example Service" service client in your Node.js application, add an import
to your application like this:

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');
```

You would also need to add a dependency for the `mysdk` package within your application project's
`package.json` file as well.

Note: this example assumes that the "Example Service" service client exists in the
`example-service` folder contained within the `mysdk` package.

Be sure to consult the service table for the Node.js SDK client library you are using
to obtain the correct import path for the service(s) you will use in your application.
</details>
<details><summary>Python</summary>
For Python SDK client libraries, each IBM Cloud service exists in its own module inside the
package that represents the SDK project as a whole.
To use the mythical "Example Service" service client in your Python application,
add an import like this:

```python
from mysdk import ExampleServiceV1
```

This imports the `ExampleServiceV1` class, which is the primary class associated with
the "Example Service" service client.
If your application needs to use additional service-related definitions (such as classes
which model the schemas in the service's API definition), use an import like this:

```python
from mysdk.example_service_v1 import Resource, Resources
```
Note: in this example, `Resource` and `Resources` are classes that are defined
in the service's module (in addition to the ExampleServiceV1 service class).

If you would like to import *all* definitions related to a particular service,
you can use an import like this:
```python
from mysdk.example_service_v1 import *
```

Be sure to consult the service table for the Python SDK client library you are using
to obtain the correct module name and service class name for the service(s) you will use in your application.
</details>

### Constructing service clients

The SDK allows you to construct the service client in one of two ways:
1. Setting client options programmatically
2. Using external configuration properties

#### Setting client options programmatically
Here's an example of how to construct an instance of the "Example Service" client
while specifying various client options (authenticator, service endpoint URL, etc.) programmatically:

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v5/core"
    "github.com/IBM/mysdk/exampleservicev1"
}

// Create an IAM authenticator.
authenticator := &core.IamAuthenticator{
    ApiKey: "<iam-api-key>",
}

// Construct an "options" struct for creating the service client.
options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,                               // required
    URL:           "https://example-service.cloud.ibm.com/v1",  // optional
}

// Construct the service client.
myService, err := exampleservicev1.NewExampleServiceV1(options)
if err != nil {
    panic(err)
}

// Service operations can now be invoked using the "myService" variable.
```

</details>
<details><summary>Java - for Java core versions prior to 9.7.0 (deprecated)</summary>

```java
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;
import com.ibm.cloud.sdk.core.security.Authenticator;
import com.ibm.cloud.sdk.core.security.IamAuthenticator;

// Create an IAM authenticator.
Authenticator authenticator = new IamAuthenticator("<iam-api-key>");

// Construct the service client.
ExampleService myService = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

// Set our custom service URL (optional).
myService.setServiceUrl("https://example-service.cloud.ibm.com/v1");

// Service operations can now be called using the "myService" variable.
```

</details>
<details><summary>Java - for Java core version 9.7.0 and above</summary>

```java
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;
import com.ibm.cloud.sdk.core.security.Authenticator;
import com.ibm.cloud.sdk.core.security.IamAuthenticator;

// Create an IAM authenticator.
Authenticator authenticator = new IamAuthenticator.Builder()
    .apikey("<iam-api-key>")
    .build();

// Construct the service client.
ExampleService myService = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

// Set our custom service URL (optional).
myService.setServiceUrl("https://example-service.cloud.ibm.com/v1");

// Service operations can now be called using the "myService" variable.
```

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');
const { IamAuthenticator } = require('mysdk/auth');

// Create an IAM authenticator.
const authenticator = new IamAuthenticator({
  apikey: '<iam-api-key>',
});

// Construct the service client.
const myService = new ExampleServiceV1({
  authenticator,                                          // required
  serviceUrl: 'https://example-service.cloud.ibm.com/v1', // optional
});

// Service operations can now be invoked using the "myService" variable.
```

</details>
<details><summary>Python</summary>

```python
from mysdk import ExampleServiceV1
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

# Create an IAM authenticator.
authenticator = IAMAuthenticator('<iam-api-key>')

# Construct the service client.
my_service = ExampleServiceV1(authenticator=authenticator)

# Set our custom service URL (optional)
my_service.set_service_url('https://example-service.cloud.ibm.com/v1')

# Service operations can now be invoked using the "my_service" variable.
```

</details>

#### Using external configuration
For a typical application deployed to the IBM Cloud, it might be convenient or necessary to avoid hard-coding
certain service client options (IAM API Key, service URL, etc.) within the application.
Instead, the SDK allows you to store these values in configuration properties external to your
application.

##### Define configuration properties
First, define the configuration properties to be used by your application.
These properties can be implemented as either:
1. Exported environment variables
2. Stored in a *credentials* file.

In the examples that follow, we'll use environment variables to implement our configuration properties.
Each property name is of the form: `<service-name>_<property-key>`.
Here is an example of some configuration properties for the "Example Service" service:

```sh
export EXAMPLE_SERVICE_URL=https://example-service.cloud.ibm.com/v1
export EXAMPLE_SERVICE_AUTH_TYPE=iam
export EXAMPLE_SERVICE_APIKEY=<iam-api-key>
```

The service name "example_service" is the default service name for the "Example Service" client,
so the SDK will (by default) look for properties that start with this prefix folded to upper case.

##### Construct service client
After you have defined the configuration properties for your application, you can
construct an instance of the service client like the examples below:

<details><summary>Go</summary>

```go

// Construct service client via config properties using default service name ("example_service")
myService, err := exampleservicev1.NewExampleServiceV1UsingExternalConfig(
        &exampleservicev1.ExampleServiceV1Options{})
if err != nil {
    panic(err)
}
```

The `NewExampleServiceV1UsingExternalConfig` function will:
1. construct an authenticator using the environment variables above (an IAM authenticator using `"<iam-api-key>"` as the api key).
2. initialize the service client to use service URL "https://example-service.cloud.ibm.com/v1".

</details>
<details><summary>Java</summary>

```java
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;

ExampleService myService = ExampleService.newInstance();
```

The `ExampleService.newInstance()` method will:
1. construct an authenticator using the environment variables above (an IAM authenticator using `"<iam-api-key>"` as the api key).
2. initialize the service client to use service URL "https://example-service.cloud.ibm.com/v1".

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');

const myService = ExampleServiceV1.newInstance();
```

The `ExampleServiceV1.newInstance()` method will:
1. construct an authenticator using the environment variables above (an IAM authenticator using `"<iam-api-key>"` as the api key).
2. initialize the service client to use service URL "https://example-service.cloud.ibm.com/v1".

</details>
<details><summary>Python</summary>

```python
from mysdk import ExampleServiceV1
my_service = ExampleServiceV1.new_instance()
```

The `ExampleServiceV1.new_instance()` method will:
1. construct an authenticator using the environment variables above (an IAM authenticator using `"<iam-api-key>"` as the api key).
2. initialize the service client to use service URL "https://example-service.cloud.ibm.com/v1".

</details>

##### Storing configuration properties in a file
Instead of exporting your configuration properties as environment variables, you can store the properties
in a *credentials* file.   Here is an example of a credentials file that contains the properties from the examples above:

```sh
# Contents of "mycredentials.env"
EXAMPLE_SERVICE_URL=https://example-service.cloud.ibm.com/v1
EXAMPLE_SERVICE_AUTH_TYPE=iam
EXAMPLE_SERVICE_APIKEY=iam-api-key
```

You would then provide the name of the credentials file via the `IBM_CREDENTIALS_FILE` environment variable:

```sh
export IBM_CREDENTIALS_FILE=/myfolder/mycredentials.env
```

When the SDK needs to look for configuration properties, it will detect the `IBM_CREDENTIALS_FILE` environment
variable, then load the properties from the specified file.


#### Service URLs

The URLs used to construct service clients - either programmatically, or via external configuration -
are not validated. Take care that your URL is valid, ensuring at least the presence of a valid protocol and host.

Note that a [user information](https://tools.ietf.org/html/rfc3986#section-3.2.1) component should not be
present as it may interfere with the correct operation of the configured authenticators, for example by
forcing the SDK's underlying HTTP client to add an additional authentication scheme.

#### Complete configuration-loading process

The above examples provide a glimpse of two specific ways to provide external configuration to the SDK
(environment variables and credentials file specified via the `IBM_CREDENTIALS_FILE` environment variable).

The complete configuration-loading process supported by the SDK is as follows:
1. Look for a credentials file whose name is specified by the `IBM_CREDENTIALS_FILE` environment variable
2. Look for a credentials file at `<current-working-directory>/ibm-credentials.env`
3. Look for a credentials file at `<user-home-directory>/ibm-credentials.env`
4. Look for environment variables whose names start with `<upper-case-service-name>_` (e.g. `EXAMPLE_SERVICE_`)

At each of the above steps, if one or more configuration properties are found for the specified service,
those properties are then returned to the SDK and any subsequent steps are bypassed.


### Authentication
IBM Cloud SDKs support a variety of authentication schemes through the use of authenticators.

An authenticator is a component that supports a specific type of authentication and is associated
with a service client.   When an operation is invoked on a service client, the service client
uses the associated authenticator instance to "authenticate" the outbound request, which typically
involves adding an Authorization header containing an appropriate security token.

Examples of authenticators include: 
- Basic Authentication: supports a basic auth username and password
- Bearer Token Authentication: supports a user-supplied bearer token
- Identity and Access Management (IAM) Authentication: supports a user-supplied API key that is automatically
exchanged for an IAM access token by interacting with the IAM token service.
- Container Authentication: supports IAM-based authentication within a Kubernetes-managed compute resource in which
the Compute Resource Identity feature is configured
- VPC Instance Authentication: supports IAM-based authentication within a VPC-managed compute resource
in which the Compute Resource Identity feature is configured
- Cloud Pack for Data Authentication: supports authentication flows implemented specifically as part of
the Cloud Pack for Data offering
- Multi-Cloud Saas Platform (MCSP) Authentication: supports a user-supplied API key that is automatically
exchanged for an access token by interacting with the MCSP offering's token service

Detailed information about these authenticators can be found in the various language-specific
core libraries:
- [Go](https://github.com/IBM/go-sdk-core/blob/main/Authentication.md)
- [Java](https://github.com/IBM/java-sdk-core/blob/main/Authentication.md)
- [Node.js](https://github.com/IBM/node-sdk-core/blob/main/Authentication.md)
- [Python](https://github.com/IBM/python-sdk-core/blob/main/Authentication.md)

IBM Cloud services support token-based Identity and Access Management (IAM) authentication.
The information within the rest of this section will focus on the use of Identity and Access Management
(IAM) authentication and will demonstrate how you can construct and use an IAM Authenticator instance.
For other types of authenticators, you can see similar usage details within the language-specific
documentation linked above.

With IAM authentication, you supply an API key and the authenticator will exchange that API key
for an access token (a bearer token) by interacting with the IAM token service.
The access token is then added (via the Authorization header) to each outbound request
to provide the required authentication information.

Access tokens are valid only for a limited amount of time and must be
periodically refreshed. The IAM authenticator will automatically detect the need
to refresh the access token and will interact with the IAM token service as needed to obtain
a fresh access token, relieving the SDK user from that burden.

There are two ways to construct and use an IAM authenticator:
- programmatically
- from configuration

#### 1. Create an IAM authenticator programmatically
In this scenario, you construct an IAM authenticator instance,
supplying your IAM API key programmatically.

The IAM authenticator will:  
- Use your API key to obtain an access token from the IAM token service
- Ensure that the access token is valid
- Include the access token in each outbound request within an Authorization header
- Refresh the access token when it expires

##### Examples

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v5/core"
    "<appropriate-git-repo-url>/exampleservicev1"
}
...
// Create the authenticator.
authenticator, err := core.NewIamAuthenticatorBuilder().
    SetApiKey("myapikey").
    Build()
if err != nil {
    panic(err)
}

// Create the service options struct.
options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,
}

// Construct the service instance.
service, err := exampleservicev1.NewExampleServiceV1(options)
if err != nil {
    panic(err)
}

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Java - for Java core versions prior to 9.7.0 (deprecated)</summary>

```java
import com.ibm.cloud.sdk.core.security.Authenticator;
import com.ibm.cloud.sdk.core.security.IamAuthenticator;
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;

Authenticator authenticator = new IamAuthenticator("<iam-api-key>");
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);
```

</details>
<details><summary>Java - for Java core version 9.7.0 and above</summary>

```java
import com.ibm.cloud.sdk.core.security.IamAuthenticator;
import <sdk_base_package>.ExampleService.v1.ExampleService;
...
// Create the authenticator.
IamAuthenticator authenticator = new IamAuthenticator.Builder()
    .apikey("myapikey")
    .build();

// Create the service instance.
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Node.js</summary>

```js
const { IamAuthenticator } = require('ibm-cloud-sdk-core');
const ExampleServiceV1 = require('<sdk-package-name>/example-service/v1');

const authenticator = new IamAuthenticator({
  apikey: 'myapikey',
});

const options = {
  authenticator,
};

const service = new ExampleServiceV1(options);

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Python</summary>

```python
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
from <sdk-package-name>.example_service_v1 import *

# Create the authenticator.
authenticator = IAMAuthenticator('myapikey')

# Construct the service instance.
service = ExampleServiceV1(authenticator=authenticator)

# 'service' can now be used to invoke operations.
```

</details>

#### 2. Create an IAM authenticator from configuration
In this scenario, you define the IAM API key in external configuration properties, and then
construct the service client using the SDK's configuration-based interface.
This allows you to avoid hard-coding your API key within
your application code or having to concern yourself with loading it from configuration properties.

##### Examples
The programming examples in this section assume that external configuration properties like the following have been
configured:

```sh
export EXAMPLE_SERVICE_AUTH_TYPE=iam
export EXAMPLE_SERVICE_APIKEY=myapikey
```

<details><summary>Go</summary>

```go
import {
    "<appropriate-git-repo-url>/exampleservicev1"
}
...

// Create the service options struct.
options := &exampleservicev1.ExampleServiceV1Options{
    ServiceName:   "example_service",
}

// Construct the service instance.
service, err := exampleservicev1.NewExampleServiceV1UsingExternalConfig(options)
if err != nil {
    panic(err)
}

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Java</summary>

```java
import <sdk_base_package>.ExampleService.v1.ExampleService;
...

// Create the service instance.
ExampleService service = ExampleService.newInstance("example_service");

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('<sdk-package-name>/example-service/v1');

const options = {
  serviceName: 'example_service',
};

const service = ExampleServiceV1.newInstance(options);

// 'service' can now be used to invoke operations.
```

</details>
<details><summary>Python</summary>

```python
from <sdk-package-name>.example_service_v1 import *

# Construct the service instance.
service = ExampleServiceV1.new_instance(service_name='example_service')

# 'service' can now be used to invoke operations.
```

</details>

#### 3. Create a Bearer Token authenticator programmatically
In this scenario, you are responsible for obtaining the access token from the IAM token service and
refreshing or reacquiring it as needed.  Once you obtain the access token, construct a
Bearer Token authenticator and supply the access token.

Note that you can also construct a Bearer Token authenticator using external configuration properties,
similar to Scenario 2 above with the IAM authenticator, but it is less useful to use that approach with the
Bearer Token authenticator because you must manage the access token yourself.

##### Examples

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v5/core"
    "<appropriate-git-repo-url>/exampleservicev1"
}
...
// Create the authenticator.
bearerToken := // ... obtain bearer token value ...
authenticator := core.NewBearerTokenAuthenticator(bearerToken)
if err != nil {
    panic(err)
}


// Create the service options struct.
options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,
}

// Construct the service instance.
service, err := exampleservicev1.NewExampleServiceV1(options)
if err != nil {
    panic(err)
}

// 'service' can now be used to invoke operations.
...
// Later, if your bearer token value expires, you can set a new one like this:
newToken := // ... obtain new bearer token value
authenticator.BearerToken = newToken
```

</details>
<details><summary>Java</summary>

```java
import com.ibm.cloud.sdk.core.security.BearerTokenAuthenticator;
import <sdk_base_package>.ExampleService.v1.ExampleService;
...
String bearerToken = // ... obtain bearer token value ...

// Create the authenticator.
BearerTokenAuthenticator authenticator = new BearerTokenAuthenticator(bearerToken);

// Create the service instance.
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

// 'service' can now be used to invoke operations.
...
// Later, if your bearer token value expires, you can set a new one like this:
newToken = // ... obtain new bearer token value
authenticator.setBearerToken(newToken);
```

</details>
<details><summary>Node.js</summary>

```js
const { BearerTokenAuthenticator } = require('ibm-cloud-sdk-core');
const ExampleServiceV1 = require('<sdk-package-name>/example-service/v1');

const bearerToken = // ... obtain bearer token value ...
const authenticator = new BearerTokenAuthenticator({
  bearerToken: bearerToken,
});

const options = {
  authenticator,
};

const service = new ExampleServiceV1(options);

// 'service' can now be used to invoke operations.
...
// Later, if your bearer token value expires, you can set a new one like this:
newToken = // ... obtain new bearer token value
authenticator.bearerToken = newToken;
```

</details>
<details><summary>Python</summary>

```python
from ibm_cloud_sdk_core.authenticators import BearerTokenAuthenticator
from <sdk-package-name>.example_service_v1 import *

# Create the authenticator.
authenticator = BearerTokenAuthenticator(<your_bearer_token>)

# Construct the service instance.
service = ExampleServiceV1(authenticator=authenticator)

# 'service' can now be used to invoke operations.
...
# Later, if your bearer token value expires, you can set a new one like this:
new_token = '<new token>'
authenticator.set_bearer_token(new_token);
```

</details>

### Passing parameters to operations
For certain languages (namely Go, Java and Node.js) a structure is defined for each operation
as a container for the parameters associated with that operation.

The use of these parameter container structures (instead of listing each operation parameter within the
argument list of the operations) allows for future expansion of the API (within certain
guidelines) without impacting application compatibility.

The language-specific sections that follow provide additional information and examples
regarding these parameter container structures:

<details><summary>Go</summary>

For Go, a struct is defined for each operation, where the name of the struct will be
`<operation-name>Options` and it will contain a field for each operation parameter.  

Here's an example of an options struct for the `GetResource` operation:

```go
// GetResourceOptions : The GetResource options.
type GetResourceOptions struct {
	// The id of the resource to retrieve.
	ResourceID *string `json:"resource_id" validate:"required"`

	...
}
```

In this example, the `GetResource` operation has one parameter - `ResourceID`.
When invoking this operation, the application first creates an instance of the `GetResourceOptions`
struct, sets the parameter value within it, then passes the options struct instance to the operation.
Along with the "options" struct, a constructor function is also provided.  

Here's an example:

```go
options := myService.NewGetResourceOptions("resource-id-1")

result, detailedResponse, err := myService.GetResource(options)
```

</details>
<details><summary>Java</summary>

For Java, a class is defined for each operation, where the name of the class will be
`<operation-name>Options` and it will contain a field for each operation parameter.  

Here's an example of an options class for the `getResource` operation:

```java
/**
 * The getResource options.
 */
public class GetResourceOptions extends GenericModel {

  protected String resourceId;

  ...

}
```

In this example, the `getResource` operation has one parameter - `resourceId`.
When invoking this operation, the application first creates an instance of the `GetResourceOptions`
class, sets the parameter value within it, then passes the options class instance to the operation.
Along with the "options" class, a nested "Builder" class is also provided as a convenient way to
construct instances of the class using the Java "builder" pattern.

Here's an example:

```java
import com.ibm.cloud.sdk.core.http.Response;
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;
import com.ibm.cloud.mysdk.example_service.v1.model.GetResourceOptions;
import com.ibm.cloud.mysdk.example_service.v1.model.Resource;

...

ExampleService myService = /* construct service client instance */

GetResourceOptions options = new GetResourceOptions.Builder()
    .resourceId("resource-id-1")
    .build();

Response<Resource> response = myService.getResource(options).execute();
Resource result = response.getResult();
```

</details>
<details><summary>Node.js</summary>

For Node.js, an interface is defined for each operation, where the name of the interface will be
`<operation-name>Params` and it will contain a field for each operation parameter.  

Here's an example of a params object for the `getResource` operation:

```js
  /** Parameters for the `getResource` operation. */
  export interface GetResourceParams {
    /** The id of the resource to retrieve. */
    resourceId: string;

    ...
  }
```

In this example, the `getResource` operation has one parameter - `resourceId`.
When invoking this operation, the application creates an instance of the `GetResourceParams`
interface, sets the parameter value within it, then passes the params object to the operation.

Here's an example:

```js
const params = {
    resourceId: 'resource-id-1',
}

myService.getResource(params).then(res => {
    ...
});
```

</details>
<details><summary>Python</summary>
Because the Python language supports named arguments (i.e. `kwargs`) there is no need to define
any sort of container structure for parameters in order to allow for future expansion of the
API without impacting applications.

Here's an example of how the `ExampleServiceV1.get_resource()` operation would be invoked:

```python
detailedResponse = my_service.get_resource(resource_id='resource-id-1')
```

</details>

### Receiving operation responses
This section contains language-specific information about how an application receives operation responses.

<details><summary>Go</summary>

Each operation will return the following values:  
1. `result` - An operation-specific response object (if the operation is defined as returning a response object).  
2. `detailedResponse` - An instance of the `core.DetailedResponse` struct. This will contain the following fields:  
  * `StatusCode` - the HTTP status code returned in the response message  
  * `Headers` - the HTTP headers returned in the response message. Keys in the map are canonicalized
    (see [CanonicalHeaderKey](https://golang.org/pkg/net/http/#CanonicalHeaderKey))
  * `Result` - the operation result (if available). This is the same value returned in the `result` return value mentioned above.
  * `RawResult` - the raw response body as a byte array if there was a problem un-marshalling a JSON response body
    or if the operation was unsuccessful and the response body content is not JSON.
3. `err` - An error object.  This will be nil if the operation was successful, or non-nil  
if an error occurred.

Here is an example of how to access the response and get additional Information
beyond the response object:

```go
result, detailedResponse, err := myService.GetResource(options)

responseHeaders := detailedResponse.GetHeaders()
responseId := responseHeaders.Get("Response-Id")
```

</details>
<details><summary>Java</summary>

Each operation will return an instance of `com.ibm.sdk.cloud.sdk.core.http.Response<T>`
where `T` is the class representing the specific
response model associated with the operation (operations that return no response object
will return an instance of `com.ibm.sdk.cloud.sdk.core.http.Response<Void>` instead).  

Here's an example of how to access that response and get additional information
beyond the response object:  

```java
// Invoke the operation.
GetResourceOptions options = /* construct options model */
Response<Resource> response = myservice.getResource(options).execute();

// Extract the operation's response model instance (result).
Resource resource  = response.getResult();

// Retrieve response headers.
Headers responseHeaders = response.getHeaders();
System.out.println("Response header names: " + responseHeaders.names());

String responseId = responseHeaders.values("response-id").get(0);
```

</details>
<details><summary>Node.js</summary>

Each operation will return a response via a Promise.
The response is an object containing the result of the operation, HTTP status code and text message,
and the HTTP response headers.  

Here is an example of how to access the various fields and headers from an operation response:  

```js
myService.getResource({
  'resourceId': 'resource-id-1',
}).then((response) => {
    // result is the response object returned in the response body
    // status is the HTTP status code returned in the response
    // headers is the set of HTTP headers returned in the response
    // statusText is the text message associated with the HTTP status code
    const { result, status, headers, statusText } = response;
    console.log(JSON.stringify(headers, null, 4));
    transactionId = headers['response-id'];
  }).catch((err) => {
    if (err.status && err.statusText) {
      console.log("Error status code: " + err.status + " (" + err.statusText + ")");
    }
    console.log("Error message:     " + err.message);
  });
```

</details>
<details><summary>Python</summary>

Each operation will return a DetailedResponse instance which encapsulates the operation response
object (if applicable), the HTTP status code and response headers.  

Here's an example of how to access that response and get additional information
beyond the response object:  

```python
detailedResponse = my_service.get_resource(resource_id='resource-id-1')
print(detailedResponse)

responseHeaders = detailedResponse.get_headers()

responseId = responseHeaders.get('response-id')
```

This would display a `DetailedResponse` instance having the structure:

```python
{
    'result': <response object returned by operation>,
    'headers': { <http response headers> },
    'status_code': <http status code>
}
```

</details>


### Pagination
For list-type operations that comply with the
[API Handbook's pagination requirements](https://cloud.ibm.com/docs/api-handbook?topic=api-handbook-pagination),
SDKs may contain special "Pager" classes (Java, Node.js and Python) or structs (Go) that can be used 
as a convenience wrapper around the list-type operation.

This section provides language-specific examples of how to use these Pager classes and structs.
Each Pager class or struct will contain the following methods (the actual language-specific names
will be provided below in each language section):
  - `has_next()`: returns true if there are potentially more items to be retrieved, or false otherwise.
  - `get_next()`: returns the next page of items by invoking the list-type operation.  The number of items returned
  depends on the selected page size (limit parameter) and the number of remaining items that are available to be returned.
  - `get_all()`: returns all of the available items by repeatedly invoking the list-type operation.  **This operation should be
  used with caution**.  The use of this method with certain resource types might result in an excessive amount of data being returned.
  If in doubt, use the `has_next()` and `get_next()` methods to retrieve a single page at a time, and process each page before
  retrieving the next page.

One thing to note is that each Pager class or struct uses the corresponding list-type operation to
retrieve the actual items, and simply provides a more convenient way to invoke the operation.
Examples will be provided in each language section below.

For the purposes of these examples, suppose that an API defines a `Cloud` resource, and includes a `list_clouds` operation that supports
[`token-based pagination`](https://cloud.ibm.com/docs/api-handbook?topic=api-handbook-pagination#token-based-pagination).
The operation returns an instance of the `CloudCollection` schema, which contains a property named `clouds` which is
an array of `Cloud` instances. In this example, the Pager class or struct would be named `CloudsPager`.

<details><summary>Go</summary>

For Go, the names of the methods provided in each Pager struct are:
  - `HasNext()`
  - `GetNext()`
  - `GetAll()`

This example shows how to use the `HasNext()` and `GetNext()` methods to
retrieve results one page at a time:

```go
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'ListClouds' options model and set page size to 10.
listCloudsOptions := myService.NewListCloudsOptions()
listCloudsOptions.SetLimit(int64(10))

// Construct the Pager.
pager, err := myService.NewCloudsPager(listCloudsOptions)

// Define a slice of Cloud instances to hold all of the results.
var allResults []exampleservicev1.Cloud

// Retrieve each page of results and add to "allResults".
for pager.HasNext() {
  nextPage, err := pager.GetNext()
  allResults = append(allResults, nextPage...)
}

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the ListClouds() operation.
```

This example shows how to use the `GetAll()` method to retrieve all the available results:

```go
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'ListClouds' options model and set page size to 10.
listCloudsOptions := myService.NewListCloudsOptions()
listCloudsOptions.SetLimit(int64(10))

// Construct the Pager.
pager, err := myService.NewCloudsPager(listCloudsOptions)

// Retrieve all of the results.
allResults, err := pager.GetAll()

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the ListClouds() operation.
```

</details>
<details><summary>Java</summary>

For Java, the names of the methods provided in each Pager class are:
  - `hasNext()`
  - `getNext()`
  - `getAll()`

This example shows how to use the `hasNext()` and `getNext()` methods to
retrieve results one page at a time:

```java
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'listClouds' options model and set page size to 10.
ListCloudsOptions listCloudsOptions = new ListCloudsOptions.Builder()
  .limit(10)
  .build();

// Define a list of Cloud instances to hold all of the results.
List<Cloud> allResults = new ArrayList<>();

// Construct the Pager.
CloudsPager pager = new CloudsPager(myService, listCloudsOptions);

// Retrieve each page of results and add to "allResults".
while (pager.hasNext()) {
  List<Cloud> nextPage = pager.getNext();
  allResults.addAll(nextPage);
}

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the listClouds() operation.
```

This example shows how to use the `getAll()` method to retrieve all the available results:

```java
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'listClouds' options model and set page size to 10.
ListCloudsOptions listCloudsOptions = new ListCloudsOptions.Builder()
  .limit(10)
  .build();

// Construct the Pager.
CloudsPager pager = new CloudsPager(myService, listCloudsOptions);

// Retrieve all of the results.
List<Cloud> allResults = pager.getAll();

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the listClouds() operation.
```

</details>
<details><summary>Node.js</summary>

For Node.js, the names of the methods provided in each Pager class are:
  - `hasNext()`
  - `getNext()`
  - `getAll()`

This example shows how to use the `hasNext()` and `getNext()` methods to
retrieve results one page at a time:

```js
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'listClouds' params object and set page size to 10.
const params = {
  limit: 10,
};

// Define a list of Cloud instances to hold all of the results.
const allResults = [];

// Construct the Pager.
const pager = new ExampleServiceV1.CloudsPager(myService, params);

// Retrieve each page of results and add to "allResults".
while (pager.hasNext()) {
  const nextPage = await pager.getNext();
  allResults.push(...nextPage);
}

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the listClouds() operation.
```

This example shows how to use the `getAll()` method to retrieve all the available results:

```js
// Assume that 'myService' has already been initialized as in other code examples.

// Construct the 'listClouds' params object and set page size to 10.
const params = {
  limit: 10,
};

// Construct the Pager.
const pager = new ExampleServiceV1.CloudsPager(myService, params);

// Retrieve all of the results.
const allResults = await pager.getAll();

// "allResults" will now contain all the Cloud instances that were
// returned by the successive invocations of the listClouds() operation.
```

</details>
<details><summary>Python</summary>

For Python, the names of the methods provided in each Pager class are:
  - `has_next()`
  - `get_next()`
  - `get_all()`

This example shows how to use the `has_next()` and `get_next()` methods to
retrieve results one page at a time:

```python
# Assume that 'my_service' has already been initialized as in other code examples.

# Define a list of Cloud instances to hold all of the results.
all_results = []

# Construct the Pager using 'my_service' as the client and a page size of 10.
pager = CloudsPager(
    client=my_service,
    limit=10,
)

# Retrieve each page of results and add to "all_results".
while pager.has_next():
    next_page = pager.get_next()
    all_results.extend(next_page)

# "all_results" will now contain all the Cloud instances that were
# returned by the successive invocations of the list_clouds() operation.
```

This example shows how to use the `get_all()` method to retrieve all the available results:

```python
# Assume that 'my_service' has already been initialized as in other code examples.

# Construct the Pager using 'my_service' as the client and a page size of 10.
pager = CloudsPager(
    client=my_service,
    limit=10,
)

# Retrieve all of the results.
all_results = pager.get_all()

# "all_results" will now contain all the Cloud instances that were
# returned by the successive invocations of the list_clouds() operation.
```

</details>


### Sending HTTP headers

#### Sending HTTP headers with all requests
You can configure a set of custom HTTP headers in the service client instance and these headers
will be included with all requests invoked from that client.
<details><summary>Go</summary>

For Go, the custom headers are set on the service client instance by using the
`SetDefaultHeaders(http.Header)` function, like this:

```go
customHeaders := http.Header{}
customHeaders.Add("Custom-Header", "custom_value")
myService.SetDefaultHeaders(customHeaders)

// "Custom-Header" will now be included with all requests invoked from "myservice".
```

</details>
<details><summary>Java</summary>

For Java, the custom headers are set on the service client instance by using the
`setDefaultHeaders(Map<String, String> headers)` function, like this:

```java
Map<String, String> headers = new HashMap<String, String>();
headers.put("Customer-Header", "custom_value");

myService.setDefaultHeaders(headers);

// "Custom-Header" will now be included with all subsequent requests invoked from "myservice".
```

</details>
<details><summary>Node.js</summary>

For Node.js, the custom headers are passed via the `headers` field of the `UserOptions` object
used to construct the service client instance, like this:

```js
import ExampleServiceV1 from 'mysdk/example-service/v1';

const myService = ExampleServiceV1.newInstance({
  headers: {
    'Custom-Header': 'custom_value',
  },
});

// "Custom-Header" will now be included with all requests invoked from "myService".
```

</details>
<details><summary>Python</summary>

For Python, the custom headers are set on the service client instance by using the
`set_default_headers()` function, like this:

```python

headers = {
    'Custom-Header': 'custom_value'
}

my_service.set_default_headers(headers)

# "Custom-Header" will now be included with all requests invoked from "my_service".
```

</details>


#### Sending request HTTP headers
You can also pass custom HTTP headers with an individual request.
<details><summary>Go</summary>

For Go, just add the custom headers to the "options" struct prior to calling the operation,
like this:

```go
options := myService.NewGetResourceOptions("resource-id-1")

customHeaders := make(map[string]string)
customHeaders["Custom-Header"] = "custom_value"

options.SetHeaders(customHeaders)

result, detailedResponse, err := myservice.GetResource(options)

// "Custom-Header" will be sent along with the "GetResource" request.
```

</details>
<details><summary>Java</summary>

For Java, just add the custom headers to the `ServiceCall` object before calling the `execute()`
method, like this:

```java
Response<Resource> response = myservice.getResource(options)
  .addHeader("Custom-Header", "custom_value")
  .execute();
```

</details>
<details><summary>Node.js</summary>

For Node.js, just add the custom headers to the "params" object via the optional `headers`
field prior to calling the operation, like this:

```js
const getResourceParams = {
  resourceId: 'resource-id-1',
  headers: {
    'Custom-Header': 'custom_value',
  }
};

myService.getResource(getResourceParams).then(res => {
  // Custom-Header will have been sent with this request
});
```

</details>
<details><summary>Python</summary>

For Python, just pass the optional `headers` parameter when invoking the operation,
like this:

```python
resourceInstance = my_service.get_resource(
    resource_id='resource-id-1',
    headers={'Custom-Header': 'custom_value'}).get_result()
```

</details>


### Transaction IDs
Each API invocation will receive a response that contains a transaction ID in the HTTP header.
This transaction ID can be useful for troubleshooting and accessing relevant logs from your service
instance. Check the documentation of the service for the correct field name of this property.

Here are examples of how to retrieve the response header:
<details><summary>Go</summary>

```go
result, detailedResponse, err := myService.GetResource(options)

transactionId := detailedResponse.GetHeaders().Get("transaction-id-field-name")
```

</details>
<details><summary>Java</summary>

For Java, you can retrieve headers from either the `Response<T>` return value of the `execute()`
method, or from the exception in case of an error:

```java
String transactionId;
try {
  Response<Resource> response = myService.getResource(options).execute();
  transactionId = response.getHeaders().values("transaction-id-field-name").get(0);
} catch (ServiceResponseException e) {
  transactionId = e.getHeaders().values("transaction-id-field-name").get(0);
}
```

</details>
<details><summary>Node.js</summary>

```js

const params = {
  resourceId: 'resource-id-1',
}

myService.getResource(params).then(
  response => {
    transactionId = response.headers['transaction-id-field-name'];
  },
  err => {
    transactionId = TBD;
  }
);
```

</details>
<details><summary>Python</summary>

```python
try:
    response = my_service.get_resource(resource_id='resource-id-1')
    transaction_id = response.get_headers().get('transaction-id-field-name')
except ApiException as e:
    transaction_id = e.http_response.headers.get('transaction-id-field-name')
```

</details>


### Configuring the HTTP Client

You have the ability to configure the underlying HTTP client that is used by the SDK to interact
with the service endpoint.  The sections below describe how this is done for each language.
<details><summary>Go</summary>

For Go, you can create your own `http.Client` instance and set it on your service client,
like this:

```go
import {
    "net/http"
    "time"
    "github.com/IBM/go-sdk-core/v5/core"
}

// Construct a new http.Client instance with a user-supplied proxy function.
myHTTPClient := core.DefaultHTTPClient()
myHTTPClient.Transport.Proxy = myProxyFunc

// Set the service's Client field with the SetHTTPClient() method.
myService.Service.SetHTTPClient(myHttpClient)

// This request will use the user-supplied proxy function.
result, detailedResponse, err := myService.GetResource(options)
```

For more details on configuring the `http.Client` instance,
see [this link](https://golang.org/pkg/net/http/#Client).
</details>
<details><summary>Java</summary>

For Java, you can configure certain HTTP client options by using the `HttpConfigOptions` class
provided by the Java SDK Core library.
After configuring the `HttpConfigOptions` instance, use the service client's
`configureClient()` method to configure the HTTP client instance held by the service client,
like this:

```java
import java.net.InetSocketAddress;
import java.net.Proxy;
import com.ibm.cloud.sdk.core.http.HttpConfigOptions;


// Configure a proxy in an HttpConfigOptions instance.
Proxy proxy =
    new Proxy(Proxy.Type.HTTP, new InetSocketAddress("myproxy.mydomain.com", 8080));
HttpConfigOptions options =
    new HttpConfigOptions.Builder().proxy(proxy).build();

myService.configureClient(options);
```

You can also use the service client's `getClient()` and `setClient()` methods to supply your own
`OkHttpClient` instance, like this:

```java

// Retrieve the current HTTP client instance.
OkHttpClient client = myService.getClient();

// Modify the call timeout to be 5 minutes.
client = client.newBuilder().callTimeout(5, TimeUnit.MINUTES).build();

// Set the new HTTP client on the service.
myService.setClient(client);
```

For more information about the `OkHttpClient` class, please see
[this link](https://square.github.io/okhttp/3.x/okhttp/index.html?okhttp3/OkHttpClient.html).

</details>
<details><summary>Node.js</summary>

For Node.js, you have full control over the HTTPS Agent used to make requests.
This is available for both the service client and the authenticators that make network requests
(e.g. `IamAuthenticator`). The package [`axios`](https://github.com/axios/axios) is used to make requests.
All [`axios` configuration parameters](https://github.com/axios/axios#request-config) are supported by the
constructors of both service clients and authenticators.
Described below are scenarios where this configuration capability is needed.
Note that this functionality is applicable only for Node.js environments - these configurations will have
no effect in the browser.

##### Accessing the HTTP Client
In most scenarios, passing HTTP configuration parameters to the SDK client constructor is sufficient.
However, there may be cases in which access to the actual instance of `axios` is necessary, such as setting interceptors.

For this purpose, use the method `getHttpClient` on the service client instance.
```js
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'mysdk/auth';

const myService = new ExampleServiceV1({
  authenticator: new IamAuthenticator({ apikey: '{apikey}' }),
});

const axiosInstance = myService.getHttpClient();
```

##### Proxy
To invoke requests behind an HTTP proxy (e.g. firewall), a special tunneling agent must be used.
Use the package [`https-proxy-agent`](https://github.com/TooTallNate/proxy-agents/tree/main/packages/https-proxy-agent) for this.
Configure this agent with your proxy information, then pass it in as the HTTPS agent in the
service constructor and the authenticator constructor, if applicable.
Additionally, you **must** set `proxy` to `false` in the constructor(s).
Here's an example:

```js
import { HttpsProxyAgent } from 'https-proxy-agent';
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'mysdk/auth';

const proxyUrl = 'http://some.host.org:1234'; // Host: some.host.org, Port: 1234
const agent = new HttpsProxyAgent(proxyUrl);

const myService = new ExampleServiceV1({
  authenticator: new IamAuthenticator({
    apikey: '{apikey}',
    httpsAgent: agent,
    proxy: false,
  }),
  httpsAgent: agent,
  proxy: false,
});
```

##### Sending custom certificates
To send custom certificates as a security measure in your request, use the `cert`, `key`,
and/or `ca` properties of the HTTPS Agent.
See [this documentation](https://nodejs.org/api/tls.html#tls_tls_createsecurecontext_options)
for more information about the options.
Note that the entire contents of the file must be provided - not just the file name.

```js
import fs = require('fs');
import https = require('https');
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'ibm-mysdk/auth';

const certFile = fs.readFileSync('./my-cert.pem');
const keyFile = fs.readFileSync('./my-key.pem');

const myService = new ExampleServiceV1({
  authenticator: new IamAuthenticator({
    apikey: '{apikey}',
    httpsAgent: new https.Agent({
      key: keyFile,
      cert: certFile,
    })
  }),
  httpsAgent: new https.Agent({
    key: keyFile,
    cert: certFile,
  }),
});
```

</details>
<details><summary>Python</summary>

For Python, you can configure certain options in the underlying http client used to
invoke requests (e.g. timeout), you can use the `set_http_config()` method, like this:

```python
my_service.set_http_config({'timeout': 120})
response = my_service.get_resource(resource_id='resource-id-1').get_result()
```

Here is a list of some of the configuration properties that can be set with the `set_http_config()` method:
- [timeout](https://requests.readthedocs.io/en/master/user/quickstart/#timeouts)
- [allow_redirects](https://requests.readthedocs.io/en/master/user/quickstart/#redirection-and-history)
- [proxies](https://requests.readthedocs.io/en/master/user/advanced/#proxies)
- [verify](https://requests.readthedocs.io/en/master/user/advanced/#ssl-cert-verification)
- [cert](https://requests.readthedocs.io/en/master/user/advanced/#client-side-certificates)

To access the existing client, use the `get_http_client` method. This returns the ["Session" from the `requests` package](https://docs.python-requests.org/en/master/user/advanced/#session-objects).

```python
mySession = my_service.get_http_client()
mySession.headers.update({'x-my-header': 'true'})
```

The client can also be overriden or reset with the `set_http_client` method.

```python
newSession = requests.Session()
my_service.set_http_client(newSession)
```

For more information regarding http client configuration,
please see [this link](https://requests.readthedocs.io/en/master/)

</details>


### Configuring Request Timeouts

This section describes how to configure request timeouts in the various SDKs.

<details><summary>Go</summary>

The recommended way to configure request timeouts in a Go SDK is to use an instance of
[`context.Context`](https://golang.org/pkg/context#Context).

Starting with version 3.15.0 of the SDK generator, the Go generator now generates
two methods for each operation, one which accepts a `context.Context` instance
as the first parameter and one with only the single "options model" parameter.

Here is an example of how to use the `context.Context` to invoke an operation with a
10-second timeout:

```go
// Create a context with a 10-second timeout.
ctx, cancelFunc := context.WithTimeout(context.Background(), 10 * time.Second)
defer cancelFunc()

// Create the resource.
result, detailedResponse, err := myService.CreateResourceWithContext(ctx, createResourceOptionsModel)
```

When constructing a Context, you receive two values - the context itself, along with the "cancel function".
The returned cancel function can also be invoked in order to cancel an in-flight request.
Note also that by using the `context.Context` instance with the `<operation-name>WithContext` method,
the timeout applies only to a single operation invocation.

By default, a service instance will use a default HTTP client with a 30-second connection timeout, and no request or read timeout.   You can configure a client with your own timeout values, then set it on the service instance by calling the `BaseService.SetHTTPClient()` method.
</details>
<details><summary>Java</summary>
Here is an example of how to set a request timeout in Java:  

```java
// Retrieve the current HTTP client instance.
OkHttpClient client = myService.getClient();

// Modify the call timeout to be 10 seconds.
client = client.newBuilder().callTimeout(10, TimeUnit.SECONDS).build();

// Set the new HTTP client on the service.
myService.setClient(client);
```

The request timeout of 10 seconds will be used in each operation invoked using `myService1`.

By default, the client has a 60-second connect timeout, a 90-second read timeout and a 60-second write timeout.  The default client does not set a `call timeout` (which covers the entire HTTP call... including the connection, write and read steps), but you can set that as in the example above.
</details>
<details><summary>Node</summary>
Here is an example of how to set a request timeout in Node:  

```js
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'ibm-mysdk/auth';

// Construct an instance of the service with a 10-second timeout (expressed in ms).
const myService = new ExampleServiceV1({
  authenticator: new IamAuthenticator({ apikey: '{apikey}' }),
  timeout: 10000,
});
```

The request timeout of 10 seconds will be used in each operation invoked using `myService`.

By default the client has no request timeout.
</details>
<details><summary>Python</summary>
As mentioned in the "Configuring the HTTP Client" section above, you can configure the options
in the http client as in this example:  

```python
# Configure a 10-second combined timeout covering the connection and read operations.
# In other words, the server should start sending back the response within 10 seconds.
my_service.set_http_config({'timeout': 10})
```

This timeout setting will be used in each operation invoked using `my_service`.

By default, the client will use a 60-second combined timeout, which means that the server should start sending back the response within 60 seconds.
</details>


### Automatic retries
Applications that invoke REST APIs sometimes encounter errors such as
`429 - Too Many Requests`, `503 - Service Unavailable`, etc.
It can be inconvenient and time-consuming to code around these types of errors in your application.
This section provides information about how to enable automatic retries.

<details><summary>Go</summary>

The Go SDK supports a generalized retry feature that can automatically retry on common
errors. The default configuration (up to 4 retries with max retry interval of 30 seconds,
along with exponential backoff if no `Retry-After` response header is present)
should suffice for most applications, but the retry feature
is customizable to support unique requirements.

</details>
<details><summary>Java</summary>

The Java SDK supports a generalized retry feature that can automatically retry on common
errors.  The default configuration (up to 4 retries with max retry interval of 30 seconds,
along with exponential backoff if no `Retry-After` response header is present)
should suffice for most applications, but the retry feature
is customizable to support unique requirements.

</details>
<details><summary>Node</summary>

The Node SDK supports a generalized retry feature that can automatically retry on common
errors. The default configuration (up to 4 retries, 
max retry interval of 30 seconds, along with exponential backoff if no `Retry-After` response
header is present) should suffice for most applications, but the retry feature is
customizable to support unique requirements.

</details>
<details><summary>Python</summary>

The Python SDK supports a generalized retry feature that can automatically retry on common
errors. The default configuration (up to 4 retries with max retry interval of 30 seconds,
along with exponential backoff if no `Retry-After` response header is present)
should suffice for most applications, but the retry feature
is customizable to support unique requirements.

</details>

#### Programmatically

<details><summary>Go</summary>

To enable automatic retries programmatically in the Go SDK, use the service client's
`EnableRetries()` method, as in this example:  

```go
// Construct the service client.
myService, err := exampleservicev1.NewExampleServiceV1(options)

// Enable automatic retries (with max retries 5, max interval 20 seconds).
myService.EnableRetries(5, 20 * time.Second)

// Create the resource.
result, detailedResponse, err := myService.CreateResource(createResourceOptionsModel)
```

In this example, the `CreateResource()` operation will be retried up to 5 times
with a maximum retry interval of 20 seconds.

If a "retryable" error response (e.g. 429, 503, etc.) contains
the `Retry-After` header, the value of that response header will be used
as the retry interval, subject to a maximum of 20 seconds.  If no `Retry-After` header
is found in the response, then an exponential backoff policy will be used such
that successive retries would use wait times of 1, 2, 4, 8 and 16 seconds.
</details>
<details><summary>Java</summary>
To enable automatic retries programmatically in the Java SDK, use the service client's
`enableRetries()` method, as in this example:  

```java
// Construct the service client.
myService = new ExampleServiceV1(serviceName, authenticator);

// Enable automatic retries (with max retries 5, max interval 20 seconds).
myService.enableRetries(5, 20);

// Create the resource.
CreateResourceOptions options = /* construct options model */
Response<Resource> response = myService.createResource(options).execute();
```

In this example, the `createResource()` operation will be retried up to 5 times
with a maximum retry interval of 20 seconds.

If a "retryable" error response (e.g. 429, 503, etc.) contains
the `Retry-After` header, the value of that response header will be used
as the retry interval, subject to a maximum of 20 seconds.  If no `Retry-After` header
is found in the response, then an exponential backoff policy will be used such
that successive retries would use wait times of 1, 2, 4, 8 and 16 seconds.
</details>
<details><summary>Node.js</summary>
To enable automatic retries programmatically in the Node SDK, use the service client's
`enableRetries()` method, as in this example:

```node
// Construct the service client.
const myService = new ExampleServiceV1({ authenticator: myAuthenticator })

// Enable automatic retries (with max retries 5, max retry interval 20 seconds).
myService.enableRetries({ maxRetries: 5, maxRetryInterval: 20 });

// Create the resource.
const response = myService.createResource({ resourceId: "3", name: "Sample Book Title" });
```

In this example, the `createResource()` operation will be retried up to 5 times
with a maximum retry interval of 20 seconds.

If a "retryable" error response (e.g. 429, 503, etc.) contains
the `Retry-After` header, the value of that response header will be used
as the retry interval.  If no `Retry-After` header
is found in the response, then an exponential backoff policy will be used such
that successive retries would use wait times of 1, 2, 4, 8, and 16 seconds.
</details>
<details><summary>Python</summary>
To enable automatic retries programmatically in the Python SDK, use the service client's
`enable_retries()` method, as in this example:

```python
// Construct the service client.
my_service = ExampleServiceV1(authenticator=my_authenticator)

// Enable automatic retries (with max retries 5, max retry interval 20.0 seconds).
my_service.enable_retries(max_retries=5, retry_interval=20.0)

// Create the resource.
response = my_service.create_resource(resource_id="3", name="Sample Book Title")
```

In this example, the `create_resource()` operation will be retried up to 5 times
with a maximum retry interval of 20 seconds.

If a "retryable" error response (e.g. 429, 503, etc.) contains
the `Retry-After` header, the value of that response header will be used
as the retry interval.  If no `Retry-After` header
is found in the response, then an exponential backoff policy will be used such
that successive retries would use wait times of 1, 2, 4, 8, and 16 seconds.
</details>


#### With external configuration
If you are constructing your service client with external configuration properties, you can
enable automatic retries in the service client by setting
properties as in the example below for the "Example Service" service:  

```sh
export EXAMPLE_SERVICE_URL=https://example-service.cloud.ibm.com/v1
export EXAMPLE_SERVICE_AUTH_TYPE=iam
export EXAMPLE_SERVICE_APIKEY=<iam-api-key>

export EXAMPLE_SERVICE_ENABLE_RETRIES=true
export EXAMPLE_SERVICE_MAX_RETRIES=3
export EXAMPLE_SERVICE_RETRY_INTERVAL=20
```

If the `<service-name>_ENABLE_RETRIES` property is defined as `true`, then retries will be enabled.
You can optionally define the `<service-name>_MAX_RETRIES` and `<service-name>_RETRY_INTERVAL`
properties to configure values for the maximum number of retries (default is 4) and maximum retry
interval (default is 30 seconds).

After setting these properties, be sure to construct your service client similar to the
examples in the [Construct service client](#construct-service-client) section above.


### Disabling SSL Verification - Discouraged

The SDK allows you to configure the HTTP client to disable SSL verification.
**Note that this has serious security implications - only do this if you really mean to!** ⚠️

#### With external configuration
If you are constructing your service client with external configuration properties, you can
disable SSL verification in both the authenticator and the service client by setting
the `<service-name>_DISABLE_SSL` (service client) and `<service-name>_AUTH_DISABLE_SSL`
(authenticator) properties `true`.
Here's an example of how to do this for the "Example Service" service:

```sh
export EXAMPLE_SERVICE_URL=https://example-service.cloud.ibm.com/v1
export EXAMPLE_SERVICE_AUTH_TYPE=iam
export EXAMPLE_SERVICE_APIKEY=<iam-api-key>

export EXAMPLE_SERVICE_DISABLE_SSL=true
export EXAMPLE_SERVICE_AUTH_DISABLE_SSL=true
```

After setting these properties, be sure to construct your service client similar to the
examples in the [Construct service client](#construct-service-client) section above.

#### Programmatically
Alternatively, you can disable SSL verification programmatically.
See the expandable sections below to see how this is done in each language:
<details><summary>Go</summary>

For Go, you can disable SSL verification in both the service client and in the authenticator
like this:  

```go

// Construct authenticator with DisableSSLVerification true
authenticator := &core.IamAuthenticator{
    ApiKey: "<iam-api-key>",
    DisableSSLVerification: true,
}

// Construct service client
myService, err := exampleservicev1.NewExampleServiceV1(
    &exampleservicev1.ExampleServiceV1Options{
        Authenticator: authenticator,
    }
}
if err != nil {
    panic(err)
}

// Disable SSL verification in service client
myService.Service.DisableSSLVerification()

```

**Note: for a given instance of a service client, the "disable SSL" and "automatic retries"
features are mutually exclusive.**
Each feature requires a specific configuration of the underlying HTTP client and are not
currently supported simultaneously.
</details>
<details><summary>Java - for Java core versions prior to 9.7.0 (deprecated)</summary>

For Java, you can disable SSL verification in both the authenticator and service client,
like this:

```java

Authenticator authenticator = new IamAuthenticator("<iam-api-key>");
authenticator.setDisableSSLVerification(true);

myService = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

HttpConfigOptions options =
    new HttpConfigOptions.Builder().disableSslVerification(true).build();

myService.configureClient(options);
```

</details>
<details><summary>Java - for Java core version 9.7.0 and above</summary>

For Java, you can disable SSL verification in both the authenticator and service client,
like this:

```java

Authenticator authenticator = new IamAuthenticator.Builder()
    .apikey("<iam-api-key>")
    .disableSSLVerification(true)
    .build();
myService = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

HttpConfigOptions options =
    new HttpConfigOptions.Builder().disableSslVerification(true).build();

myService.configureClient(options);
```

</details>
<details><summary>Node.js</summary>
For Node.js, set `disableSslVerification` to `true` in the service constructor and/or
authenticator constructor, like this:  

```js
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'ibm-mysdk/auth';

const myservice = new ExampleServiceV1({
  authenticator: new IamAuthenticator({
    apikey: '<apikey>',
    disableSslVerification: true,   // this will disable SSL verification for requests to the IAM token server
    }),
  disableSslVerification: true, // this will disable SSL verification for any requests invoked with this service client instance
});
```

</details>
<details><summary>Python</summary>
For Python, use the `set_disable_ssl_verification()` method on the service client, like this:

```python
my_service.set_disable_ssl_verification(True)
```

When you disable SSL verification, the python `urllib3` library might log an excessive number of warning
messages, like this:

```
.../connectionpool.py:1004: InsecureRequestWarning: Unverified HTTPS request is being made to host '<my-insecure-host>'.
Adding certificate verification is strongly advised.
See: https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
```

To suppress these warning messages, you can add this code to your application:

```python
import urllib3

urllib3.disable_warnings()
```

See [this link](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings)
for more details.
</details>


### Error Handling
This section provides information about how to deal with errors resulting from request invocations.

The SDK uses standard HTTP response codes to indicate whether a method completed successfully.
HTTP response codes in the 2xx range indicate success.
A response in the 4xx range is some sort of failure, and a response in the 5xx range usually indicates an internal system error
that cannot be resolved by the user.

<details><summary>Go</summary>

In the case of an error response from the server endpoint, the Go SDK will do the following:  
1. The service method (operation) will return a non-nil `error` object.  This `error` object will
contain the error message retrieved from the HTTP response if possible, or a generic error message
otherwise.
2. The `detailedResponse` return value will contain the following fields:  -
- `StatusCode`: the HTTP status code returned in the response.
- `Headers`: the HTTP headers returned in the response.
- `Result`: if the operation returned a JSON error response, this will contain
the unmarshalled response in the form of a `map[string]interface{}`.
This allows the application to examine all of the error information returned in the HTTP
response message.
- `RawResult`: if the operation returned a non-JSON error response, this will contain
the raw response body as a `[]byte`.

Here's an example of checking the `error` object after invoking the `GetResource` operation:

```go
// Call the GetResource operation and receive the returned Resource.
options := myservice.NewGetResourceOptions("bad-resource-id")
result, detailedResponse, err := myservice.GetResource(options)
if err != nil {
    fmt.Println("Error retrieving the resource: ", err.Error())
    fmt.Println("   status code: ", detailedResponse.StatusCode)
    if (detaliedResponse.Result != nil) {
        fmt.Println("   full error response: ", detailedResponse.Result)
    } else {
        fmt.Println("   raw error response: ", detailedResponse.RawResult)
    }
}
```

</details>
<details><summary>Java</summary>

In the case of an error response from the server endpoint, the Java SDK will throw an exception
from the `com.ibm.cloud.sdk.core.service.exception` package.

All service exceptions contain the following fields:  
- `statusCode`: the HTTP response code that was returned in the response
- `message`: a message that describes the error
- `headers`: the HTTP headers returned in the response
- `debuggingInfo`: a `Map<String, Object>` which contains the unmarshalled error response object
in the event that a JSON error response is returned.  This will provide more information beyond
the error message.
- `responseBody`: a string containing the entire body from the response

An operation may also throw an `IllegalArgumentException` if it detects missing or invalid
input arguments.

Here's an example of how to catch and process specific exceptions that may be returned
from an operation:

```
import com.ibm.cloud.sdk.core.service.exception.BadRequestException;
import com.ibm.cloud.sdk.core.service.exception.NotFoundException;
import com.ibm.cloud.sdk.core.service.exception.RequestTooLargeException;
import com.ibm.cloud.sdk.core.service.exception.ServiceResponseException;
import com.ibm.cloud.sdk.core.service.exception.UnauthorizedException;

...

try {
  // Invoke an operation
  Response<Resource> response = myService.getResource(options).execute();

  // Process response...

} catch (BadRequestException e) {
  // Handle Bad Request (400) exception
} catch (UnauthorizedException e) {
  // Handle Unauthorized (401) exception
} catch (NotFoundException e) {
  // Handle Not Found (404) exception
} catch (RequestTooLargeException e) {
  // Handle Request Too Large (413) exception
} catch (ServiceResponseException e) {
  // Base class for all exceptions caused by error responses from the service
  System.out.println("Service returned status code "
    + e.getStatusCode() + ": " + e.getMessage());
  System.out.println("Detailed error info:\n" + e.getDebuggingInfo().toString());
  System.out.println("Error response body:\n" + e.getResponseBody());
}
```

</details>
<details><summary>Node.js</summary>

In the case of an error response from the server endpoint, the Node SDK will
create an `Error` object with information that describes the error that occurred.
This error object is passed as the first parameter to the callback function for the method,
and will contain the following fields:  
- `status`: the HTTP status code that was returned in the response
- `statusText`: a text description of the status code
- `message`: the error message returned in the response
- `body` : the error response body.  If a JSON response was returned, this will be
the stringified response body.  If a non-JSON response was returned, this will
simply be the raw response body obtained from the response.

Here's an example of how to access the error object:

```js
myService.getResource({
  'resourceId': 'bad-resource-id',
}).then((response) => {
    // ...handle successful response...
  }).catch((err) => {
    // ...handle error response...
    console.log("Error status code: " + err.status + " (" + err.statusText + ")");
    console.log("Error message:     " + err.message);
  });
```

</details>
<details><summary>Python</summary>

In the case of an error response from the server endpoint, the Python SDK will throw an exception
with the following fields:  
- `code`: the HTTP status code that was returned in the response
- `message`: a message that describes the error
- `http_response`: a `requests.Response` instance that contains the operation response information

Here's an example:

```python
from ibm_cloud_sdk_core import ApiException

try:
  response = my_service.get_resource(resource_id='does not exist')
except ApiException as e:
  print("Method failed with status code " + str(e.code) + ": " + e.message)
  print("Response body: " + e.http_response.text)
```

</details>


### Logging
This section describes how to enable logging within the various SDKs.

<details><summary>Go</summary>

Within Go SDKs, the underlying Go core library uses a basic logging facility that supports
various logging levels: Error, Info, Warn, Debug.
By default, the Go core logger is configured with log level "Error", which means
that only error messages will be displayed. A logger configured at log level "Warn" would
display "Error" and "Warn" messages (but not "Info" or "Debug"), etc.

For details on how to enable and configure logging in the Go core library, [see this](https://github.com/IBM/go-sdk-core#logging).

</details>
<details><summary>Java</summary>

For Java SDKs, the underlying Java core library uses the
[java.util.logging](https://docs.oracle.com/en/java/javase/11/core/java-logging-overview.html) framework
for logging various messages.

For details on how to enable and configure logging in the Java core library, [see this](https://github.com/IBM/java-sdk-core#logging).

</details>
<details><summary>Node.js</summary>

For Node.js SDKs, the underlying Node.js core library uses the [`debug`](https://github.com/visionmedia/debug) package
for logging various messages.

For details on how to enable and configure logging in the Node.js core library, [see this](https://github.com/IBM/node-sdk-core#logging).

</details>
<details><summary>Python</summary>

For Python SDKs, the underlying Python core library uses Python's builtin `logging` module for logging various messages.

For details on how to enable and configure logging in the Python core library, [see this](https://github.com/IBM/python-sdk-core#logging).

</details>

### Synchronous and asynchronous requests

For some languages, the SDK supports both synchronous (blocking) and asynchronous (non-blocking)
execution of service methods.

<details><summary>Go</summary>
The Go SDK supports only synchronous execution of service methods.
</details>
<details><summary>Java</summary>

For Java, all service methods implement the [`ServiceCall`][service-call] interface.

[service-call]: https://ibm.github.io/java-sdk-core/docs/9.1.0/com/ibm/cloud/sdk/core/http/ServiceCall.html

To call a method synchronously, use the `execute()` method of the `ServiceCall<T>` interface,
like this:  

```java
// Invoke the operation.
GetResourceOptions options = /* construct options model */
Response<Resource> response = myservice.getResource(options).execute();

// Continue execution...
```

To call a method asynchronously, use the `enqueue()` method of the `ServiceCall<T>` interface
to receive a callback when the response arrives.
The `ServiceCallback<T>` interface provides `onResponse` and `onFailure` methods that you
override to handle the callback, like this:

```java
// Invoke the operation in the background
GetResourceOptions options = /* construct options model */
myservice.getResource(options).enqueue(new ServiceCallback<ResourceInstance>() {
  @Override
  public void onResponse(Response<Resource> response) {
    System.out.println("We did it! " + response);
  }

  @Override
  public void onFailure(Exception e) {
    System.out.println("Whoops...");
  }
});

// Continue execution in the meantime!
```

</details>
<details><summary>Node.js</summary>
The Node.js SDK executes each request asynchronously and returns the response as a `Promise`. The Node SDK also supports `async/await`, which processes the `Promise` without needing to formally chain the functions, to provide synchronous-like behavior.

```node
// Without async/await
const resourceMetadata = () => {
  return getResource('my-resource')
    .then(res => getResourceMetadata(res))
}

// With async/await
const resourceMetadata = async () => {
  const res = await getResource('/users.json') 
  const data = await getResourceMetadata(res) 
  return data
}
```
</details>
<details><summary>Python</summary>
The Python SDK supports only synchronous execution of service methods.
</details>

## Questions

If you are having difficulties using an SDK or have a question about the IBM Cloud Services,
please ask a question at [Stack Overflow](http://stackoverflow.com/questions/ask?tags=ibm-cloud).

## Open source @ IBM
Find more open source projects on the [IBM Github Page](http://ibm.github.io/)

## License

This project is released under the Apache 2.0 license.
The license's full text can be found in
[LICENSE](https://github.com/IBM/ibm-cloud-sdk-common/blob/main/LICENSE).
