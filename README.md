# HTTP Interceptors

## Overview

## Objectives

- Describe HTTP Interceptors
- Write an HTTP Interceptor

## What is a HTTP interceptor?

Now, using `$http` or `$resource` means we're going to end up doing a lot of HTTP requests in our applications.

Eventually, we may need to manipulate all of the requests, or even take a wide-look at all the responses and handle any errors globally instead of on an individual case - we can do this with HTTP interceptors.

We might also want to retry requests if they fail. We can do this with HTTP interceptors!

HTTP interceptors allow us to define functionality that will happen before every request is made, or just after every request has come back from the server. This means we can append things to every request we make, or handle errors that may arise from the backend (it's better to do this in an interceptor and have application-wide error handling, rather than doing it for every possible request we make).

HTTP interceptors are just simple services that we then push into an interceptors array in the `$httpProvider` service.

First, let's look at how we'd make the service:

```
function MyInterceptor() {

}

angular
	.module('app')
	.service('MyInterceptor', MyInterceptor);
```

This looks familiar - it is! Now you might be wondering how we actually intercept things.

There are certain functions we can attach to our interceptor service - Angular will look if they exist, and then run them if they do.

### Before a request

Now, to intercept the request before it is actually made, we need to define the function `request` on `this`.

```
function MyInterceptor() {
	this.request = function (config) {
		// this will be fired before each request!
	};
}

angular
	.module('app')
	.service('MyInterceptor', MyInterceptor);
```

We can now manipulate our request by editing the `config` variable passed through. For instance, if we want to add another header to our request, we just modify `config.headers`.

```
function MyInterceptor() {
	this.request = function (config) {
		config.headers['X-Requested-From'] = 'Angular';
		return config;
	};
}

angular
	.module('app')
	.service('MyInterceptor', MyInterceptor);
```

This will append the `X-Requested-From` header to every single request going through `$http`!

### After a request

We can also intercept after a request has been completed (but before we return the results to our controllers/services) by attaching the function `response`.

We might want to log what time the request came back:

```
function MyInterceptor() {
	this.response = function (config) {
		config.config.responseTime = Date.now();
		return config;
	};
}

angular
	.module('app')
	.service('MyInterceptor', MyInterceptor);
```

We can then check when the request was completed in our services!

### After an error

We can just purely kick in our interceptor after an error too. We could have some service to display notifications to the user, and we can invoke this service in our interceptor if there was an error. We can do this by the `responseError` function.

```
function MyInterceptor(NotificationService) {
	this.responseError = function (config) {
		NotificationService
			.showError(config);
	};
}

angular
	.module('app')
	.service('MyInterceptor', MyInterceptor);
```

(NotificationService will be a custom service that we have made, alerting the user when we call `.showError`)

It's that simple, whilst also being incredibly awesome + powerful!
