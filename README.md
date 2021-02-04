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
$ expo export --dev --public-url http://127.0.0.1:8000
...
Export was successful. Your exported files can be found in dist
```


Serve the `dist` directory on your web server (and make sure it's available at the same URL that you chose for the flag `--public-url` above). For example, run

```bash
$ npx http-server -p 8000 dist
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
{"name":"ExampleApplication","slug":"ExampleApplication","version":"1.0.0","orientation":"portrait","icon":"./assets/icon.png","splash":{"image":"./assets/splash.png","resizeMode":"contain","backgroundColor":"#ffffff","imageUrl":"http://127.0.0.1:8000/assets/201a91bd1740bb1d6a1dbad049310724"}...
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
  --allow-non-https-public-url \
  --public-url http://127.0.0.1:8000/android-index.json
```

Once the build finishes, take note of the location of your build `.apk` file. For example,

```bash
Feb 5 08:34:22 turtle[11626] INFO:  copied build to ~/expo-apps/@anonymous__ExampleApplication-6d7e1749c5b64bd0851525b0c7eec780-signed.apk
  platform: "android"
  buildPhase: "copying build artifact"
```


## 5. [Create IPA file -- iOS](https://www.robincussol.com/build-standalone-expo-apk-ipa-with-turtle-cli/#5-create-ipa-file----ios)


First, verify that your local server is running, e.g.

```bash
$ curl http://127.0.0.1:8000/ios-index.json
{"name":"ExampleApplication","slug":"ExampleApplication","version":"1.0.0","orientation":"portrait","icon":"./assets/icon.png","splash":{"image":"./assets/splash.png","resizeMode":"contain","backgroundColor":"#ffffff","imageUrl":"http://127.0.0.1:8000/assets/201a91bd1740bb1d6a1dbad049310724"...
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
  --provisioning-profile-path </path/to/your/provisioning/profile.mobileprovision> \
  --allow-non-https-public-url \
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
- Expo's guide to Building Standalone apps: https://docs.expo.io/distribution/building-standalone-apps/
- Configuring `app.json`: https://docs.expo.io/workflow/configuration
- Expo docs to configure CI with Turtle CLI: https://docs.expo.io/distribution/turtle-cli
- Expo docs to host application on your own servers: https://docs.expo.io/distribution/hosting-your-app/
- How to create a keystore for Android: https://developer.android.com/studio/publish/app-signing#generate-key
- Supporting GitHub repository: https://github.com/RobinCsl/build-standalone-expo-app/

[1]: https://expo.io
[2]: https://github.com/expo/turtle#readme
[3]: https://developer.android.com/studio/publish/app-signing#generate-key
