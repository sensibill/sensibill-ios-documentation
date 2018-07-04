# Authentication
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

## Token Provider Example
*//TODO: Fill in here with example*
