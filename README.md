<p align="center">
    <img src="https://user-images.githubusercontent.com/9938337/31054113-7cac93d8-a6a3-11e7-84ae-e98c57129a72.png" alt="Leaf Error Middleware">
    <br>
    <br>
    <a href="https://swift.org">
        <img src="http://img.shields.io/badge/Swift-4.1-brightgreen.svg" alt="Language">
    </a>
    <a href="https://travis-ci.org/brokenhandsio/leaf-error-middleware">
        <img src="https://travis-ci.org/brokenhandsio/leaf-error-middleware.svg?branch=master" alt="Build Status">
    </a>
    <a href="https://codecov.io/gh/brokenhandsio/leaf-error-middleware">
        <img src="https://codecov.io/gh/brokenhandsio/leaf-error-middleware/branch/master/graph/badge.svg" alt="Code Coverage">
    </a>
    <a href="https://raw.githubusercontent.com/brokenhandsio/leaf-error-middleware/master/LICENSE">
        <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License">
    </a>
</p>

Leaf Error Middleware is a piece of middleware for [Vapor](https://github.com/vapor/vapor) which allows you to return custom 404 and server error pages.

Note that this middleware is designed to be used for Leaf front-end websites only - it should not be used for providing JSON error responses for an API, for example.

# Usage

First, add LeafErrorMiddleware as a dependency in your `Package.swift` file:

```swift
dependencies: [
    // ...,
    .package(url: "https://github.com/brokenhandsio/leaf-error-middleware.git", from: "1.0.0")
],
targets: [
    .target(name: "App", dependencies: ["Vapor", ..., "LeafErrorMiddleware"]),
    // ...
]
```

To use the LeafErrorMiddleware, register the middleware service in `configure.swift` (make sure you `import LeafErrorMiddleware` at the top):

```swift
// You must set the preferred renderer:
config.prefer(LeafRenderer.self, for: ViewRenderer.self)

services.register { worker in
    return try LeafErrorMiddleware(environment: worker.environment)
}
```

Then add it to your `MiddlewareConfig`:

```swift
var middlewares = MiddlewareConfig()
middlewares.use(LeafErrorMiddleware.self)
// ...
services.register(middlewares)
```

This replaces the default error middleware in Vapor, so ***do not*** add the default `ErrorMiddleware` to your `MiddlewareConfig`.

# Setting Up

You need to include two [Leaf](https://github.com/vapor/leaf) templates in your application:

* `404.leaf`
* `serverError.leaf`

When Leaf Error Middleware catches a 404 error, it will return the `404.leaf` template. Any other error caught will return the `serverError.leaf` template. The `serverError.leaf` template will be passed two parameters:

* `status` - the status code of the error caught
* `statusMessage` - a reason for the status code
