_This branch corresponds to the [article updated on January 08, 2020](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/). To see the code corresponding to previous versions of the article, please switch to the branch with the date of the version you're interested in. For example, for the original version published on March 12, 2019, select the branch entitled [2019-03-12](https://github.com/RobinCsl/build-standalone-expo-app/tree/2019-03-12)._

---

# Build Standalone Expo .apk and .ipa with Turtle CLI

_This is a condensed version which only shows the commands for each section._

We will go through the following steps:

1. [Install Turtle CLI](#1-turtle-cli)
2. [Create a dummy application](#2-create-a-dummy-application)
3. [Publish Expo app on local server](#3-publish-expo-app-on-local-server)
4. [Create APK file -- Android](#4-create-apk-file----android)
5. [Create IPA file -- iOS](#5-create-ipa-file----ios)
6. [Distribute and install](#6-distribute-and-install)

## 1. [Turtle CLI](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#1-turtle-cli)

To install Turtle CLI, run

```bash
$ npm install -g turtle-cli
```

## 2. [Create a dummy application](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#2-create-a-dummy-application)

Let's create a dummy application using Expo CLI. In your terminal, run

```bash
$ npx expo init ExampleApplication
```

Add the keys `"bundleIdentifier"` under `"ios"`, and `"package"` under `"android"` in your `app.json` file:

```json
...
    "ios": {
      ...
      "bundleIdentifier": "com.example.exampleApplication",
      ...
    },
    "android": {
      ...
      "package": "com.example.example_application",
      ...
    }
...
```

## 3. [Publish Expo app on local server](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#3-publish-expo-app-on-local-server)

Assuming that your local web server will be running on `http://127.0.0.1:8000`, export the app with

```bash
$ expo export --public-url http://127.0.0.1:8000
...
Export was successful. Your exported files can be found in dist
```


Serve the `dist` directory on your web server (and make sure it's available at the same URL that you chose for the flag `--public-url` above). For example, run

```bash
$ cd dist
# Assuming Python 3 is installed on your machine
$ python -m http.server
# OR
$ python3 -m http.server
```

Note: if for some reason you need to re-export your application (because you modified your `app.json` file since the last export, for instance), you must first remove the `dist` directory:

```bash
# assuming you are at the root of the project
$ rm -rf dist
$ expo export --dev --public-url http://127.0.0.1:8000
```

## 4. [Create APK file -- Android](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#4-create-apk-file----android)

Verify that your local server is running, e.g.

```bash
$ curl http://127.0.0.1:8000/android-index.json
{"name":"ExampleApplication","slug":"ExampleApplication","privacy":"public","sdkVersion":"36.0.0","platforms":["ios","android","web"],"version":"1.0.0","orientation":"portrait","icon":"./assets/icon.png","splash":{"image":"./assets/splash.png","resizeMode":"contain","backgroundColor":"#ffffff","imageUrl":"http://127.0.0.1:8000/assets/43ec0dcbe5a156bf9e650bb8c15e7af6"},"updates":{"fallbackToCacheTimeout":0},"ios":{"supportsTablet":true,"bundleIdentifier":"com.example.exampleApplication"},"android":{"package":"com.example.example_application"},"locales":{},"iconUrl":"http://127.0.0.1:8000/assets/f82b34f900882c5120a1bfbf6df22a27","bundledAssets":["asset_3a2ba31570920eeb9b1d217cabe58315.ttf","asset_8b12b3e16d591abc926165fa8f760e3b.json","asset_744ce60078c17d86006dd0edabcd59a7.ttf","asset_461d9bba8b6a3c91675039df12cfe6ca.json","asset_140c53a7643ea949007aa9a282153849.ttf","asset_94c4ffdcbffeb0570c635d7f8edd8a25.json","asset_6beba7e6834963f7f171d3bdd075c915.ttf","asset_648f2d510967a87880abfed9476aeb28.json","asset_b06871f281fee6b241d60582ae9369b9.ttf","asset_f1f91feb805137c9283fb766620ec5eb.json","asset_09dd345dbd4ec5a0874841d5749ac153.json","asset_0886a6b127c6057cee83f9c65c7ffd62.json","asset_2e562d4ebf15395f00bc738738f79291.ttf","asset_872545dde71de3842234bf6afe80c4cb.ttf","asset_c6aef942e3668158ec29d4adcb2e768f.ttf","asset_e20945d7c929279ef7a6f1db184a4470.ttf","asset_60668d999bbaf663420340f7bdd580d7.json","asset_b2e0fc821c6886fb3940f85a3320003e.ttf","asset_3e6805fbc794680014716b8c752f20b8.json","asset_5a293a273bee8d740a045d9922b9a9ae.ttf","asset_b582e1c8a605c3b9a1c26e09789a78d4.json","asset_a37b0c01c0baf1888ca812cc0508f6e2.ttf","asset_7e078700f0c35367a56c5bbb2047dda7.json","asset_8e7f807ef943bff1f6d3c2c6e0f3769e.ttf","asset_fdc01171a7a7ea76b187afcd162dee7d.json","asset_d2285965fe34b05465047401b8595dd0.ttf","asset_647543ebfccf6e5495434383598453d1.json","asset_5cdf883b18a5651a29a4d1ef276d2457.ttf","asset_74d124a3caeac2bea111f3ca2f2dd34a.json"],"assetUrlOverride":"./assets","publishedTime":"2020-01-10T08:40:46.255Z","commitTime":"2020-01-10T08:40:46.255Z","revisionId":"XwJm9wdyZw","developer":{"tool":"exp"},"id":"@anonymous/ExampleApplication","bundleUrl":"http://127.0.0.1:8000/bundles/android-180fb088cff97225a61024176ed1af3a.js","platform":"android","dependencies":["expo","react","react-dom","react-native","react-native-web"]}⏎
```

If you know which Expo SDK version you are going to use, you can run

```bash
$ turtle setup:android --sdk-version <SDK-VERSION>
```

### Create Keystore

You can generate a keystore file with the following command
```bash
$ keytool -genkeypair -v -keystore keystore.jks -alias keyalias -keyalg RSA -keysize 2048 -validity 9125
```

### Build APK

Make sure that you are serving the `dist` directory on http://127.0.0.1:8000 as explained above in **Run a local server**, and run

```bash
EXPO_ANDROID_KEYSTORE_PASSWORD=<KEYSTORE_PASSWORD> \
EXPO_ANDROID_KEY_PASSWORD=<KEY_PASSWORD> \
turtle build:android \
  --type apk \
  --keystore-path <KEYSTORE_PATH> \
  --keystore-alias <KEYSTORE_ALIAS> \
  --public-url http://127.0.0.1:8000/android-index.json
```

Once the build finishes, take note of the location of your build `.apk` file. For example,

```bash
Jan 9 22:38:42 turtle[76552] INFO:  copying build to fake upload directory
  platform: "android"
  buildPhase: "copying build artifact"
Jan 9 22:38:42 turtle[76552] INFO:  copied build to ~/expo-apps/@anonymous\ExampleApplication-9be976cea1fb4651a6fa04d8432873eb-signed.apk
```


## 5. [Create IPA file -- iOS](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#5-create-ipa-file----ios)


First, verify that your local server is running, e.g.

```bash
$ curl http://127.0.0.1:8000/ios-index.json
{"name":"ExampleApplication","slug":"ExampleApplication","privacy":"public","sdkVersion":"36.0.0","platforms":["ios","android","web"],"version":"1.0.0","orientation":"portrait","icon":"./assets/icon.png","splash":{"image":"./assets/splash.png","resizeMode":"contain","backgroundColor":"#ffffff","imageUrl":"http://127.0.0.1:8000/assets/43ec0dcbe5a156bf9e650bb8c15e7af6"},"updates":{"fallbackToCacheTimeout":0},"ios":{"supportsTablet":true,"bundleIdentifier":"com.example.exampleApplication"},"android":{"package":"com.example.example_application"},"locales":{},"iconUrl":"http://127.0.0.1:8000/assets/f82b34f900882c5120a1bfbf6df22a27","bundledAssets":["asset_3a2ba31570920eeb9b1d217cabe58315.ttf","asset_8b12b3e16d591abc926165fa8f760e3b.json","asset_744ce60078c17d86006dd0edabcd59a7.ttf","asset_461d9bba8b6a3c91675039df12cfe6ca.json","asset_140c53a7643ea949007aa9a282153849.ttf","asset_94c4ffdcbffeb0570c635d7f8edd8a25.json","asset_6beba7e6834963f7f171d3bdd075c915.ttf","asset_648f2d510967a87880abfed9476aeb28.json","asset_b06871f281fee6b241d60582ae9369b9.ttf","asset_f1f91feb805137c9283fb766620ec5eb.json","asset_09dd345dbd4ec5a0874841d5749ac153.json","asset_0886a6b127c6057cee83f9c65c7ffd62.json","asset_2e562d4ebf15395f00bc738738f79291.ttf","asset_872545dde71de3842234bf6afe80c4cb.ttf","asset_c6aef942e3668158ec29d4adcb2e768f.ttf","asset_e20945d7c929279ef7a6f1db184a4470.ttf","asset_60668d999bbaf663420340f7bdd580d7.json","asset_b2e0fc821c6886fb3940f85a3320003e.ttf","asset_3e6805fbc794680014716b8c752f20b8.json","asset_5a293a273bee8d740a045d9922b9a9ae.ttf","asset_b582e1c8a605c3b9a1c26e09789a78d4.json","asset_a37b0c01c0baf1888ca812cc0508f6e2.ttf","asset_7e078700f0c35367a56c5bbb2047dda7.json","asset_8e7f807ef943bff1f6d3c2c6e0f3769e.ttf","asset_fdc01171a7a7ea76b187afcd162dee7d.json","asset_d2285965fe34b05465047401b8595dd0.ttf","asset_647543ebfccf6e5495434383598453d1.json","asset_5cdf883b18a5651a29a4d1ef276d2457.ttf","asset_74d124a3caeac2bea111f3ca2f2dd34a.json"],"assetUrlOverride":"./assets","publishedTime":"2020-01-10T08:40:46.255Z","commitTime":"2020-01-10T08:40:46.255Z","revisionId":"XwJm9wdyZw","developer":{"tool":"exp"},"id":"@anonymous/ExampleApplication","bundleUrl":"http://127.0.0.1:8000/bundles/ios-404585eb9ae529b61ed72e5df8a757ad.js","platform":"ios"}⏎
```

If you know which Expo SDK version you are going to use, you can run

```bash
$ turtle setup:ios --sdk-version <SDK-VERSION>
```

### Create Signing keys

See this [section](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#52-create-signing-keys) in the article.


### Build IPA

Make sure that you are serving the `dist` directory on http://127.0.0.1:8000 as explained **Run a local server**, and run

```bash
EXPO_IOS_DIST_P12_PASSWORD=<PASSWORD HERE> \
turtle build:ios \
  --team-id <YOUR_TEAM_ID> \
  --dist-p12-path </path/to/your/dist/cert.p12> \
  --provisioning-profile-path </path/to/your/provisioning/profile.mobileprovision>
  --public-url http://127.0.0.1:8000/ios-index.json
```

Once the build finishes successfully, you should get the path to your `.ipa` file.

## 6. [Distribute and install](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#6-distribute-and-install)

### on Android

If you connect an Android device to your computer, you should be able to run:

```bash
$ adb devices
emulator-5554	device
```

to list all your connected Android devices and emulators. (In my case, only the emulator is "connected".)

It is then possible to install the `.apk` file by running:

```bash
$ adb -s emulator-5554 install <PATH_TO_YOUR_APK_FILE>
Performing Streamed Install
Success
```

### on iOS

_Caveat: I could not fully test this section, so my apologies if this does not work as intended._

Since you should have Xcode, the most promising solution is in this [guide](https://codeburst.io/latest-itunes-12-7-removed-the-apps-option-how-to-install-ipa-on-the-device-3c7d4a2bc788). It also mentions other ways to install the `.ipa` file.

---

I hope this was helpful. If you have any questions or comments, please drop me a line on [Twitter](https://www.twitter.com/RobinCsl) or create an issue in this repository. I shall reply as soon as possible.

## Resources

- Expo: https://expo.io
- Turtle CLI: https://github.com/expo/turtle#readme
- Configuring `app.json`: https://docs.expo.io/versions/latest/workflow/configuration
- Expo docs to configure CI with Turtle CLI: https://docs.expo.io/versions/latest/distribution/turtle-cli
- Expo docs to host application on your own servers: https://docs.expo.io/versions/latest/distribution/hosting-your-app/
- How to create a keystore for Android: https://developer.android.com/studio/publish/app-signing#generate-key
- Supporting GitHub repository: https://github.com/RobinCsl/build-standalone-expo-app/

[1]: https://expo.io
[2]: https://github.com/expo/turtle#readme
[3]: https://developer.android.com/studio/publish/app-signing#generate-key
