# Transactions

## Transaction Linking
**Transaction Linking** allows you to capture a paper receipt passing in a transactionID. This allows us to create a link on the receipt to the transaction id, and is particularly helpful to banking clients who want to link their account transactions to receipts.

To achieve this, first initialize an `SBTransaction` and set the `externalAccountTransactionID` in it's private metadata. When initializing the `SensibillCaptureNavigationController` pass the uploadTransaction.

```swift
func presentReceiptCaptureWithTransasctionId() {
    let uploadTransaction = SBTransaction(imageData: nil)
    uploadTransaction.privateMetaData?.externalAccountTransactionID = "transactionID"

    if let viewController = SensibillCaptureNavigationController(transaction: uploadTransaction, exitAfterCapture: true) {
        self.present(viewController, animated: true, completion: nil)
    }
}
```

## Transaction Matching

*Fill out transaction matching*
