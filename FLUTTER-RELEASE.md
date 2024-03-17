# Release A Flutter App on Android

### 1. Adding a launcher Icon

 - Create a launcher Icon (.png , 512px-512px) for your app
 - Create a `assets` folder in your project, place the icon in your `assets/icon` folder
 - now go to your `pubspec.yaml` file and add these code under the `dev_dependencies` section

```yaml

flutter_launcher_icons:
  android: true
  ios: true
  image_path: "assets/icon/logo.png"
  min_sdk_android: 21 # android min sdk min:16, default 21
  web:
    generate: true
    image_path: "path/to/image.png"
    background_color: "#hexcode"
    theme_color: "#hexcode"
  windows:
    generate: true
    image_path: "path/to/image.png"
    icon_size: 48 # min:48, max:256, default: 48
  macos:
    generate: true
    image_path: "path/to/image.png"
  # The "flutter_lints" package below contains a set of recommended lints to
  # encourage good coding practices. The lint set provided by the package is
  # activated in the `analysis_options.yaml` file located at the root of your
  # package. See that file for information about deactivating specific lint
  # rules and activating additional ones.
  flutter_lints: ^2.0.0
```
 - Make sure the alignment is correct
 - Run your app again to check in the emulator if your icon is changed

### 2. Rename your app to what you want user to see as

 - Run this in your terminal to add the `rename` dependency
  ```bash
  flutter pub global activate rename
  ```
 - Run this to check if it was added successfully
  ```bash
  rename help
  ```
 - If it says `rename command not found` add this path `C:\Users\%YOUR USER NAME%\AppData\Local\Pub\Cache\bin` to your machines environment variable paths and restart your machine
 - Run this to change your app name
  ```bash
  rename setAppName --targets ios,android --value "YourAppName"
  ```
 - The BundleID needs to be a unique ID for any app in the app store, generally recommended to use your domain name backward and add project name after it
 - Run this to change your bundleID
  ```bash
  rename setBundleId --targets android --value "com.example.bundleId"
  ```

### 3. Sign Your App

  - Create a `key.properties` file inside `android` folder
  - Pase this code inside your `key.properties` file
  ```properties
    storePassword=YOUR_PASSWORD
    keyPassword=YOUR_PASSWORD
    keyAlias=upload
    storeFile=../app/upload-keystore.jks
  ```
  - Run this in your terminal to generate keystore file
  ```bash
  keytool -genkey -v -keystore .\upload-keystore.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias upload
  ```
  - If you see that `keytool command not found` Run this in your terminal
  ```bash
    flutter doctor -v
  ```
  - Under the `Android toolchain` section you will see a path named `Java binary at` that looks something like this `C:\Program Files\Android\Android Studio\jbr\bin\java`

  - Copy the path except for the last `\java` part and add this to your machines environment variable paths
  - Restart your pc and try generating the keystore file again
  - Place this file in your `android\app` folder

### 4. Configure Signing in gradle
  - Go to your `android\app\build.gradle` file
  - Find the android block
  ```gradle
    android {
      namespace ...
      ...
      ...
    }
  ```
  - Paste this code before the android section
  ```gradle
    def keystoreProperties = new Properties()
    def keystorePropertiesFile = rootProject.file('key.properties')
    if (keystorePropertiesFile.exists()) {
      keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
    }
  ```
  - Find and remove the buildTypes block
  ```gradle
  buildTypes {
    release {
      // TODO: Add your own signing config for the release build.
      // Signing with the debug keys for now,
      // so `flutter run --release` works.
      signingConfig signingConfigs.debug
    }
  }
  ```
  - Place this code in the place where buildTypes block was
  ```gradle
  signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
  }
  buildTypes {
    release {
      signingConfig signingConfigs.release
    }
  }
  ```

### 5. Building the app
  - Before creating the release bundle go to your `pubspec.yaml` file and update the app version, otherwise you won't be able to upload the new bundle to the app store
  - Run this to clean if you have any previous build
  ```bash
    flutter clean
  ```
  - Run this to build your app
  ```bash
    flutter build appbundle
  ```
  - This will crate a `app-release.aab` file in our `build\outputs\bundle\release` folder
  - This is the file that you will need to upload to app store