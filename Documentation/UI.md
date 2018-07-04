# UI Presentation
## Receipt List
To simply use the default Receipt List you can initialize the controller with default values and present it.

```swift
self.present(ReceiptListViewController(), animated: true, completion: nil)
```

### Custom Bottom Bar and Left UIBarButtonItem
We allow you to override the default bottom bar with you own custom `UIView`. You can also customize the left `UIBarButtonItem` to use a custom icon, and handle what happens when the button is tapped. This is done by having your `UIViewController` conform to `ReceiptListViewControllerDataSource`.

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
        let bottomView = UIView(frame: CGRect(x: 0, y: 0, width: 320, height: 40))
        bottomView.backgroundColor = UIColor.blue
        return bottomView
    }

    func bottomViewHeight(in viewController: UIViewController) -> CGFloat {
        return 40
    }

}
```

## Receipt Capture
To present our capture controller to leverage rectangle detection to automatically capture and crop receipt images, you can present it after setting your `UIViewController` as the delegate that conforms to `SBViewControllerDelegate`.

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

## Receipt Detail
To present the details of a receipt, you can initialize an `SensibillReceiptViewController` with a `receiptId` and present it after setting your `UIViewController` as the delegate that conforms to `SBViewControllerDelegate`.

```swift
class ViewController: UIViewController, SBViewControllerDelegate {

  func presentReceiptDetail() {
        if let viewController = SensibillReceiptViewController(receiptId: "receiptId") {
            viewController.dismissDelegate = self
            self.present(viewController, animated: true, completion: nil)
        }
    }

  //MARK: SBViewControllerDelegate

  func dismissSensibillViewController(_ controller: UIViewController!) {
      controller.dismiss(animated: true, completion: nil)
  }

}

## Receipt Export
