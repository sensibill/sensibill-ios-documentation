![Sensibill](Sensibill-Logo.png)

**Version: 1.15.0**

* [Overview](#overview)
* [Data Protection](#data-protection)
* [Requirements](#requirements)
* [Installation](#installation)
* [Quick Start](#quick-start)
* Usage
	* [Authentication](Documentation/Authentication.md) - [Token Authentication](Documentation/Authentication.md#token-authentication), [Password Authentication](Documentation/Authentication.md#password-authentication)
	* UI Presentation - Receipt List, Receipt Capture, Receipt Detail, Receipt Exports
	* Transaction Matching - Linking, Matching
	* Configuration
	* Examples Code
* [License](License.md)

## Overview
Sensibill is a iOS library that converts paper and email receipts into a digital format, extracting a number of data points such as merchant information, amounts, dates, and line item data.

## Data Protection
**Important**: The library makes the assumption that Data Protection is enabled and working. For this reason, data cached locally (using NSFileManager) does not use secondary encryption on top of Data Protection. The library leaves this work up to iOS assuming that Data Protection is functioning.

## Requirements
* Xcode 9.4.1 - Build for iOS 11.4
* iOS 9.3+

## Installation
* Download and Import `SensibillUI.framework` into your project, and ensure that it is included in **Embedded Binaries** and **Linked Frameworks and Libraries**
* Create and Import `Sensibill.plist` into the main bundle of your target. Configure the plist with the following values:
	* API_KEY: String
	* API_SECRET: String
	* CREDENTIAL_TYPE: String
	* REDIRECT_URL: String
	* ENVIRONMENT: String
	* CERTIFICATE_PINNING_FILES (optional): Array of Strings

## Quick Start
Import **SensibillUI** into the file that will present the Receipt List. Our example assumes that you have registered a user and that you are using Password Authentication. You can dig deeper into documentation for authentication and UIView presentation.

### Swift
```swift
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

### Objective-C

```objective-c
@import SensibillUI;

...

PasswordAuthenticator *authenticator = [PasswordAuthenticator new];
[authenticator loginWithAccessId:@"accessId" password:@"password" completion:^(enum SensibillError error) {
    if (error == SensibillErrorNone) {
        [self presentViewController:[ReceiptListViewController new] animated:YES completion:nil];
    } else {
        // Handle error condition
    }
}];
```
