Configuring Sensibill SDK using JSON
====================================
###### Last updated for v1.10.0

Overview
--------
#### Introduction
This doc provides a brief example of how to configure the SDK reading from a `JSON` file, and demonstrates how the configuration can be modified during runtime.

_______________________
Sample Code
-----------------------

#### Example JSON File
JSON provides a nice structure for key/value pairs that lends itself nicely to configuration. A `.json` file can be fetched from a server or stored locally, and will have the same effect of changing the SDK configuration on runtime.
Here is an example JSON that touches on some of the common objects found of our `SBSDKConfiguration`

```
{
    //UIColor
    "primary-colour": {
        "red":0.749019607843137,
        "green":0.6,
        "blue":0.231372549019608,
        "alpha":1.0
    },
    "secondary-colour": {
        "red":0.02352941176,
        "green":0.18823529411,
        "blue":0.26666666666,
        "alpha":1.0
    },

    //UIFont
    "regular-font": {
        "name":"Helvetica",
        "size":12.0
    },
    "bold-font": {
        "name":"Helvetica-Bold",
        "size":14.0
    },

    //UIImage
    "loading-image-url": "https://path/to/file",
    "image-base64": "some base64 encoded string",

    //BOOL
    "categories-enabled": false,

    //NSBundle
    "localization-bundle-name": "SensibillLocalization"
}
```

#### Convert JSON to NSDictionary
The code below shows how to read JSON from a file stored in the local bundle. You could alternatively fetch `NSData` from a server to drive the changes. This assumes that we have created a file called `sensibill-config.json`

```
- (NSDictionary *)getJSONConfig {
    NSString *path = [[NSBundle mainBundle] pathForResource:@"sensibill-config" ofType:@"json"];
    NSData *data = [NSData dataWithContentsOfFile:path];
    return [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingAllowFragments error:nil];
}
```

#### Configuring SBSDKConfiguration using JSON dictionary
The following code shows how to convert JSON stored objects to values the Objective-C can work with

```
- (void) configureWithJSON {
    NSDictionary *json = [self getJSONConfig];

    //Colours
    NSDictionary *jsonPrimaryColour = json[@"primary-colour"];
    UIColor *primaryColour = [UIColor colorWithRed:[jsonPrimaryColour[@"red"] floatValue]
                                             green:[jsonPrimaryColour[@"green"] floatValue]
                                              blue:[jsonPrimaryColour[@"blue"] floatValue]
                                             alpha:[jsonPrimaryColour[@"alpha"] floatValue]];
    NSDictionary *jsonSecondaryColour = json[@"secondary-colour"];
    UIColor *secondaryColour = [UIColor colorWithRed:[jsonSecondaryColour[@"red"] floatValue]
                                               green:[jsonSecondaryColour[@"green"] floatValue]
                                                blue:[jsonSecondaryColour[@"blue"] floatValue]
                                               alpha:[jsonSecondaryColour[@"alpha"] floatValue]];

    SBSDKUseLightIconsConfiguration *lightIcons = [SBSDKConfiguration new].lightIconsConfigurations;
    SBSDKConfiguration *configuration = [[SBSDKConfiguration alloc] initWithMainColor:primaryColour secondaryColor:secondaryColour lightIconsConfiguration:lightIcons];

    //Fonts
    NSDictionary *jsonRegularFont = json[@"regular-font"];
    NSDictionary *jsonBoldFont = json[@"bold-font"];
    configuration.sdkFont = [[SBSDKFontSet alloc] initWithBoldFont:[UIFont fontWithName:jsonBoldFont[@"name"]
                                                                                   size:[jsonBoldFont[@"size"] floatValue]]
                                                    andRegularFont:[UIFont fontWithName:jsonRegularFont[@"name"]
                                                                                   size:[jsonRegularFont[@"size"] floatValue]]];
    //Images
    UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:[NSURL URLWithString:json[@"loading-image-url"]]]];
    configuration.loadingViewConfiguration.loadingImage = image;

    //Boolean Flags
    BOOL nonReceiptEmailsEnabled = [json[@"non-receipt-emails-enabled"] boolValue];
    configuration.nonReceiptEmailsEnabled = nonReceiptEmailsEnabled;

    //Localization Override
    NSString *mainBundlePath = [[NSBundle mainBundle] pathForResource:json[@"localization-bundle-name"] ofType:@"bundle"];
    configuration.localizationBundle = [NSBundle bundleWithPath:mainBundlePath];
}
```

#### Using Configuration
The same way you would configure the SDK manually, you call the following method found in `SensibillSDK`:

```
+ (void)configureWithEnvironment:(int)environment apiKey:(NSString *)apiKey secret:(NSString *)secret credentialType:(NSString *)credentialType redirectURI:(NSString *)redirectURI andConfiguration:(SBSDKConfiguration *)configuration;
```