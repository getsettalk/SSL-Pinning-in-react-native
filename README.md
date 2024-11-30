# SSL Pinning Certificate Generating Guide

## **Introduction**
This guide explains how to generate a SHA256 public key hash from a server certificate for SSL pinning in React Native applications. SSL pinning enhances security by ensuring your app communicates only with a trusted server.

## **Steps to Generate the Certificate Public Key Hash**

### 1. **Retrieve the Server Certificate**

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
```bash
openssl x509 -in rimeso_cert.pem -text -noout | grep "Subject:"
```

#### Example Output:
```
Subject: C=IN, ST=Haryana, L=Gurugram, O=UMMEED HOUSING FINANCE PRIVATE LIMITED, CN=*.rimeso.in
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
