# java-aes-crypto

A simple Android class for encrypting &amp; decrypting strings, aiming to avoid [serious cryptographic errors](http://tozny.com/blog/encrypting-strings-in-android-lets-make-better-mistakes/) that most such classes suffer from. [Show me the code](https://github.com/tozny/java-aes-crypto/blob/master/aes-crypto/src/main/java/com/tozny/crypto/android/AesCbcWithIntegrity.java)

# Features

Here are the features of this class. We believe that these properties are consistent with what a lot of people are looking for when encrypting Strings in Android.

- _Works for strings_: It should encrypt arbitrary strings or byte arrays. This means it needs to effectively handle multiple blocks (CBC) and partial blocks (padding). It consistently serializes and deserializes ciphertext, IVs, and key material using base64 to make it easy to store.
- _Algorithm & Mode_: We chose: AES 256, CBC, and PKCS5 padding. We would have picked GCM for its built-in integrity checking, but that's only available since Android Jelly Bean.
- _IV Handling_: We securely generate a random IV before each encryption and provide a simple class to keep the IV and ciphertext together so they're easy to keep track of and store. We set the IV and then request it back from the Cipher class for compatibility across various Android versions.
- _Key generation_: Random key generation with the updated generation code recommended for Android. If you want password-based keys, we provide functions to salt and generate them.
- _Integrity_: Lots of people think AES has integrity checking built in. The thinking goes, "if it decrypts correctly, it was generated by the person with the private key". Actually, AES CBC allows an attacker to modify the messages. Therefore, we've also added integrity checking in the form of a SHA 256 hash.

# How to include in project?

## Copy and paste

It's a single very simple java class,
[AesCbcWithIntegrity.java](https://github.com/tozny/java-aes-crypto/blob/master/aes-crypto/src/main/java/com/tozny/crypto/android/AesCbcWithIntegrity.java)
that works across most or all versions of Android. The class should be easy to
paste into an existing codebase.

## Android Library project

The library is in Android library project format so you can clone this project
and add as a library module/project.

## Maven Dependency

We've also published the library AAR file via Jitpack for simple
gradle dependency management:

Add the Jitpack repository to your root build.gradle:

```groovy
allprojects {
  repositories {
    ...
    maven { url 'https://jitpack.io' }
  }
}
```

Add the dependency to your project's build.gradle:

```groovy
dependencies {
  compile 'com.github.JarmoKukkola:java-aes-crypto:1.1.1'
}
```

# Examples

## Generate new key

```java
  AesCbcWithIntegrity.SecretKeys keys = AesCbcWithIntegrity.generateKey();
```

## Encrypt

```java
   AesCbcWithIntegrity.CipherTextIvMac cipherTextIvMac = AesCbcWithIntegrity.encrypt("some test", keys);
   //store or send to server
   String ciphertextString = cipherTextIvMac.toString();
```

## Decrypt

```java
  //Use the constructor to re-create the CipherTextIvMac class from the string:
  CipherTextIvMac cipherTextIvMac = new CipherTextIvMac (cipherTextString);
  String plainText = AesCbcWithIntegrity.decryptString(cipherTextIvMac, keys);
```

## Storing Keys

Once you've generated a random key, you naturally might want to store it. This
may work for some use cases, but please be aware that if you store the key in
the same place that you store the encrypted data, your solution is not
cryptographically sound since the attacker can just get both the key and the
encrypted text. Instead, you should use either the [Keystore
infrastructure](http://developer.android.com/training/articles/keystore.html)
or consider generating the key from a passphrase and using that to encrypt the
user data.

If despite the above you still want to store the key, you can convert the keys
to a string using the included functions and store them in preferences or
SQLite.

# License

The included MIT license is compatible with open source or commercial products.
Tozny also offers custom support and licensing terms if your organization has
different needs. Contact us at [info@tozny.com](mailto:info@tozny.com) for more
details.
