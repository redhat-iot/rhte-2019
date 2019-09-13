# Stock ticker

* Single producer for each stock
* Multiple consumers, directly from Web browser
* No buffering, no persistence
* Update every 5 seconds

## Deployment

Unfortunately you cannot simply deploy the whole application in one big step. The next sections
describe how to deploy this tutorial.

### Messaging resources

First you need to deploy the messaging resources. The messaging infrastructure already
is deployed on the cluster, and shared with others.

Deploy the messaging resource by executing:

    oc apply -f 010-messaging

This will create:

* An address space
* A single address in this address space
* Two users (one for reading, one for writing) to the address in this address space

**Note**: As the address space is a pre-requisite for the address and the users, it may happen
that the requests are executed "too fast" and so the address and/or the user creation may fail.
In this case, simply wait a bit and re-try `oc apply`.

### Publisher

The publisher is a small process which "generated" a stock price, and publishes it. It is a
program written in Go, and available on GitHub.

The deployment will use source-to-image (S2I) to compile and run the application.

Before deploying, you must extract the endpoint to your address space, and replace the
environment variable `URI` in the file `020-publisher/030-DeploymentConfig.yaml`.

You can extract the information by taking a look at the address space status:

    oc get addressspace ticker -o jsonpath='{ .status.endpointStatuses[?(@.name=="messaging")].serviceHost }'

Use the value, and replace the string `<<"messaging" service endpoint>>` with it.

The deploy the publisher by executing:

    oc apply -f 020-publisher

### Consumer

The consumer is a NodeJS application, which we will again deploy using S2I. Also must the
address space endpoint information be replaced before deploying.

For this you need to extract the external endpoint information, this time of the Web Socket endpoint:

    oc get addressspace ticker -o jsonpath='{ .status.endpointStatuses[?(@.name=="messaging-wss")].externalHost }'

Use the value, and replace the string `<<"messaging-wss" external endpoint>>` in the file `030-consumer/030-DeploymentConfig.yaml` with it.

### Testing

Either by executing:

    oc describe route consumer

Or by using the OpenShift Web console, you can get the final URL to the consumer application.
You can open a web browser to this URL, and should see the stock price page, with a single stock
ticker, and an updating price.

## Things to try

* Change the stock symbol of existing one
* Add a new stock symbol
  ** Add new messaging resources
  ** Add a new publisher
  ** Add the stock symbol to the existing consumer
* …
