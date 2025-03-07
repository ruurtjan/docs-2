---
title: "Encryption"
category: "Modules"
description: "Describes the configuration and usage of the Encryption module, which is available in the Mendix Marketplace."
tags: ["marketplace", "marketplace component", "encryption", "aes", "platform support"]
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details.
---

## 1 Introduction

The [Encryption](https://marketplace.mendix.com/link/component/1011/) module takes care of the encryption of strings (for example, passwords) using AES.

### 1.1 Typical Use Cases

The typical usage scenario is when a project/module consumes a service where a user name and password are required, you can store the password in an encrpyted way in the database. The key used for encrypting passwords is configured as a constant and remains on the application server.

### 1.2 Limitations

* Encryption using AES only

## 2 Configuration

### 2.1 EncryptionKey Constant

Set the `EncryptionKey` constant located in the **Private - String en/de-cryption** folder. Make sure the key consists of 16 characters.

In version 2.2.0, the key length was increased from 128 to 256 bits. The `EncryptionKey` constant must now have a key with 32 characters. The `LegacyEncryptionKey` constant can be used for the 128 bits, in order to decrypt strings that were encrypted using an older version of the Encryption module.

### 2.2 EncryptionPrefix Constant

Set the `EncryptionPrefix` constant located in the **Private - String en/de-cryption** folder. The value depends on the module version you are using:

* For version 2.2.0 or above, set the constant to `{AES3}`
* For versions 1.4.1–2.1.3 , set the constant to `{AES2}`

{{% alert type="info" %}}
In version 1.4.1, the AES algorithm used for encrypting/decrypting text was switched from CBC to GCM mode, because CBC mode was vulnerable to Oracle padding attacks. For backward compatibility, the module still supports decrypting texts encrypted using CBC mode in older versions of the module. It does not support encrypting strings using the legacy CBC mode. So, strings encrypted in versions below 1.4.1 in CBC mode have the prefix `{AES}`, while strings encrypted in GCM mode in version 1.4.1 have the prefix `{AES2}`. If the the `EncryptionPrefix` constant is set to `{AES}`, the module in version 1.4.1 or above will still encrypt the string using a new GCM mode. Then, when decrypting the string, the module will detect the prefix `{AES}` and try to decrypt it using the legacy CBC mode, which will fail because the string was encrypted using GCM mode (which is incompatible with CBC).
{{% /alert %}}

{{% alert type="warning" %}}
If you are updating the module from a version below 1.4.1 to 1.4.1 or above (including 2.2.0 and above), do not forget to update the `EncryptionPrefix` constant value when deploying your app to the Mendix Cloud. It is also advised to re-encrypt the encrypted data by first decrypting and then encrypting it again, in order to ensure it is encrypted with the new mechanism.
{{% /alert %}}
