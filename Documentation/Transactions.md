# Transactions

## Transaction Linking
**Transaction Linking** allows you to capture a paper receipt passing in a `transactionID`. This allows us to create a link on the receipt to the transaction id, and is particularly helpful to banking clients who want to link their account transactions to receipts.

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
**Transaction Matching** allows you to pass a list of `Transaction` objects that represent bank transactions, and we will attempt to match them to receipts. The more fields that are set on a transaction the more likely we are to find make a unique match, but the bare minimum is a date and amount. See the SDK reference documentation for details on `Transaction` and `ReceiptSummary` objects. If you have transactions that have already been linked using [Transaction Linking](#transaction-linking), you can set the `transactionID` on transaction's `identifier` to help the matcher filter out matched receipts.

```swift
func matchTransactions() {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy-MM-dd"

    let transaction1 = Transaction(identifier: "transactionID", date: dateFormatter.date(from: "2018-05-22"), amount: 2.93, currencyCode: "CAD", merchant: "Tim Hortons")
    let transaction2 = Transaction(identifier: nil, date: dateFormatter.date(from: "2018-05-22"), amount: 26.10, currencyCode: "CAD", merchant: nil)

    ReceiptCollection.shared.match(transactions: [transaction]) { (matches, error) in
        if error == .success {
            let receipt = matches[transaction1]!.first!
            // Do something with receipt.identifier
        }
    }
}
```
