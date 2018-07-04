# Sensibill iOS

###### Last updated for v1.15.0

## Overview
### Introduction
This SDK provides simple interfaces to facilitate receipt capture and management. If you are looking for documentation about our web APIs, you can find it [here](https://receipts-sandbox.sensibill.io/docs/sensibill).

### Data Protection
**Important**: The SDK makes the assumption that Data Protection is enabled and working. For this reason, data cached locally (using NSFileManager) does not use secondary encryption on top of Data Protection. The SDK leaves this work up to iOS assuming that Data Protection is functioning.

## Getting Started

### Importing to Project
* Import `SensibillUI.framework` into your project, and ensure that it is included in **Embedded Binaries** and **Linked Frameworks and Libraries**
* Import `Sensibill.plist` into the main bundle of your target. Configure the plist with the following values:
	* API Key
	* API Secret
	* Credential Type
	* Redirect Url
	* Environment
	* Pinning Certificates (optional)

We support Password and Token authentication. Password authentication relies on accessId and password, where as Token authentication assumes you implement your own TokenProvider that provides valid tokens.

### Password Authentication

```
import SensibillUI

...

PasswordAuthenticator().login(accessId: "accessId", password: "password") { error in
    if error == .none {
        self.present(ReceiptListViewController(), animated: true, completion: nil)
    } else {
        // Handle error condition
    }
}
```

### Token Authentication

```
import SensibillUI

...

class CustomTokenProvider: TokenProvider {
    func provideTokenReplacement(completion: @escaping (Credentials?, Error?) -> ()) {
        // Token replacement code
    }
}

...

let tokenProvider = CustomTokenProvider()
Authenticator(tokenProvider: tokenProvider).restore(cacheIdentifier: "accessId") { error in
    if error == .none {
        self.present(ReceiptListViewController(), animated: true, completion: nil)
    } else {
        // Handle error condition
    }
}
```

### Auto Login

We support auto login the last authenticated user, provided that the tokens are still valid. To leverage this feature you simply call authentication methods using with default parameters values.

#### Password Authentication

```
PasswordAuthenticator().login { error in
    if error == .none {
        self.present(ReceiptListViewController(), animated: true, completion: nil)
    } else {
        // Handle error condition
    }
}
```

#### Token Authentication

```
let tokenProvider = CustomTokenProvider()
Authenticator(tokenProvider: tokenProvider).restore { error in
    if error == .none {
        self.present(ReceiptListViewController(), animated: true, completion: nil)
    } else {
        // Handle error condition
    }
}
```

## Configuration

To see how to configure the SDK further to toggle features, replace images, and match you brand colours see our [wiki](https://github.com/sensibill/sensibill-ios/wiki/Configuration)

## Examples

To see examples for setting up Local Reminders and Inbox Sync see our [wiki](https://github.com/sensibill/sensibill-ios/wiki/Examples)

## Dependencies

* Inbox Sync:
	* [AppAuth (0.9.1)](https://github.com/openid/AppAuth-iOS/tree/0.9.1)
* Analytics:
	* [Piwik (v4.2.0)](https://github.com/piwik/piwik-sdk-ios/tree/v4.2.0)
