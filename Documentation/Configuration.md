# Configuration

## Branding Configuration

There are two layers to branding configuration. The easiest way for most purposes is to use the `colors` property of the `SBSDKConfiguration` singleton object. The properties of `colors` will be used by the SDK to map your branding to all visual elements of the SDK. When the `SBSDKConfiguration` singleton is first accessed, it will be initialized with a default set of values for the colors of the SDK, but you can override these before starting the SDK:

### Swift
```swift
let configuration = SBSDKConfiguration.sharedInstance()

configuration?.colors.brandBackground = .red
configuration?.colors.brandForeground = .blue
configuration?.colors.brandForegroundDisabled = .gray
configuration?.colors.actionBackground = .purple
configuration?.colors.actionForeground = .orange

configuration?.applyColors()
```

### Objective-C
```objective-c
SBSDKConfiguration *configuration = [SBSDKConfiguration sharedInstance];

configuration.colors.brandBackground = [UIColor redColor];
configuration.colors.brandForeground = [UIColor blueColor];
configuration.colors.brandForegroundDisabled = [UIColor greyColor];
configuration.colors.actionBackground = [UIColor purpleColor];
configuration.colors.actionForeground = [UIColor orangeColor];

[configuration applyColors];
```

There are plenty of other colors one can set, but these are just some examples. Using the small number of properties on the `color` object, you can quickly brand the SDK with minimal work.

Since you are setting these properties on the singleton configuration object, there are no other changes needed (i.e. don't need to pass this object anywhere) as any UI which is created in the SDK next will use the new values. All you need to do is call `applyColors` when you are done setting your custom colors on the `SBSDKConfiguration` singleton and these colors will be mapped to their respective settings for you.

---

The second way of setting these values (which could be used together with the first method) is to override the underlying properties which those initial properties map to. The only reason you would want to use this method was if you wanted an individual element on one screen to appear differently than a similar element on another screen. Usually these two elements would have the same appearance (they'd both use the child property of `colors`, as shown above), but in this case you may want to have more granularity:

### Swift
```swift
let configuration = SBSDKConfiguration.sharedInstance()

configuration?.loadingViewConfiguration.loadingBackgroundColor = .black
configuration?.loadingViewConfiguration.loadingSpinnerColor = .white
```

### Objective-C
```objective-c
SBSDKConfiguration *configuration = [SBSDKConfiguration sharedInstance];

configuration.loadingViewConfiguration.loadingBackgroundColor = [UIColor blackColor];
configuration.loadingViewConfiguration.loadingSpinnerColor = [UIColor whiteColor];
```

Above you can see a simple example for overriding the colors of the loading view. If you didn't override the colors like this, they'd take on the colors defined in the higher-level configurations (in this example, brandBackground & brandForeground).

This section just outlines the basic way of changing color configurations, but there is more extensive documentation on all the configuration options available in another document, so please refer to the _iOS Customization Document_.

## Image Configuration

It is very simple to override what images the SDK uses with your own icons by including a Asset Catalog `SensibillAssets.xcassets` in the bundle which you set the `externalBundle` property to.

From there, simply add images with the same name as the ones contained in the internal `SensibillAssets.xcassets` located in the SDK: `Resources/Images/Images.xcassets`. For best results, please ensure the assets you provide are the same size as the ones you wish to override.
