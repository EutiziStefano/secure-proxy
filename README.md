# Nginx-secureproxy for Openshift

Pod Nginx for a secure access to microservices, authentication, acl, mutual authentication MTLS, throttling etc...

In this example nginx secure proxy is deployed as a pod in the namespace and can protect multiple microservices in in and out traffic, but this solution can also be deployed in a sidecar proxy approach.


## Preparation

In the yaml template there are two secrets that you must modify inserting your values when needed:
1. client.crt.Secret contains the clients public keys if you would enable mutual authentication (MTLS)
2. certificati.ssl.Secret contains 2 values, the public and the private key that the server will use for ssl, when Route is in passthrough

In the configmap section you'll find the nginx configuration, with servers and locations, in this repo there are some example configuration for mtls and throttling. 

Route is configured by parameters contained in configuration file, there is an example in this repo

As you can see there are three parameters to configure: 
* description: External route, fqdn, for the nginx
  name: ROTTA
  required: true
* description: Port of the service for that route
  name: PORTASERVICE
  required: true
* description: SSL termination for the route
  name: TLS_TERMINATION
  required: true

The service is configured with both SSL and plaintext port to the nginx, choose on your needs.

## INSTALLATION
Launch the command for template processing and create objects:

oc process -f template.yaml --param-file=configuration.properties | oc create -f -

## Mutual Authentication (mtls)

This Nginx can be used for mtls as a server or as a client. 
In the server mode nginx protect internal microservices that are called from other systems (ingress protection).
In the client mode nginx inject a client certificate when proxy a microservice call to another system that require mutual authentication.

In this way for both cases the application will remain unaware of mtls and developers don't should take care of it.

In the follow image you have the functioning schema for these scenarios:



![classic](/readme/mutual.jpg)


## Throttling example with multiple clients
Think at a microservice api that is called by different external systems.

Normally the schema would be as follow:



![classic](/readme/classic.jpg)



In this scenario we can't identify in any way the caller system, and so we can't apply any specific rule to a specific client, collect statistics of utilization, and so on.

Introducing the secureproxy, with an ad hoc nginx configuration whe can create a virtualhost for each client and this allow us to identify them and use different acls for each one, make statistics and throttling on client base.
This is the functioning schema:


![secure](/readme/secure.jpg)



We will create a specific Route object for each client, all the routes are directed on the nginx service.
The service routes the incoming connection to the nginx pods that are active, nginx with SNI capability identifies the correct virtualhost and applies the configurations that we have disposed.

The nginx then proxies the traffic to the application service, and in turn to the application Pods.

Nginx logs can give us information and statistics of the different clients.

