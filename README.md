# The Hisillion TX9 Pro Max Project

### **Disclaimer:**
* **I am not responsible for any of the damages that may be inflicted to your device.** 
* **The incorrect installation of an operating system (OS) on an embedded device may render it permanently unuseable, often referred to as *"bricking."*
* **Never violate the End User License Agreement of a manufacturer's device, violations include reverse engineering the source code of properietary software and uploading it to the internetor using it for commercial purposes.**

### Preface
The Hisillicon TX9 Pro Max Project's GitHub repository is dedicated to the development of a custom build of Armbian Trixie GNU/Linux (minimal or server variant) for the use of a cheap, common TV box that is found in television (TV) box and video accessories stores across Bangladesh. Although the manufacturers claim highly performative device hardware specifications, the TV box comprises of significantly less performative device hardware specification, as given below:

<img width="589" height="601" alt="Screenshot from 2026-06-24 13-32-07" src="https://github.com/user-attachments/assets/946d4ac7-df1b-49bc-a556-c56b798f5e4e" />

I initially began developing this project to learn more about embedded Linux systems, as I aspire to work professionally with embedded and IoT systems. The project includes the important documentation of the Linux kernel, running on the Allwinner H616 System on a Chip (SoC). Allwinner H616 (sun50iw9p1) is a SoC that features a Quad-Core Cortex-A53 ARM CPU, and a Mali-G31 MP2 GPU from ARM. It is targeted for low-end TV boxes that require hardware accelerated transcoding capability for media streaming (e.g., Tanix TX9 Pro (original), Hisillicon TX9 Pro Max (counterfeit model), etcetera). [Source](linux-sunxi.org/H616)

This repository contains the full procedure of debugging, developing and instaling Armbian Trixie GNU/Linux (minimal/server) on the H616-powered machine. It even includes the mistakes that were made during the entire process. 
