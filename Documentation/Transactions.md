# Transactions

## Transaction Linking
**Transaction Linking** allows you to capture a paper receipt passing in a `transactionID`. This allows us to create a link on the receipt to the transaction id, and is particularly helpful to banking clients who want to link their account transactions to receipts.

To achieve this, first initialize an `SBTransaction` and set the `externalAccountTransactionID` in its private metadata. When initializing the `SensibillCaptureNavigationController` pass the transaction.

It is recommended, that you register an observer on the transaction so that you can react to different transaction states. 

```swift
func registerTransactionObserver() {
    SBDataEvent.sharedManager().addTransactionObserver(self) { (type, transaction) in
        guard let transaction = transaction else {
            return
        }

        if transaction.privateMetaData?.externalAccountTransactionID == transactionCopy.identifier {
            switch transaction.status {
            case .needsSynchronization:
                // Uploading receipt image
                break
            case .processing:
                // Image uploaded successfully and processing started
                break
            case .uploaded:
                // Receipt processed successfully
                if !transaction.receiptID.isEmpty {
                    // Store transaction.receiptID
                }
                break
            case .failed, .stopped: 
                // Image upload failed or was stopped externally
                break
            }
        }
    }
}

func presentReceiptCaptureWithTransasctionId() {
    let uploadTransaction = SBTransaction(imageData: nil)
    uploadTransaction.privateMetaData?.externalAccountTransactionID = "transactionID"

    if let viewController = SensibillCaptureNavigationController(transaction: uploadTransaction, exitAfterCapture: true) {
        self.present(viewController, animated: true, completion: nil)
    }
}
```

## Transaction Matching
**Transaction Matching** allows you to pass a list of `Transaction` objects that represent bank transactions, and we will attempt to match them to receipts. The more fields that are set on a transaction the more likely we are to find make a unique match, but the bare minimum is a date and amount. See the [API reference documentation](https://sensibill.github.io/sensibill-ios-distribution/index.html) for details on `Transaction` and `ReceiptSummary` objects. If you have transactions that have already been linked using [Transaction Linking](#transaction-linking), you can set the `transactionID` on transaction's `identifier` to help the matcher filter out matched receipts.

```swift
func matchTransactions() {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyy-MM-dd"

    let transaction1 = Transaction(identifier: "transactionID", date: dateFormatter.date(from: "2018-05-22"), amount: 2.93, currencyCode: "CAD", merchant: "Tim Hortons")
    let transaction2 = Transaction(identifier: nil, date: dateFormatter.date(from: "2018-05-22"), amount: 26.10, currencyCode: "CAD", merchant: nil)

    ReceiptCollection.shared.match(transactions: [transaction]) { (matches, status) in
        if status == .success {
            let receipt = matches[transaction1]!.first!
            // Do something with receipt.identifier
        }
    }
}
```
