# Authentication
Our backend APIs follow [OAuth 2.0](https://oauth.net/2/). We support two interfaces for authentication, **Token** and **Password**. Token authentication is used when you plan on interfacing directly with our backend APIs for token creation and refreshing. In this case you would implement your own custom token provider which conforms to our `TokenProvider` protocol, and make your own network requests to the backend.  Alternatively, you can use password authentication to authenticate using a username and password. In this case, our SDK will handle all token management.

Your **Sensibill.plist** requires the environment url string to be set for key `ENVIRONMENT`. Additionally, you can set the `CERTIFICATE_PINNING_FILES` if you are using certificate pinning.

## Token Authentication
Token authentication assumes that your custom token provider will handle creating and refreshing tokens. The `restore` method for the `Authenticator` class will setup the SDK with all necessary data, and will internally request for tokens from the custom token provider. To find documentation for our backend APIs please contact a representative from Sensibill who will provide you with details.

```swift
import SensibillUI

...

class CustomTokenProvider: TokenProvider {
    func provideTokenReplacement(completion: @escaping (Credentials?, Error?) -> ()) {
        // Token replacement code
    }
}

...

let tokenProvider = CustomTokenProvider()
Authenticator(tokenProvider: tokenProvider).start(identifier: "accessId") { error in
    // Handle error condition
}
```

## Password Authentication
Password authentication uses username and password for authentication. As such, a user must be registered using `PasswordAuthenticator().register(...)`. See our [API reference documentation](https://sensibill.github.io/sensibill-ios-distribution/index.html) for details on registration, accessId availability, and password strength. Password authentication also requires `API_KEY`, `API_SECRET`, and `CREDENTIAL_TYPE` in your **Sensibill.plist**.

```swift
import SensibillUI

...

PasswordAuthenticator().login(accessId: "accessId", password: "password") { error in
    // Handle error condition
}
```

## Auto Login
We provide a convenient interface to login as the last authenticated user, assuming that the tokens are still valid. To leverage this feature you simply call authentication methods using with default parameters values.

### Token Authentication
```swift
let tokenProvider = CustomTokenProvider()
Authenticator(tokenProvider: tokenProvider).start { error in
    // Handle error condition
}
```

### Password Authentication
If there is an error attempting to auto login with password authentication, the user is required to authenticate again with username and password as the tokens have expired.

```swift
PasswordAuthenticator().login { error in
    // Handle error condition
}
```
