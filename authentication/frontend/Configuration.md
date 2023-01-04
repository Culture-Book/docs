# Android Configuration

> Application configuration for the android application

The android application requires several configuration in order to work. The configuration of the Android app includes several methods, including environment variables and json configuration files.

> Set the following environment variables before launching the app.

```bash

# Environment variables

# CI/CD - required for the correct versioning of the app
GITHUB_RUN_NUMBER

# Fastlane support - required for ci/cd
FIREBASE_APP_ID
FIREBASE_TOKEN
APP_CENTER_TOKEN
APP_CENTER_OWNER
APP_CENTER_APP_NAME_DEBUG
ABB_FILE_PATH_DEBUG

# Pre_release variant - required for pre_release builds
PRE_RELEASE_ALIAS
PRE_RELEASE_PASS

# Release variant - required for release builds
RELEASE_ALIAS
RELEASE_PASS

# Json files - required for all builds
app/google-services.json #(From firebase)

```

## General configuration

> General configuration comments

In the ```network_security_config.xml``` file the subdomains `192.168.0.10` is the local IP address of the computer that runs the local server. The `10.0.2.2` address is the default address to connect to `localhost` from an emulator.

> API url configuration

The api URL is found in the `data` gradle file.

## Proguard rules configuration

> Required for kotlinx.serialization

Kotlinx serialization uses the data classes variable names to serialize and deserialize json objects, as such, proguard messes up the serialization when the app is obfuscated. Add the following rules to the ```proguard-rules.pro``` of the ```app``` and the ```core:data``` modules.

```bash
# If you keep the line number information, uncomment this to
# hide the original source file name.
#-renamesourcefileattribute SourceFile
# Keep `Companion` object fields of serializable classes.
# This avoids serializer lookup through `getDeclaredClasses` as done for named companion objects.
-if @kotlinx.serialization.Serializable class **
-keepclassmembers class <1> {
    static <1>$Companion Companion;
}

# Keep `serializer()` on companion objects (both default and named) of serializable classes.
-if @kotlinx.serialization.Serializable class ** {
    static **$* *;
}
-keepclassmembers class <2>$<3> {
    kotlinx.serialization.KSerializer serializer(...);
}

# Keep `INSTANCE.serializer()` of serializable objects.
-if @kotlinx.serialization.Serializable class ** {
    public static ** INSTANCE;
}
-keepclassmembers class <1> {
    public static <1> INSTANCE;
    kotlinx.serialization.KSerializer serializer(...);
}

# @Serializable and @Polymorphic are used at runtime for polymorphic serialization.
-keepattributes RuntimeVisibleAnnotations,AnnotationDefault

# Serializer for classes with named companion objects are retrieved using `getDeclaredClasses`.
# If you have any, uncomment and replace classes with those containing named companion objects.
#-keepattributes InnerClasses # Needed for `getDeclaredClasses`.
#-if @kotlinx.serialization.Serializable class
#com.example.myapplication.HasNamedCompanion, # <-- List serializable classes with named companions.
#com.example.myapplication.HasNamedCompanion2
#{
#    static **$* *;
#}
#-keepnames class <1>$$serializer { # -keepnames suffices; class is kept when serializer() is kept.
#    static <1>$$serializer INSTANCE;
#}
```

## Dependencies

### Data

- Asynchronous library: `org.jetbrains.kotlinx:kotlinx-coroutines-android`
- Content Negotiation: `org.jetbrains.kotlinx:kotlinx-serialization-json`

- Firebase

  - `com.google.firebase:firebase-config-ktx`
  - `com.google.firebase:firebase-analytics-ktx`
  - `com.google.firebase:firebase-crashlytics`

- Retrofit

  - `com.squareup.okhttp3:okhttp-urlconnection:5.0.0-alpha.11`
  - `com.squareup.retrofit2:retrofit:2.9.0`
  - `com.jakewharton.retrofit:retrofit2-kotlinx-serialization-converter:0.8.0`
  - `com.squareup.okhttp3:logging-interceptor:5.0.0-alpha.11`

- SharedPreferences (encrypted)

  - `androidx.security:security-crypto:1.0.0`
  - `androidx.security:security-identity-credential:1.0.0-alpha03`
  - `androidx.security:security-app-authenticator:1.0.0-alpha02`

- Android ViewModel (for compose): `androidx.lifecycle:lifecycle-viewmodel-compose:2.5.1`

### Navigation

- Jetpack Compose navigation `androidx.navigation:navigation-compose`

### UI

- Jetpack Compose: `androidx.compose.ui:ui`
- Material3 (for jetpack compose): `androidx.compose.material3:material3`
- System UI controller (Accompanist): `com.google.accompanist:accompanist-systemuicontroller`
