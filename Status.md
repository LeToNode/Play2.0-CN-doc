# Play 2.0 current status

Play 2.0 beta is a preview of the next major version of Play framework, which integrates a brand new build system and awesome asynchronous architecture all with native Java and Scala support.

You can already try to write your first Play 2.0 applications with this beta version. It will give you an almost complete preview of the Play 2.0 experience, including the native Scala support, the new possibilities provided by the sbt integration, and the new Controller/Action API.

Play 2.0 beta, however, is not ready to run production applications. 

## What is missing?

We have worked hard to achieve this beta version and we are half way to the final 2.0 version. Here are listed the most important items we will be working on now:

### Support for more content types out of the box

Currently we have only support for parsing classical URL form encoded HTTP requests. We will add out of the box support for major content types like JSON, XML and File uploads.

### Asynchronous support

Play 2.0 is built from the ground up with reactiveness and optimised resource consumption in mind. We will add public API for these features to make asynchronous reactive responses, streaming and WebSocket programming really simple.

### Testing

We have basic Specs/Junit integration for now. We will add better API for testing your web applications, either from unit tests or integration tests based on Selenium Web driver.

### External HTTP libraries

As in Play 1.x, Play 2.0 will provide out of the box integration with a powerful Web Service client, and OpendID, OAuth support.

### War support

It is also planned to add War deployment support via Servlet 3.0.

### And of course, all that make a great release

Including more validators, binders, better documentation, tutorials and awesome sample applications. 