---

copyright:
  years: 2018, 2019
lastupdated: "2019-01-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Setting up End-to-End tracing
{: #e2e-tracing}

The following tutorial focuses on Zipkin and the use of the [appmetrics-zipkin](https://github.com/RuntimeTools/appmetrics-zipkin) module for tracing Node.js applications. You can learn more about Zipkin in the original [appmetrics-zipkin announcement](https://developer.ibm.com/node/2017/10/26/add-zipkin-open-tracing-support-node-js-application-one-line-code/). 

In the following steps, two small applications (one front end, one back end) are used to trace between two endpoints by using the `appmetrics-zipkin` module. You can start from scratch or apply the principles that are described here to your existing Node.js applications. 

## Step 1. Installing and enabling the appmetrics-zipkin module
{: #install-zipkin}

In the same location as your Node.js application’s `package.json` file, enter the following [npm](https://nodejs.org/) command to add the `appmetrics-zipkin` module into your dependency list:
```
npm install --save appmetrics-zipkin
```
{: codeblock}

Add the following line to your Node.js server code, **BEFORE** any other appmetrics `require` statements:
```js
var appzip = require('appmetrics-zipkin');
```
{: codeblock}

The following statement causes tracing to be added to your `HTTP` and `request` method calls, and the data to be sent to the Zipkin server. By default, the module looks for the Zipkin server at `localhost` and port `9411`. You can change the host name and port by using the following syntax:
```js
var appzip = require('appmetrics-zipkin')({
 host: "my.host.here",
 port: 12345, // changeme
 serviceName:'my-service-name'
});
```
{: codeblock}

Send a request as you normally would. For example:
```
http.request(options, callback).end();
```
{: codeblock} 

## Step 2. Setting up a Zipkin server
{: #setup-zipkin-server}

You now need a place to send your data to, specifically the traces, which are made up of spans. Before you deploy to any cloud, you can test the e2e tracing configuration by setting up a Zipkin server locally or in a container. 

### Setting up Zipkin locally

Zipkin is provided in a single `jar` file so you can download and run it using the following commands on the system where you want Zipkin to be available:

1. Downloading Zipkin:
  ```
  wget zipkin.jar 'https://search.maven.org/remote_content?g=io.zipkin.java&a=zipkin-server&v=1.31.3&c=exec'
  ```
  {: codeblock}

2. Starting Zipkin:
  ```
  java -jar zipkin.jar
  ```
  {: codeblock}

  The `wget` command downloads the Zipkin file, and the `java -jar` command starts the Zipkin server. You can download Zipkin from other locations too, but it’s important that you use version 1.x for this tutorial so the trace format matches what the Zipkin server is expecting.

  If the output from this command is too verbose or you’d like to run Zipkin in the background, you can add `-q -O` for the `wget` command and `/dev/null 2>&1 &` for Zipkin. At this stage, you're downloading the Zipkin `.jar` file, and running the main method to start the Zipkin server.

### Setting up Zipkin in a Docker container

You can optionally run a Zipkin server in a Docker container by running the following command:
```
docker run -d -p 9411:9411 openzipkin/zipkin
```
{: codeblock}

The `openzipkin/zipkin` module is downloaded, installed, and started on port `9411` by using one simple command.

### Accessing the Zipkin console
The following image shows the Zipkin server that runs on `localhost` on port `9411`:

![ZipkinNoData](images/ZipkinNoData.png)

You can click **Find traces** and modify the search options to selectively show only traces within a certain time period. You can also filter to show traces that involve specific service names. The service names are specified when you instrument your code, and in the example scenario we use “getter” and “pusher”.

## Step 3. Testing an example scenario
{: #example-scenario}

If you follow the [GitHub project’s documentation](https://github.com/ibm-developer/nodejs-zipkin-tracing), you end up with the following sample application. It’s a simple process that involves tracing a request and response between two endpoints. The following images show the Zipkin server with collected trace data on display. The key point to remember is the inclusion of `require('appmetrics-zipkin')`, and optionally the Zipkin server configuration code. The following example scenario shows how you can quickly add Zipkin tracing into your existing Node.js applications.

### Tracing scenario overview:
* A **front end**, which is known as the pusher, prompts the user for the length of a string to create and convert to lowercase. The bigger the number, the bigger the string, and the longer it takes to handle the request. Available on port `3000`.
* A **back-end**, known as the getter, handles the request and is available on port `3001`.
* A **Zipkin server** runs locally or on Kubernetes where you see your trace data.

### Front-end app (pusher)
The front-end app (pusher) service sends the request (our simple front end):
![frontend_app](images/frontend_app.png)

### Back-end app (getter)
The back-end app (getter) receives the request, which is listening on a different port:
![backend_app](images/Backend.png)

### Sending a request from the pusher to the getter
Send a request from the pusher to the getter:
![500please](images/500Please.png)

### Viewing traces with the Zipkin web UI
The trace data sent to Zipkin can be viewed with the Zipkin web UI at `localhost:9411`. You can see that the **getter** receives user input (the user wants to send a 500 character long message to the getter, by using the pusher service):
![Getter500msg](images/Getter500Msg.png)

The user request details are shown. Notice the “500” which is the parameter that is provided for the user’s request. They wanted to generate a string of 500 characters. You can see exactly what the user requested and how long it took to handle this request. The contents of the request (payload), returned from the server, isn't visible. 

We're concerned with response times, and the parameters so we can determine what users are requesting when they’re experiencing slow response times: 
![GetterGet](images/GetterGet.png)

### Identifying the slow request
Here’s what a slow request would look like. The following user is requesting to convert 5,000,000 characters from uppercase to lowercase (as you do). It’s something that obviously takes longer:
![SlowRequest](images/SlowRequest.png)

Clicking this span leads to the following output. Again, you can see the expensive request that consumed much more time. A more realistic scenario would involve potentially many Node.js microservices receiving all manner of requests at various endpoints continuously. By having a high-level view of your endpoints, you can quickly determine which services are responding slowly, and exactly what users are requesting: 
![TookaWhile](images/TookAWhile.png)

With this example, you now have the following scenario:

* The pusher sends a message to the getter (one span).
* The getter sends back a response (one span).
* The full trace, consisting of the two spans, is visible on the Zipkin server that is deployed locally.

As your applications become more complex, and your services become more popular, the need to have such tracing in place becomes obvious. Tracing at a high level provides value to developers so that problems can be identified and triaged quickly and effectively. Plenty of alternatives are available, but our approach is to make it as simple as possible, and act entirely in the open.

The tutorial finishes here for deployments without Kubernetes. Check out the next section if you would like to pursue tracing Node.js apps that run on Kubernetes.

## Next steps
{: #next-steps}

* Learn to build Cloud Native Node.js applications with the help of the [CloudNativeJS](https://www.cloudnativejs.io/) community project that provides assets and tools to help you deploy them to Docker and Kubernetes based clouds.

* If you're ready to add tracing to your Node.js applications that run on Kubernetes, check out [tracing Node.js applications that use Kubernetes](https://developer.ibm.com/node/tutorial-end-end-tracing-node-js-applications/#appservice).

