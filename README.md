# bunq C# SDK
Version 0.9.0 **BETA**

## Introduction
Hi developers!

Welcome to the bunq C# SDK! 👨‍💻

We're very happy to introduce yet another unique product: complete banking SDKs!
Now you can build even bigger and better apps and integrate them with your bank of the free! 🌈

Before you dive into this brand new SDK, please consider:
- Checking out our new developer’s page [https://bunq.com/en/developer](https://bunq.com/en/developer) 🙌  
- Grabbing your production API key from the bunq app or asking our support for a Sandbox API key 🗝
- Visiting [together.bunq.com](https://together.bunq.com) where you can share your creations,
questions and experience 🎤

Give us your feedback, create pull requests, build your very own bunq apps and most importantly:
have fun! 💪

This SDK is in **beta**. We cannot guarantee constant availability or stability.
Thanks to your feedback we will make improvements on it.

## Installation
 The `sdk_csharp` is hosted on [nuget](https://www.nuget.org/packages/Bunq.Sdk).
 
```Install-Package Bunq.Sdk```

```dotnet add package Bunq.Sdk```

## Usage

### Creating an API context
In order to start making calls with the bunq API, you must first register your API key and device,
and create a session. In the SDKs, we group these actions and call it "creating an API context". The
context can be created by using the following code snippet:

```
var apiContext = ApiContext.Create(ApiEnvironmentType.SANDBOX, API_KEY, DEVICE_DESCRIPTION);
apiContext.Save();
```

**Please note:** initializing your application is a heavy task and it is recommended to do it only once per device.  

After saving the context, you can restore it at any time:

```
var apiContext = ApiContext.Restore(API_CONTEXT_FILE_PATH);
```

**Tip:** both saving and restoring the context can be done without any arguments. In this case the context will be saved
to/restored from the `bunq.conf` file in the same folder with your executable.

#### Example
See [`ApiContextSaveSample.cs`](./BunqSdk.Samples/ApiContextSaveSample.cs)

The API context can then be saved with:

#### Safety considerations
The file storing the context details (i.e. `bunq.conf`) is a key to your account. Anyone having
access to it is able to perform any Public API actions with your account. Therefore, we recommend
choosing a truly safe place to store it.

### Making API calls
There is a class for each endpoint. Each class has functions for each supported action. These
actions can be `Create`, `Get`, `Update`, `Delete` and `List`.

Sometimes API calls have dependencies, for instance `MonetaryAccount`. Making changes to a monetary
account always also needs a reference to a `User`. These dependencies are required as arguments when
performing API calls. Take a look at [doc.bunq.com](https://doc.bunq.com) for the full
documentation.

#### Creating objects
Creating objects through the API requires an `ApiContext`, a `requestMap` and identifiers of all
dependencies (such as User ID required for accessing a Monetary Account). Optionally, custom headers
can be passed to requests.


```
var apiContext = ApiContext.Restore();
var paymentMap = new Dictionary<string, object>
{
    {Payment.FIELD_AMOUNT, new Amount(PAYMENT_AMOUNT, PAYMENT_CURRENCY)},
    {
        Payment.FIELD_COUNTERPARTY_ALIAS,
        new Pointer(COUNTERPARTY_POINTER_TYPE, COUNTERPARTY_EMAIL)
    },
    {Payment.FIELD_DESCRIPTION, PAYMENT_DESCRIPTION}
};

var paymentId = Payment.Create(apiContext, paymentMap, USER_ITEM_ID,
    MONETARY_ACCOUNT_ITEM_ID);
```

##### Example
See [`PaymentSample.cs`](./BunqSdk.Samples/PaymentSample.cs)

#### Reading objects
Reading objects through the API requires an `ApiContext`, identifiers of all dependencies (such as
User ID required for accessing a Monetary Account), and the identifier of the object to read (ID or
UUID) Optionally, custom headers can be passed to requests.

This type of calls always returns a model.

```
var monetaryAccount = MonetaryAccount.Get(apiContext, USER_ITEM_ID, MONETARY_ACCOUNT_ITEM_ID);
```

##### Example
See [`MonetaryAccountSample.cs`](./BunqSdk.Samples/MonetaryAccountSample.cs)

#### Updating objects
Updating objects through the API goes the same way as creating objects, except that also the object to update identifier
(ID or UUID) is needed.

```
var requestUpdateMap = new Dictionary<string, object> {{RequestInquiry.FIELD_STATUS, STATUS_REVOKED}};
var requestUpdated = RequestInquiry.Update(apiContext, requestUpdateMap, USER_ITEM_ID,
    MONETARY_ACCOUNT_ITEM_ID, requestId);
```

##### Example
See [`RequestSample.cs`](./BunqSdk.Samples/RequestSample.cs)

#### Deleting objects
Deleting objects through the API requires an `ApiContext`, identifiers of all dependencies (such as User ID required for
accessing a Monetary Account), and the identifier of the object to delete (ID or UUID) Optionally, custom headers can be
passed to requests.

```
CustomerStatementExport.Delete(apiContext, userId, monetaryAccountId, customerStatementId);
```

##### Example
See [`CustomerStatementExportSample.cs`](./BunqSdk.Samples/CustomerStatementExportSample.cs)

#### Listing objects
Listing objects through the API requires an `ApiContext` and identifiers of all dependencies (such as User ID required
for accessing a Monetary Account). Optionally, custom headers can be passed to requests.

```
var users = User.List(apiContext);
```

##### Example
See [`UserListSample.cs`](./BunqSdk.Samples/UserListSample.cs)

## Running Samples
In order to make the experience of getting into bunq C# SDK smoother, we have bundled it with `BunqSdk.Samples`, a
separate project containing sample use cases of the SDK.

To run a sample, please do the following:
1. In your IDE, open the sample you are interested in and adjust the constants, such as `API_KEY` or `USER_ID`, to
hold your data.

2. In your terminal, go to the directory of `BunqSdk.Samples`:

```shell
$ cd /path/to/bunq/sdk/solution/BunqSdk.Samples/
```

3. In the terminal, run:

```shell
$ dotnet run <SomethingSample.cs>
```
   Replace `<SomethingSample.cs>` with the name of the sample you would like to run.

In order for samples to run, you would need a valid context file (`bunq.conf`) to be present in the `BunqSdk.Samples`
directory. The file can either copied from somewhere else (e.g. tests) or created by running the following command
in your `BunqSdk.Samples` directory:

```shell
$ dotnet run ApiContextSaveSample.cs
```

Please do not forget to set the `API_KEY` constant in `ApiContextSaveSample.cs` to your actual API key before running the
sample!

## Running tests
Information regarding the test cases can be found in the [README.md](./BunqSdk.Tests/README.md)
located in [test](./BunqSdk.Tests).

## Exceptions
The SDK can throw multiple exceptions. For an overview of these exceptions please
take a look at [EXCEPTIONS.md](./BunqSdk/Exception/EXCEPTIONS.md)
 
