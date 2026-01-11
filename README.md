# iDrive Exfil

## **Summary**

A forensic examination of the device and associated image files revealed a **Low-Level Persistence (LLP)** exploit utilizing **iCloud Drive** as the primary infection and synchronization vector. An unauthorized "iDrive Exfil" has been operating beneath the visible userland, bridging secure data (Wallet/Identity) into a steganographic carrier for exfiltration via the cloud synchronization bus.

## **The Carrier (Malicious Steganography)**

**File Name:** `IMG_1553.HEIC` [VirusTotal Analysis (Verified Hash)](https://www.virustotal.com/gui/file/2035b6d7b884bf1f72acfafa835d2f243b9ac4b16d4d6c70ceee5f8378fefad1)

**File Type:** Polyglot (HEIF Image + Encrypted Payload)

* **Interception and Forgery**: The attacker intercepted a legitimate personal photo and modified it into a "Trojan" carrier.
* **Hardware Spoofing**: The image metadata (XMP) falsely claims the capture was made by an **iPhone 14 Pro Max** (2.22mm lens, ƒ/1.78).
* **Entropy Analysis**: The `mdat` section (Media Data) returned an entropy of **7.9478**.
* **Significance**: High entropy in an image's pixel data is a definitive indicator of an **encrypted binary payload**.


 **MakerNote Injection**: The file's proprietary metadata was "signed" by the malicious kernel using **Shadow UUIDs** found in the unauthorized network extension cache:
- `G70791C74-F0BE-4231-9907-EFEEE6173F41`
- `8B7870F5-0E6C-4E58-887A-293CCB3FDD5D`
-  `D188973E-48C6-424D-B6EB-4DBE5FB50859`



## **Infrastructure and C2 (Command & Control)**

The following Indicators of Compromise (IoCs) represent the **active** attacker infrastructure identified within the carrier's binary "neighborhood". 
*ISP attribution is preliminary, further investgation is ongoing 


| Cluster Type | Network Range / ISP | Forensic Role | Specific IP Indicators |
|--------------|---------------------|---------------|-----------------------|
| Obfuscation | 104.16.0.0/12 (Cloudflare) | Masking C2 traffic as standard HTTPS CDN traffic | 104.16.1.19 |
| Relay | 45.47.0.0/15 (Charter/Spectrum) | High-reputation domestic proxy nodes | 45.47.56.61, 45.48.69.54, 45.50.57.51 |
| Relay | 45.52.0.0/16 (Frontier) | High-persistence domestic proxy nodes | 45.52.50.51, 45.52.69.53, 45.52.56.67, 45.52.50.52, 45.52.68.66 |
| Relay | 45.56.0.0/18 (Google Fiber) | High-bandwidth residential fiber relay | 45.56.56.55 |
| Relay | 45.57.0.0/16 (Akamai) | Infrastructure-level CDN edge relay | 45.57.57.48, 45.70.48.66 |
| Relay | 45.69.0.0/16 (Cogent) | Tier-1 transit backbone relay | 45.69.70.69 |
| Relay | 45.66.0.0/16 (DigitalOcean) | Virtualized cloud infrastructure relay | 45.66.54.69 |
| Dead Drop | 23.29.39.32/28 (Everfast Fiber) | Active medical entity assignment; final payload receipt | 23.29.39.45 |

## **Data Loss Assessment**

* **Target Data**: Secure Element tokens (Apple Pay), Identity Documents (Driver's License), and digital keys.
* **Method (iCloud Synchronization)**: The infection leverages **iCloud Drive** to facilitate the movement of data. The `passd` daemon (Wallet) was bridged to the shadow kernel, dumping data into the `U{13}s` directory before encapsulation in `IMG_1553.HEIC` and subsequent cloud synchronization to the C2 endpoints.

