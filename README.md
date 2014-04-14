## CanvasAPI by Instructure for Android

CanvasAPI is an Android library that will help you integrate your own third party app with [Canvas by Instructure](https://instructure.com/). CanvasAPI adds support for Android 2.3 (Gingerbread) and up.

CanvasAPI is built on the [Canvas API](https://canvas.instructure.com/doc/api/index.html). CanvasAPI is designed to allow for great flexibility while providing an easy to use interface. You can use CanvasAPI to build apps for open source versions of Canvas as well as instances hosted by Instructure.

## How To Get Started

- [Download CanvasAPI](https://github.com/instructure/CanvasAPI) 
- Become familiar with [Retrofit by Square](http://square.github.io/retrofit/)
- Download and explore the project found in the example directory.
- Take a look at the [Canvas API](https://canvas.instructure.com/doc/api/index.html) for a complete list of endpoints

##Usage
### Setup

In order to use CanvasAPI with Instructure managed instances of [Canvas LMS](https://github.com/instructure/canvas-lms) you must obtain a Client ID and Shared Secret. CanvasAPI uses [OAuth 2](https://canvas.instructure.com/doc/api/file.oauth.html) for authentication. Request your Client ID and Shared Secret by sending an email to <mike@instructure.com>. Make sure to give us your name, email, and what you are hoping to do with the CanvasAPI.

### Download

Download latest .aar from Maven:
```xml
    <dependency>
        <groupId>com.instructure</groupId>
        <artifactId>CanvasAPI</artifactId>
        <version>(insert latest version)</version>
    </dependency>
```

Or include using gradle:
```
compile 'com.instructure:CanvasAPI:LATEST_VERSION@aar'
```


You must also include [Retrofit by Square](https://github.com/square/retrofit)
```xml
<dependency>
    <groupId>com.squareup.retrofit</groupId>
    <artifactId>retrofit</artifactId>
    <version>(insert latest version)</version>
</dependency>
```


###Initializing CanvasAPI
Now that you have your Client ID and Shared Secret you can start using CanvasAPI. You then can begin the OAuth native flow outlined [here](https://canvas.instructure.com/doc/api/file.oauth.html). Once you've received the OAuth Request String, you can begin by calling `OAuth.getToken(String clientId, String clientSecret, String oAuthRequest, CanvasCallback<OAuthToken> callback))`.
This will return the OAuth token for the user. You then need to initialization the CanvasRestAdapter by calling `CanvasRestAdapter.setupInstance(Context context, String token, String domain)`

###Using the API
CanvasAPI is mainly structured around the notion of CanvasContexts. We currently support courses, groups, and users as CanvasContexts. For the most part, when you make an API call, you'll provide the API with a CanvasContext object. `Course.java` and `Group.java` both extend from `CanvasContext.java` so those can be used in these cases. You can also call `CanvasContext.getGenericContext(Type type, long id, String name)` to generate one on the fly.

###Tips and Tricks
* `APIHelpers.java` provides a lot of useful methods for CanvasAPI. These include getters/setters for the current OAuth Token, user-agent, protocol, domain, signed-in user, etc.

* `CanvasCallback.java` is our extension of [Retrofit's Callback class](https://github.com/square/retrofit/blob/master/retrofit/src/main/java/retrofit/Callback.java). It provides automatic caching and pagination support. 

* The cache callback occurs on whichever thread you launched the API call from.

* We only cache the FIRST page of all API calls.

###Error Handling
First and foremost, all logging that our library does uses the tag _canvas-api_, which can be found in `APIHelpers.java` 

We also provided an interface entitled `ErrorDelegate` that gives you callbacks when an error occurs during an API call. We specify which type of error occurred, so you can handle each of the cases however it makes the most sense for your project. You can set up a _default_ ErrorDelegate inside of `CanvasCallback.java` which will be used whenever one isn't provided as a constructor argument when creating a new CanvasCallback. If the server returns an HTTP200 or a HTTP401, we also return a `CanvasError` object that contains the exact error the server returned.

You can also handle errors manually by overriding `public boolean onFailure(RetrofitError RetrofitError)`. This callback allows you to handle errors in API calls before the error delegate methods are called. Returning true from this method means you have successfully handled the error and the error delegate will NOT be called; return false otherwise.

