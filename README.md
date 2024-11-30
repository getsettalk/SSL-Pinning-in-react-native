# SSL Pinning Certificate Generating Guide

## **Introduction**
This guide explains how to generate a SHA256 public key hash from a server certificate for SSL pinning in React Native applications. SSL pinning enhances security by ensuring your app communicates only with a trusted server.

## **Steps to Generate the Certificate Public Key Hash**

## ✅ User Linux/Unix Command to get Correct Hash base64 key [ Use GitBash ]

### 1. **Retrieve the Server Certificate**

> You can use this website to get your api domain public pin sha256 key: https://www.ssllabs.com/ssltest/index.html, view below image in (blue colord marked text is sha256 key)
> ![image](https://github.com/user-attachments/assets/3f7ac668-34d5-4967-bf73-8e8bc88f7aa5)



#### **Linux/Unix/macOS:**
```bash
openssl s_client -showcerts -servername rimeso.in -connect rimeso.in:443 < /dev/null
```

#### **Windows (PowerShell):**
```powershell
openssl s_client -showcerts -servername uatsaarthiapi.rimeso.in -connect uatsaarthiapi.rimeso.in:443
```

- Copy the first certificate from the output (Certificate Chain Index: **0**) starting from:
  ```
  -----BEGIN CERTIFICATE-----
  [Certificate Content]
  -----END CERTIFICATE-----
  ```

### 2. **Save the Certificate**
Save the copied certificate as a `.pem` file in your working directory.

#### Example for `rimeso_cert.pem`:
```plaintext
-----BEGIN CERTIFICATE-----
[Paste the certificate content here]
-----END CERTIFICATE-----
```

### 3. **Verify the Certificate (Optional)**
To check which domain the certificate belongs to:
#### **Linux/Unix/macOS:**
```bash
openssl x509 -in rimeso_cert.pem -text -noout | grep "Subject:"
```

#### **Window (Powershell):**
```powershell
openssl x509 -in rimeso_cert.pem -text -noout | FindStr "Subject:"
```
#### Example Output:
```
Subject: C=IN, ST=Haryana, L=Gurugram, O=RIMESO PRIVATE LIMITED, CN=*.rimeso.in
```

### 4. **Generate the Public Key Hash (Pin SHA256)**

#### **Linux/Unix/macOS:**
```bash
openssl x509 -in rimeso_cert.pem -pubkey -noout | openssl pkey -pubin -outform DER | openssl dgst -sha256 -binary | openssl enc -base64
```

#### **Windows (PowerShell):**
```powershell
openssl x509 -in rimeso_cert.pem -pubkey -noout | openssl pkey -pubin -outform DER | openssl dgst -sha256 -binary | openssl enc -base64
```

### 5. **Example Output**
After running the command, you’ll receive a Base64-encoded public key hash. Example:
```
DOYkqQRYoLT8deXF2pmiYhbLLJUrhp1J7thsN7m3rxA=
```

This is your **Pin SHA256** key, which will be used in your React Native app for SSL pinning.

---

## **Common Troubleshooting Steps**

### 1. **"No such file or directory" Error**
- Ensure the `.pem` file is in your current working directory.
- Use the full path to the `.pem` file if needed:
  ```bash
  openssl x509 -in /path/to/rimeso_cert.pem -pubkey -noout
  ```

### 2. **Certificate Chain Mismatch**
- Always use the **first certificate** in the chain (Index: **0**) for the correct public key hash.

---

## **Conclusion**
Follow these steps to correctly generate and verify the SSL public key hash for secure SSL pinning in your React Native application. By following this guide, you can strengthen your app’s security and prevent man-in-the-middle (MITM) attacks.

---



# 👌 How to Implement SSL Pinning in React-Native Android
Create A file name `SSLPinningFactory.java` at here `android\app\src\main\java\com\schoolapp\SSLPinningFactory.java`
in this file copy and paste this content:
and replace content : <-- your website sha256 key --> with your webiste or api public ssl key without any space

```java

package com.ummeedhousingfinance; <--- change this to your app based

import com.facebook.react.modules.network.OkHttpClientFactory;
import com.facebook.react.modules.network.OkHttpClientProvider;
import okhttp3.CertificatePinner;
import okhttp3.OkHttpClient;

public class SSLPinningFactory implements OkHttpClientFactory {
    private static String hostname = "*.rimeso.com";

    public OkHttpClient createNewNetworkModuleClient() {

        CertificatePinner certificatePinner = new CertificatePinner.Builder()
                .add(hostname, "sha256/<-- your website sha256 key -->") // this should be public key not privet key
                .build();

        OkHttpClient.Builder clientBuilder = OkHttpClientProvider.createClientBuilder();
        return clientBuilder.certificatePinner(certificatePinner).build();
    }
}

```

After that Register this file in MainApplication.java

```java

  @Override
  public void onCreate() {
    super.onCreate();



    SoLoader.init(this, /* native exopackage */ false);

    if (BuildConfig.IS_NEW_ARCHITECTURE_ENABLED) {
      // If you opted-in for the New Architecture, we load the native entry point for this app.
      DefaultNewArchitectureEntryPoint.load();
    }
    ReactNativeFlipper.initializeFlipper(this, getReactNativeHost().getReactInstanceManager());
    OkHttpClientProvider.setOkHttpClientFactory(new SSLPinningFactory()); <-- i have kept here 
  }
```


Great , now rebuild you android app and you are able to make SSL Handshake test your api request in any (Axios, Fetch)
