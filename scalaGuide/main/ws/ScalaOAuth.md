# OAuth

[[OAuth|http://oauth.net/]] is a simple way to publish and interact with protected data. It's also a safer and more secure way for people to give you access. For example, it can be used to access your users' data on [[Twitter|https://dev.twitter.com/docs/auth/using-oauth]].

There are 2 very different versions of OAuth: [[OAuth 1.0|http://tools.ietf.org/html/rfc5849]] and [[OAuth 2.0|http://oauth.net/2/]]. Version 2 is simple enough to be implemented easily without library or helpers, so Play only provides support for OAuth 1.0.

## Required Information

OAuth requires you to register your application to the service provider. Make sure to check the callback URL that you provide, because the service provider may reject your calls if they don't match. When working locally, you can use `/etc/hosts` to fake a domain on your local machine.

The service provider will give you:

* Application ID
* Secret key
* Request Token URL
* Access Token URL
* Authorize URL

## Authentication Flow

Most of the flow will be done by the Play library.

1. Get a request token from the server (in a server-to-server call)
2. Redirect the user to the service provider, where he will grant your application rights to use his data
3. The service provider will redirect the user back, giving you a /verifier/
4. With that verifier, exchange the /request token/ for an /access token/ (server-to-server call)

Now the /access token/ can be passed to any call to access protected data.

## Example

```scala
object Twitter extends Controller {

  val KEY = ConsumerKey("xxxxx", "xxxxx")

  val TWITTER = OAuth(ServiceInfo(
    "https://api.twitter.com/oauth/request_token",
    "https://api.twitter.com/oauth/access_token",
    "https://api.twitter.com/oauth/authorize", KEY),
    false)

  def authenticate = Action { request =>
    request.queryString.get("oauth_verifier").flatMap(_.headOption).map { verifier =>
      val tokenPair = sessionTokenPair(request).get
      // We got the verifier; now get the access token, store it and back to index
      TWITTER.retrieveAccessToken(tokenPair, verifier) match {
        case Right(t) => {
          // We received the authorized tokens in the OAuth object - store it before we proceed
          Redirect(routes.Application.index).withSession("token" -> t.token, "secret" -> t.secret)
        }
        case Left(e) => throw e
      }
    }.getOrElse(
      TWITTER.retrieveRequestToken("http://localhost:9000/auth") match {
        case Right(t) => {
          // We received the unauthorized tokens in the OAuth object - store it before we proceed
          Redirect(TWITTER.redirectUrl(t.token)).withSession("token" -> t.token, "secret" -> t.secret)
        }
        case Left(e) => throw e
      })
  }

  def sessionTokenPair(implicit request: RequestHeader): Option[RequestToken] = {
    for {
      token <- request.session.get("token")
      secret <- request.session.get("secret")
    } yield {
      RequestToken(token, secret)
    }
  }
}
```
> **Next:** [[Integrating with Akka| ScalaAkka]]