# SDK Bundle

# Integration steps
## 1. Unity Editor
After the import of the .unitypackage, drag the `SDKBundle` prefab in your main scene. At this point the bundle is successfully added, to enable the various SDKs in the bundle you'll need to follow certain steps detailed below.

## 2. AdsMediation specific steps
## MoPub
### Xcode (iOS)
In the `info.plist` file you will have to fill a `GADApplicationIdentifier` field. Your project manager should be able to give you the corresponding identifier.
### Android Studio (Android)
In order to make AdMob (in MoPub) work and to avoid crash at launch, you need to add this code in an `AndroidManifest.xml` (most likely in Assets/Plugins/Android) : 
~~~~
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="YOUR_ID" />
~~~~
Your project manager should be able to give you the corresponding identifier.
(Android Studio is not need for this step.)

## AdMob
### Unity Editor
In `Assets/Google Mobile Ads/Settings...` check `Enabled` for `Google Ad Manager` and `Google AdMob` and fill both iOs and Android App Ids.

## 3. Firebase specific steps
### Unity Editor
In order to link Firebase to your application you have to get the `GoogleService-Info.plist` (for iOS) or `google-services.json` (for Android) associate with your app.

# Usage
## GDPR
In the Unity Editor, you'll be able to check/uncheck the `GDPR` boolean to enable the automatic management of GDPR, to show the GDPR popup you'll need to call 
~~~~
public void ShowGDPR()
~~~~
from the SDKBundleManager itself, you'll be able to check if you need to show the GDPR agreement Popup with the `shouldShowGDPR` variable which is updated at initialisation.
Don't forget to subscribe to `SDKBundleProtocol` in order to get the 
~~~~
void GDPRPopupAccepted();
~~~~
callback which will allow you to update your app accordingly when the GDPR is accepted and the popup disappear.

## Analytics
### Facebook Analytics
Facebook Analytics should be handled automatically by the `SDKBundle`. but if there is any problem during initialization you'll be able to refresh it by calling the following method:
~~~~
public void CheckActivation ()
~~~~
To track an event, just call the following method, if Facebook is not initialized yet, it will store your event to send it asap :
~~~~
public void TrackEvent (string eventName, Dictionary<string, object> eventParam = null)
~~~~
### Firebase Analytics
To track an event through Firebase Analytics, simply call :
~~~~
public void LogEvent (string eventName, Parameter[] parameters = null)
~~~~
Where parameters is an optional array of informations to send (if you need precise data of an event).
## Remote Configuration
### Firebase Remote Configuration
To use the remote configuration you'll need two things :
~~~~
public void SetDefaultConfig (Dictionary<string, object> defaults)
~~~~
First thing first, you'll need to set a default configuration dictionary in order to have all the required values setted up.
You need to subscribe to a specific interface via the `SetCallback(IFirebaseCallbacks target)` method : `IFirebaseCallbacks` which contains `FetchComplete`, a callback that will notify your app when the remote configuration is fetched correctly.
In order to activate the retrieved configuration you need to call :
~~~~
public bool ActivateConfig ()
~~~~
The method returns a bool indicating if the activation is successful (if there is a fetched config non activated, otherwise it always returns false).
To retrieve a config value, simply call :
~~~~
public ConfigValue GetConfigElement(string key)
~~~~
Where `key` is the element key.

## Crashlytics
*This section does concern you if you used Fabric in your game*. Since it has been removed in the SDKBundle 1.5.0, Crashlytics migrates from Fabric to Firebase. 
In order to keep track of your Crashlytics, in Unity, you need to open the Firebase Crashlytics menu in `Window -> Firebase -> Crashlytics`. You will find a field `Fabric Key`. Fill it using your previous Fabric Key and save.

## Attribution
### AppsFlyer
To initialize AppsFlyer, use the following code :
~~~~
public void Init("dev_key_id", "android_id", "ios_id")
~~~~
You will be able to track events using AppsFlyer. Use the following code to track event :
~~~~
public void TrackEvent("event_name")
~~~~
You can add a `Dictionary<string, string>` as second parameter for the TrackEvent method.
### Tenjin
In order to use Tenjin you'll need to set an `apiKey`. You'll do that by calling the `SetApiKey` method in the `TenjinManager` object.
~~~~
public void SetApiKey (string apiKey)
~~~~
Once it's done, you'll be able to send custom event through the `SendEvent` method, `eventName` will be the name of your event and you'll be able to specify a `value`.
~~~~
public void SendEvent (string eventName, string value = null)
~~~~

## Ads
### Initialization
Call this method from the `adsManager` to initialize your ads. You'll need to fill the adUnits array with the types you want to use in your app, and your class that subscribe to the `IAdsModule` interface to get your callback. 
~~~~
public void InitializeAds(string[] interstitials, string[] banners, string[] rvs, IAdsModule callbacks)
~~~~
To be notified of every actions on your ads, subscribe to the desired interfaces :
~~~~
public IAdsModule `adsCallback`;
public IBannerModule `bannerCallbacks`;
public IInterstitialModule `interstitialCallbacks`;
public IRewardedVideoModule `rewardedVideoCallbacks`;
~~~~
The following callback is called when your mediation SDK is setup :
~~~~
public void AdsModuleInitialized()
~~~~
#### MoPub specific steps
To use Fyber (since version 1.7.3), you have to set your app Fyber ID before calling `InitializeAds()`. You can use the following code :
~~~~
((MoPubAdsHandler)SDKBundleManager.Instance.adsManager).SetFyberID("your-fyber-app-id");
~~~~
To use TikTok (since version 1.8.0), you have to set your Tik Tok App ID before calling `InitializeAds()`. 
~~~~
((MoPubAdsHandler)SDKBundleManager.Instance.adsManager).SetTikTokAppID("your-tiktok-app-id");
~~~~
### Ads management
You can request an ad using the following method from `adsManager`:
~~~~
public void RequestInterstitial (string interId)
public void RequestRewardedVideo (string rvId)
public void RequestBanner (string bannerId)
~~~~
To show an ad, call the desired method, where `bannerId`, `interId` and `rvId` are your adUnits (we advise you to put a list of predefined variables to keep track of your adUnits and be sure to have the proper adUnit passed when coding) :
~~~~
public void ShowBanner(string bannerId, BannerPosition adPosition)
public void ShowInterstitial(string interId)
public void ShowRewardedVideo(string rvId)
~~~~

## Cross promotion
To use the CPKit, you need to check the `Use CPKit` box on the SDKBundle prefab inspector, and initialize it from the code with the following method:
~~~~
public void InitializeCrossPromo (REQUEST_SERVER server, int appId, IPublicCallbacks callback = null)
~~~~
If you need to change the callback receiver you can achieve it by calling:
~~~~
public void SetCallback (IPublicCallbacks callback = null)
~~~~
To get creative you'll call :
~~~~
public void AskCreative (string placement, CPAdFormat format)
~~~~
If you need to check if a creative is ready call `CreativeReady`. And the `IPublicCallbacks` contains all informations about CPKit and creative states.
To simplify the process of getting your own square displayed we made a few prefab that you can use in the /Resources/Prefabs/ folder inside the CPKit.

1. Square:
Simply drag and drop the `SquareZone` object in your scene and place it where you need. To get a square shown in it you'll need to retrieve its component `SquareController` and call this method :
~~~~
public void Init (CPDisplaySquare assetInfos)
~~~~
`CPDisplaySquare` being what you're given in the `SquareVideoReceived` callback.

2. Native:
Drag and drop the `NativeZone`object in your scene and place it where you need. To get a native shown in it you'll need to retrieve its component `NativeController` and call this method :
~~~~
public void Init (CPDisplayNative assetInfos)
~~~~
`CPDisplayNative` being what you're given in the `NativeAdReceived` callback.
