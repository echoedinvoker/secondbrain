---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Signing Your App

You must first sign your app on Google Store before they accept your app.

## Build unsigned APK

The APK generated in [[2025-03-24_Generating-the-APK|Generating the APK]] only for testing purposes, we need to use another parameter to generate the unsigned APK.

```sh
./gradlew assemble -Dorg.gradle.jvmargs="-Xmx4g -XX:MaxMetaspaceSize=512m"
#         ^^^^^^^^ `assemble` instead of `assembleNormal`

```

It'll generate the unsigned APK in
/home/matt/Projects/love-android/app/build/outputs/apk/embedRecord/release/app-embed-record-release-unsigned.apk
or
/home/matt/Projects/love-android/app/build/outputs/apk/embedNoRecord/release/app-embed-noRecord-release-unsigned.apk


## Generating KeyStore by KeyTool

```sh
 keytool -genKey -v -keystore /home/matt/release-key.keystore -alias echoedinvoker -keyalg RSA -keysize 2048 -validity 10000
#                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^                           ^^^^^^^^^^^^^^^ means the key will be valid for 10000 days
#                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^ don't forget to this alias
#                              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ name of the keystore file, you can change it to whatever you want
Enter keystore password:  
Re-enter new password:  # remember this password
```

## Signing APK

```sh
 jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore /home/matt/release-key.keystore /home/matt/Projects/love-android/app/build/outputs/apk/embedNoRecord/release/app-embed-noRecord-release-unsigned.apk echoedinvoker
#                                                  ^^^^^^^^^^^^^ alias of keystore
Enter Passphrase for keystore: # ender the keystore password you set before
```

Now, the APK `app-embed-noRecord-release-unsigned.apk` is signed, but there is final step to optimize the APK by using zipalign.

## Zipalign to Optimize APK

```sh
cd $ANDROID_HOME/build-tools/36.0.0/
./zipalign -f -v 4 /home/matt/Projects/love-android/app/build/outputs/apk/embedNoRecord/release/app-embed-noRecord-release-unsigned.apk /home/matt/lovebird-signed.apk
#                ^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ output APK, you can change it to whatever you want
#                ^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ signed APK from previous step
#                ^ align to 4 bytes(32-bit), which is typical for most apps
```

Now, the APK `lovebird-signed.apk` is ready to be uploaded to Google Store.

I know this whole process is very tedious, but you should be doing this less often because most of the process of modifying codes and testing does not require this part, only when you are ready to go live.
