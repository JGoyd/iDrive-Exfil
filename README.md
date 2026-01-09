# iDrive Exfil



**Forensic Status:** Active Compromise / Data Exfiltration Confirmed

## **Summary**

A forensic examination of the device and associated image files revealed a **Low-Level Persistence (LLP)** exploit utilizing **iCloud Drive** as the primary infection and synchronization vector. An unauthorized "iDrive Exfil" has been operating beneath the visible userland, bridging secure data (Wallet/Identity) into a steganographic carrier for exfiltration via the cloud synchronization bus.

## **The Carrier (Malicious Steganography)**

**File Name:** `IMG_1553.HEIC` [VirusTotal](https://www.virustotal.com/gui/file/2035b6d7b884bf1f72acfafa835d2f243b9ac4b16d4d6c70ceee5f8378fefad1)

**File Type:** Polyglot (HEIF Image + Encrypted Payload)

* **Interception and Forgery**: The attacker intercepted a legitimate personal photo and modified it into a "Trojan" carrier.
* **Hardware Spoofing**: The image metadata (XMP) falsely claims the capture was made by an **iPhone 14 Pro Max** (2.22mm lens, ƒ/1.78).
* **Entropy Analysis**: The `mdat` section (Media Data) returned an entropy of **7.9478**.
* **Significance**: High entropy (**>7.9**) in an image's pixel data is a definitive indicator of an **encrypted binary payload**.


 **MakerNote Injection**: The file's proprietary metadata was "signed" by the malicious kernel using **Shadow UUIDs** found in the unauthorized network extension cache:
- `G70791C74-F0BE-4231-9907-EFEEE6173F41`
- `8B7870F5-0E6C-4E58-887A-293CCB3FDD5D`
-  `D188973E-48C6-424D-B6EB-4DBE5FB50859`



## **Infrastructure and C2 (Command & Control)**

The following Indicators of Compromise (IoCs) represent the **active** attacker infrastructure identified within the carrier's binary "neighborhood":

| Cluster Type | Network Range / ISP | Forensic Role |
| --- | --- | --- |
| **Obfuscation** | `104.16.0.0/12` (Cloudflare) | Used to mask C2 traffic as standard HTTPS CDN traffic. |
| **Relay** | `45.46.0.0/15` (Charter/Spectrum) | **Live** domestic US ISP range used as a high-reputation proxy. |
| **Dead Drop** | `23.29.39.32/28` (Everfast Fiber) | **Active** assignment at a medical entity used for final payload receipt. |

## **Data Loss Assessment**

* **Target Data**: Secure Element tokens (Apple Pay), Identity Documents (Driver's License), and digital keys.
* **Method (iCloud Synchronization)**: The infection leverages **iCloud Drive** to facilitate the movement of data. The `passd` daemon (Wallet) was bridged to the shadow kernel, dumping data into the `U{13}s` directory before encapsulation in `IMG_1553.HEIC` and subsequent cloud synchronization to the C2 endpoints.

