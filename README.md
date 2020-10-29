# IBM Cloud SDK Common
This project contains documentation and other resources related to IBM Cloud SDKs

<details><summary>Table of Contents</summary>

<!--
  The TOC below is generated using the `markdown-toc` node package.

      https://github.com/jonschlinkert/markdown-toc

  You should regenerate the TOC after making changes to this file.

      npx markdown-toc -i README.md
  -->

<!-- toc -->

- [Overview](#overview)
- [SDK Project Catalog](#sdk-project-catalog)
- [Using the SDK](#using-the-sdk)
  * [Constructing service clients](#constructing-service-clients)
    + [Setting client options programmatically](#setting-client-options-programmatically)
    + [Using external configuration](#using-external-configuration)
  * [Authentication](#authentication)
    + [1. Create an IAM authenticator programmatically](#1-create-an-iam-authenticator-programmatically)
    + [2. Create an IAM authenticator from configuration](#2-create-an-iam-authenticator-from-configuration)
    + [3. Create a Bearer Token authenticator programmatically](#3-create-a-bearer-token-authenticator-programmatically)
    + [Additional Information](#additional-information)
  * [Passing parameters to operations](#passing-parameters-to-operations)
  * [Receiving operation responses](#receiving-operation-responses)
  * [Sending HTTP headers](#sending-http-headers)
    + [Sending HTTP headers with all requests](#sending-http-headers-with-all-requests)
    + [Sending request HTTP headers](#sending-request-http-headers)
  * [Transaction IDs](#transaction-ids)
  * [Configuring the HTTP Client](#configuring-the-http-client)
  * [Disabling SSL Verification - Discouraged](#disabling-ssl-verification---discouraged)
    + [With external configuration](#with-external-configuration)
    + [Programmatically](#programmatically)
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

IBM Cloud SDKs allow developers to programmatically interact with various IBM Cloud Services.
SDKs are provided for the following languages:
- Go
- Java
- Node.js
- Python

## SDK Project Catalog
The following table provides links to the various SDK projects associated with IBM Cloud Service
categories:

Service Category | Go | Java | Node.js | Python
--- | --- | --- | --- | ---
Platform Services  | [Go SDK](https://github.com/IBM/platform-services-go-sdk) | [Java SDK](https://github.com/IBM/platform-services-java-sdk) | [Node.js SDK](https://github.com/IBM/platform-services-node-sdk) | [Python SDK](https://github.com/IBM/platform-services-python-sdk)
Watson | [Go SDK](https://github.com/watson-developer-cloud/go-sdk) | [Java SDK](https://github.com/watson-developer-cloud/java-sdk) | [Node.js SDK](https://github.com/watson-developer-cloud/node-sdk) | [Python SDK](https://github.com/watson-developer-cloud/python-sdk)
Watson Health Cognitive Services | [Go SDK](https://github.com/IBM/whcs-go-sdk) | [Java SDK](https://github.com/IBM/whcs-java-sdk) | [Node.js SDK](https://github.com/IBM/whcs-node-sdk) | [Python SDK](https://github.com/IBM/whcs-python-sdk)
DB2 On Cloud v4 | [Go SDK](https://github.com/ibmdb/go_ibm_db) | [Java SDK](https://github.com/ibmdb/java-db2) | [Node.js SDK](https://github.com/ibmdb/node-ibm_db) | [Python SDK](https://github.com/ibmdb/python-ibmdb)
Cloud Pak For Data | [Go SDK](https://github.com/ibm/cloudpakfordata-go-sdk) | [Java SDK](https://github.com/IBM/cloudpakfordata-java-sdk) | [Node.js SDK](https://github.com/IBM/cloudpakfordata-node-sdk) | [Python SDK](https://github.com/IBM/cloudpakfordata-python-sdk)
API Gateway | [Go SDK](https://github.com/IBM/apigateway-go-sdk) | | |
App ID | | | [Node.js SDK](https://github.com/ibm-cloud-security/appid-serversdk-nodejs) |
Cloudant | [Go SDK](https://github.com/IBM/cloudant-go-sdk) | [Java SDK](https://github.com/IBM/cloudant-java-sdk) | [Node.js SDK](https://github.com/IBM/cloudant-node-sdk) | [Python SDK](https://github.com/IBM/cloudant-python-sdk)
Code Engine | [Go SDK](https://github.com/IBM/code-engine-go-sdk) | [Java SDK](https://github.com/IBM/code-engine-java-sdk) | [Node.js SDK](https://github.com/IBM/code-engine-node-sdk) | [Python SDK](https://github.com/IBM/code-engine-python-sdk)
DNS Services | [Go SDK](https://github.com/IBM/dns-svcs-go-sdk) | | |
Security Advisor | [Go SDK](https://github.com/ibm-cloud-security/security-advisor-sdk-go) | [Java SDK](https://github.com/ibm-cloud-security/security-advisor-sdk-java) | [Node.js SDK](https://github.com/ibm-cloud-security/security-advisor-sdk-node) | [Python SDK](https://github.com/ibm-cloud-security/security-advisor-sdk-python)
Networking Services | [Go SDK](https://github.com/IBM/networking-go-sdk) | [Java SDK](https://github.com/IBM/networking-java-sdk) | [Node.js SDK](https://github.com/IBM/networking-node-sdk) | [Python SDK](https://github.com/IBM/networking-python-sdk)
Analytics Engine | [Go SDK](https://github.com/IBM/ibm-iae-go-sdk) | [Java SDK](https://github.com/IBM/ibm-iae-java-sdk) | [Node.js SDK](https://github.com/IBM/ibm-iae-node-sdk) | [Python SDK](https://github.com/IBM/ibm-iae-python-sdk)
Key Protect | [Go SDK](https://github.com/IBM/keyprotect-go-client) | [Java SDK](https://github.com/IBM/keyprotect-java-client) | | [Python SDK](https://github.com/IBM/keyprotect-python-client)
VPC | [Go SDK](https://github.com/IBM/vpc-go-sdk) | [Java SDK](https://github.com/IBM/vpc-java-sdk) | | [Python SDK](https://github.com/IBM/vpc-python-sdk)
Cloud Object Storage Configuration | [Go SDK](https://github.com/IBM/ibm-cos-sdk-go-config) | [Java SDK](https://github.com/IBM/ibm-cos-sdk-java-config) | [Node.js SDK](https://github.com/IBM/ibm-cos-sdk-js-config) | [Python SDK](https://github.com/IBM/ibm-cos-sdk-python-config)


## Using the SDK
This section provides general information on how to use the services contained in an SDK project.
The programming examples below will illustrate how the various activities can be performed for
each of the programming languages mentioned above using a mythical service named "Example Service"
that is defined in [example-service.yaml](example-service.yaml).

### Constructing service clients
Each service client - the client-side view of a service - is implemented in its own
class (Java, Node.js, Python) or struct (Go) within the corresponding SDK project.
For example, the "Example Service" client is implemented as:
- Go: the `ExampleServiceV1` struct within the `exampleservicev1` package
- Java: the `ExampleService` class within the `com.ibm.cloud.mysdk.example_service.v1` package
- Node.js: the `ExampleServiceV1` class in the `example-service/v1` module
- Python: the `ExampleServiceV1` class within the `mysdk` module

The SDK allows you to construct the service client in one of two ways:
1. Setting client options programmatically
2. Using external configuration properties

#### Setting client options programmatically
Here's an example of how to construct an instance of the "Example Service" client
while specifying various client options (authenticator, service endpoint URL, etc.) programmatically:

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v4/core"
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
<details><summary>Java</summary>

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

```
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

```
# Contents of "mycredentials.env"
EXAMPLE_SERVICE_URL=https://example-service.cloud.ibm.com/v1
EXAMPLE_SERVICE_AUTH_TYPE=iam
EXAMPLE_SERVICE_APIKEY=iam-api-key

```

You would then provide the name of the credentials file via the `IBM_CREDENTIALS_FILE` environment variable:

```
export IBM_CREDENTIALS_FILE=/myfolder/mycredentials.env
```

When the SDK needs to look for configuration properties, it will detect the `IBM_CREDENTIALS_FILE` environment
variable, then load the properties from the specified file.

##### Complete configuration-loading process
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
IBM Cloud Services use token-based Identity and Access Management (IAM) authentication.

With IAM authentication, you supply an API key to obtain an access token, and this access token is
then included with each API request to provide the required authentication information.
Access tokens are valid only for a limited amount of time and must be refreshed or reacquired.

To provide the proper authentication information to the SDK, you can use one of the following scenarios:

#### 1. Create an IAM authenticator programmatically
In this scenario, you construct an IAM authenticator instance, supplying your IAM api key programmatically.

The SDK's IAM authenticator will:  
- Use your API key to obtain an access token from the IAM token service
- Ensure that the access token is valid
- Include the access token in each outgoing request
- Refresh or reacquire the access token when it expires

##### Examples

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v4/core"
    "github.com/IBM/mysdk/exampleservicev1"
}

authenticator := &core.IamAuthenticator{
    ApiKey: "<iam-api-key>",
}

options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,
}

myService, err := exampleservicev1.NewExampleServiceV1(options)
if err != nil {
    panic(err)
}
```

</details>
<details><summary>Java</summary>

```java
import com.ibm.cloud.sdk.core.security.Authenticator;
import com.ibm.cloud.sdk.core.security.IamAuthenticator;
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;

Authenticator authenticator = new IamAuthenticator("<iam-api-key>");
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);
```

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');
const { IamAuthenticator } = require('mysdk/auth');

const authenticator = new IamAuthenticator({
  apikey: '<iam-api-key>',
});

const myService = new ExampleServiceV1({
  authenticator,
});
```

</details>
<details><summary>Python</summary>

```python
from mysdk import ExampleServiceV1
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

authenticator = IAMAuthenticator('<iam-api-key>')
my_service = ExampleServiceV1(authenticator=authenticator)
```

</details>

#### 2. Create an IAM authenticator from configuration
In this scenario, you define the IAM api key in external configuration properties, and then use the
SDK's authenticator factory to construct an IAM authenticator using the configured IAM api key.
This allows you to avoid hard-coding your api key within
your application code or having to concern yourself with loading it from configuration properties.

The SDK's authenticator factory will:
- Retrieve your external configuration to obtain your IAM api key
- Construct an IAM authenticator using the configured IAM api key
- The IAM authenticator will then behave exactly the same as the authenticator constructed in Scenario 1 above.

##### Examples
The programming examples in this section assume that external configuration properties like the following have been
configured:

```
export EXAMPLE_SERVICE_AUTH_TYPE=iam
export EXAMPLE_SERVICE_APIKEY=<iam-api-key>
```

<details><summary>Go</summary>

```go
import {
    "github.com/IBM/go-sdk-core/v4/core"
    "github.com/IBM/mysdk/exampleservicev1"
}

authenticator, err := core.GetAuthenticatorFromEnvironment(exampleservicev1.DefaultServiceName)
if err != nil {
   panic(err)
}

options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,
}

myService, err := exampleservicev1.NewExampleServiceV1(options)
if err != nil {
    panic(err)
}
```

</details>
<details><summary>Java</summary>

```java
import com.ibm.cloud.sdk.core.security.Authenticator;
import com.ibm.cloud.sdk.core.security.ConfigBasedAuthenticatorFactory;
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;

Authenticator authenticator = ConfigBasedAuthenticatorFactory.getAuthenticator(ExampleService.DEFAULT_SERVICE_NAME);
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);
```

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');
const { IamAuthenticator, getAuthenticatorFromEnvironment } = require('mysdk/auth');

const authenticator = getAuthenticatorFromEnvironment(ExampleServiceV1.DEFAULT_SERVICE_NAME);

const myService = new ExampleServiceV1({
  authenticator,
});
```

</details>
<details><summary>Python</summary>

```python
from mysdk import ExampleServiceV1
from ibm_cloud_sdk_core import get_authenticator_from_environment

authenticator = get_authenticator_from_environment(ExampleServiceV1.DEFAULT_SERVICE_NAME)
my_service = ExampleServiceV1(authenticator=authenticator)
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
    "github.com/IBM/go-sdk-core/v4/core"
    "github.com/IBM/mysdk/exampleservicev1"
}

authenticator := &core.BearerTokenAuthenticator{
    BearerToken: "<access-token>",
}

// Create the service options struct.
options := &exampleservicev1.ExampleServiceV1Options{
    Authenticator: authenticator,
}

// Construct the service instance.
myservice, err := exampleservicev1.NewExampleServiceV1(options)

...

// Later when the access token expires, the application must acquire
// a new access token, then set it on the authenticator.
// Subsequent request invocations will include the new access token.
authenticator.BearerToken = "<new-access-token>"
```
</details>
<details><summary>Java</summary>

```java
import com.ibm.cloud.sdk.core.security.BearerTokenAuthenticator;
import com.ibm.cloud.mysdk.example_service.v1.ExampleService;

Authenticator authenticator = new BearerTokenAuthenticator("<access-token>");
ExampleService service = new ExampleService(ExampleService.DEFAULT_SERVICE_NAME, authenticator);

...

// Later when the access token expires, the application must acquire
// a new access token, then set it on the authenticator.
// Subsequent request invocations will include the new access token.
authenticator.setBearerToken("<new-access-token>")
```

</details>
<details><summary>Node.js</summary>

```js
const ExampleServiceV1 = require('mysdk/example-service/v1');
const { BearerTokenAuthenticator } = require('mysdk/auth');

const authenticator = new BearerTokenAuthenticator({
  bearerToken: '<access-token>',
});

const myService = new ExampleServiceV1({
  authenticator,
});
...

// Later when the access token expires, the application must acquire
// a new access token, then set it on the authenticator.
// Subsequent request invocations will include the new access token.
authenticator.setBearerToken('<new-access-token>')
```

</details>
<details><summary>Python</summary>

```python
from mysdk import ExampleServiceV1
from ibm_cloud_sdk_core.authenticators import BearerTokenAuthenticator

authenticator = BearerTokenAuthenticator('<access-token>')
my_service = ExampleServiceV1(authenticator=authenticator)

...

// Later when the access token expires, the application must acquire
// a new access token, then set it on the authenticator.
// Subsequent request invocations will include the new access token.
authenticator.set_bearer_token('<new-access-token>')
```

</details>

#### Additional Information
For more details about authentication, including the full set of authentication schemes supported by
the SDK Core library for your language, see these links:
- [Go](https://github.com/IBM/go-sdk-core/blob/master/Authentication.md)
- [Java](https://github.com/IBM/java-sdk-core/blob/master/Authentication.md)
- [Node.js](https://github.com/IBM/node-sdk-core/blob/master/AUTHENTICATION.md)
- [Python](https://github.com/IBM/python-sdk-core/blob/master/Authentication.md)

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
```
{
    'result': <response object returned by operation>,
    'headers': { <http response headers> },
    'status_code': <http status code>
}
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
myService.Service.SetDefaultHeaders(customHeaders)

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
}

// Construct a new http.Client instance with a 1-minute request timeout.
myHTTPClient := &http.Client{
    Timeout: time.Minute,
}

// Set the service's Client field directly.
myService.Service.Client = myHTTPClient

// Alternatively, you can set the service's Client field with the SetHTTPClient() method.
myService.Service.SetHTTPClient(myHttpClient)

// After using ONE of the methods above, this request will use a 1-minute timeout.
result, detailedResponse, err := myService.GetResource(options)
```

You can also directly modify the `http.Client` instance being used by the service client,
like this:

```go
import {
    "net/http"
    "time"
}

// Modify the service's http.Client to set a 1-minute timeout.
myService.Service.Client.Timeout = 1 * time.Minute

// This request will use a 1-minute timeout.
result, detailedResponse, err := myService.GetResource(options)
```

Note that the default `Timeout` associated with the `http.Client` struct is `0` (no timeout),
so it may be important for you to set the `Timeout` field for your application.

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
(e.g. `IamAuthenticator`).
Described below are scenarios where this capability is needed.
Note that this functionality is applicable only for Node.js environments - these configurations will have
no effect in the browser.

##### Proxy
To invoke requests behind an HTTP proxy (e.g. firewall), a special tunneling agent
must be used.
Use the package [`tunnel`](https://github.com/koichik/node-tunnel/) for this.
Configure this agent with your proxy information, and pass it in as the HTTPS agent in the
service constructor.
Additionally, you must set `proxy` to `false` in the client constructor.
Here's an example:

```js
const tunnel = require('tunnel');
import ExampleServiceV1 from 'ibm-mysdk/example-service/v1';
import { IamAuthenticator } from 'mysdk/auth';

const myService = new ExampleServiceV1({
  authenticator: new IamAuthenticator({ apikey: '{apikey}' }),
  httpsAgent: tunnel.httpsOverHttp({
    proxy: {
      host: 'some.host.org',
      port: 1234,
    },
  }),
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
const tunnel = require('tunnel');
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

For more information regarding http client configuration,
please see [this link](https://requests.readthedocs.io/en/master/)

</details>


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

#### programmatically
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

</details>
<details><summary>Java</summary>

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
- `info`: a dictionary of additional information about the error

Here's an example:

```python
try:
  response = my_service.get_resource(resource_id='does not exist')
except ApiException as e:
  print("Method failed with status code " + str(e.code) + ": " + e.message)
  print("Detailed error information: " + e.info)
```
</details>


### Logging
This section describes how to enable logging within the SDK.

<!--
<details><summary>Go</summary>
TBD
</details>
-->
<details><summary>Java</summary>
For Java, you can configure the logging detail level by using the `HttpConfigOptions` class.

Here's an example:

```java

HttpConfigOptions options =
    new HttpConfigOptions.Builder()
        .loggingLevel(HttpConfigOptions.LoggingLevel.BASIC)
	.build();

myService.configureClient(options);
```
</details>
<details><summary>Node.js</summary>

For Node.js, the SDK uses the [`debug`](https://github.com/visionmedia/debug) package for logging.
Specify the desired environment variable to enable logging debug messages.

</details>
<details><summary>Python</summary>

For Python, you can enable debug logging by importing the `logging` package and then setting
the log level to DEBUG as in this example:

```
import logging
logging.basicConfig(level=logging.DEBUG)

```

This will cause messages like the following to be logged as your application invokes
various operations:

```
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): iam.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://iam.cloud.ibm.com:443 "POST /identity/token HTTP/1.1" 200 1809
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): myservice.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://myservice.cloud.ibm.com:443 "POST /myservice/api/v1/resource HTTP/1.1" 201 None
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): myservice.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://myservice.cloud.ibm.com:443 "GET /myservice/api/v1/resource/resource-id-1 HTTP/1.1" 200 None
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): myservice.cloud.ibm.com:443
DEBUG:urllib3.connectionpool:https://myservice.cloud.ibm.com:443 "DELETE /myservice/api/v1/resource/resource-id-1 HTTP/1.1" 204 None
```

To enable detailed logging of request and response messages, you can import the
`http.client` package, and then enable debug logging within HTTP connections like this:

```
from http.client import HTTPConnection
HTTPConnection.debuglevel = 1
```

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
The Node.js SDK executes each request asynchronously and returns the response as a Promise.
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
[LICENSE](https://github.ibm.com/CloudEngineering/node-sdk-template/blob/master/LICENSE).
