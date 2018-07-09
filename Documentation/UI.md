# UI Presentation
## Receipt List
To simply use the default Receipt List you can initialize the controller with default values and present it.

```swift
self.present(ReceiptListViewController(), animated: true, completion: nil)
```

### Custom Bottom Bar and Left UIBarButtonItem
We allow you to override the default bottom bar with your own custom `UIView`. You can also customize the left `UIBarButtonItem` to use a custom icon, and handle what happens when the button is tapped. This is done by having your class conform to `ReceiptListViewControllerDataSource`. When initializing the Receipt List you will pass in your class as the `dataSource`.

*Note:* One caveat of overriding the bottom bar, is that you will always have the responsibility for presenting the [Receipt Exports](#receipt-export) screen.

```swift
class ViewController: UIViewController, ReceiptListViewControllerDataSource {
    var receiptListViewController: ReceiptListViewController?

    func presentReceiptList() {
        receiptListViewController = ReceiptListViewController(dataSource: self)
        self.present(receiptListViewController!, animated: true, completion: nil)
    }

    //MARK: ReceiptListViewControllerDataSource

    func leftBarButtonItem(in viewController: UIViewController) -> UIBarButtonItem? {
        return UIBarButtonItem(image: UIImage(named: "close", in: SBSDKBundle.uiBundle(), compatibleWith: nil), style: .plain, target: self, action: #selector(didTapDismissReceiptList))
    }

    @objc public func didTapDismissReceiptList() {
        self.receiptListViewController.dismiss(animated: true, completion: nil)
    }

    func bottomView(in viewController: UIViewController) -> UIView {
        // Return your bottomView
    }

    func bottomViewHeight(in viewController: UIViewController) -> CGFloat {
        // Return bottomView height
    }

}
```

## Receipt Capture
Our receipt capture controller provides a convenient interface that leverages rectangle detection to automatically capture and crop receipt images. You can present it after setting your class as the delegate that conforms to `SBViewControllerDelegate`.

```swift
class ViewController: UIViewController, SBViewControllerDelegate {

  func presentReceiptCapture() {
      let captureController = SensibillCaptureNavigationController()
      captureController.dismissDelegate = self
      self.present(captureController, animated: true, completion: nil)
  }

  //MARK: SBViewControllerDelegate

  func dismissSensibillViewController(_ controller: UIViewController!) {
      controller.dismiss(animated: true, completion: nil)
  }

}
```
*Note:* Receipt Capture can also be used to link a bank account transaction to a receipt. See [Transaction Linking](Transactions.md#transaction-linking) for  details.

## Receipt Detail
To display the details of a receipt, you can initialize an `SensibillReceiptViewController` with a `receiptId` and push it with your navigation controller.

```swift
class ViewController: UIViewController, SBViewControllerDelegate {

    func presentReceiptDetail() {
        if let viewController = SensibillReceiptViewController(receiptId: "receiptId") {
            self.navigationController?.pushViewController(viewController, animated: true)
        }
    }
    
}
```

## Receipt Export
To see receipts that were recently exported, you can initialize an `SBRecentExportController` and present it.

```swift
let controller = SBRecentExportController(nibName: "SBRecentExportController", bundle: SBSDKBundle.uiBundle())
self.present(controller, animated: true, completion: nil)
```
