---
title: "Troubleshooting: 'This PC can't run Windows 11' (Secure Boot Error)"
category: "Troubleshooting / System Administration"
author: "Emerson Bossi"
date: 2022
status: "archived"
topics: ["Windows 11", "Secure Boot", "UEFI", "MBR2GPT"]
---

# Troubleshooting: "This PC can't run Windows 11" (Secure Boot Error)

Windows 11 installation requires that users enable Trusted Platform Module [(TPM)](https://learn.microsoft.com/en-us/windows/security/hardware-security/tpm/trusted-platform-module-overview) and [Secure Boot](https://learn.microsoft.com/en-us/windows/security/operating-system-security/system-security/trusted-boot) before installing Windows 11.

> [!NOTE]
> This guide focuses specifically on Secure Boot-related causes and does not cover TPM 2.0 configuration.

> [!WARNING]
> This was written in 2022. Instructions and information might be outdated.


---

## 1. Issue Description

If you run into the error message **"This PC doesn't meet the minimum system requirements."** when trying to update or install Windows 11, this might be a Secure Boot issue.

The error can be due to a disabled BIOS setting or an outdated partition style (**MBR**) that prevents UEFI features from working, thus blocking Windows 11 from installing properly.

---

## 2. Diagnostics: Checking System State

Before making changes, verify whether or not the issue is related to  **Secure Boot.**

1. **Click** on the **Windows Start** button.

2. Type **System Information** on the search bar.

3. Click **System Information** on the **Best match** menu.

4. Click **System Summary** at the top left of the **System Information** window.

5. Locate **Secure Boot State** on the **Item** column to the right.

6. Locate **BIOS Mode** on the **Item** column to the right.

> **Logic Check:**
> * If **Secure Boot State** is **On** and **BIOS Mode** is **UEFI**, the installation error is **not** related to Secure Boot. You should investigate TPM 2.0 or other hardware requirements.
> * If **BIOS Mode** is **Legacy**, follow the disk conversion steps before attempting to enable Secure Boot.
> * If **Secure Boot State** is **Off** and **BIOS Mode** is **UEFI**, follow the instructions below to enable it.

---

## 3. Root Cause: Legacy Partition Styles

Before enabling **Secure Boot**, you need the correct partition style on your drive.

If your **BIOS Mode** is listed as **Legacy**, your disk is likely using the **MBR (Master Boot Record)** partition style. 

Modern **Secure Boot** requires the **GPT (GUID Partition Table)** style. You must convert the disk before enabling **Secure Boot** in the BIOS.

---

## 4. MBR to GPT Partition Conversion

If **BIOS Mode** is set to **Legacy**, you need to convert your drive from **MBR** to **GPT**.

> **Warning:** Although the **mbr2gpt** tool is designed to preserve data, always **backup your files** before modifying partition structures.

1. Go to **Settings > Update & Security > Recovery**.

2. Under **Advanced startup**, click **Restart now**.

3. After reboot, navigate to **Troubleshoot > Advanced options > Command Prompt**.

4. Run the validation command: `mbr2gpt /validate`

5. If the terminal returns `Validation completed successfully`, then run the conversion: `mbr2gpt /convert`

6. Type `exit` and press **Enter** to restart the system.

## 5. Enabling Secure Boot in UEFI

Once the current drive is set to GPT, you can proceed to enabling **Secure Boot**.

> **Warning:** Enabling Secure Boot requires accessing your motherboard's BIOS/UEFI settings. Any incorrect configuration in this environment can prevent your system from booting. Do not change any settings other than those strictly mentioned in this guide unless you are an experienced user.

1. **Click** on **Windows Start** button.

2. **Click** on **Settings**.

3. Navigate to **Update & Security > Recovery**.

4. **Click Advanced Startup** under **Restart now**.

5. **Click** on **UEFI Firmware Settings.**

6. Select **Restart**.

Your system should restart and open the BIOS. Once inside the BIOS:

1. Navigate to the **Security** or **Boot** tab.

2. Navigate to the **Secure Boot** option and press **Enter.**

3. Select **Enabled** and press **Enter**.

4. Navigate to the **Exit** tab.

5. Select **Save Changes and Exit** to reboot.

> [!TIP]
> You can also manually access the BIOS by pressing the BIOS key when booting up (e.g., **Delete**, **Esc**, **F10**, or **F12**). You can check your motherboard manual for the corresponding key if necessary.

## 6. Verification

After the system reboots, repeat the diagnostic steps from [2. Diagnostics: Checking System State](#2-diagnostics-checking-system-state) to check if the information now matches the requirements.

| Requirement | Expected Value |
| :--- | :--- |
| **BIOS Mode** | UEFI |
| **Secure Boot State** | On |

If it does, then the system is fully configured to support Windows 11 installation.