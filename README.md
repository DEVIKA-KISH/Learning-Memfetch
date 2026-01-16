# 🧠 Learning-Memfetch: Android Process Memory Acquisition

![Platform](https://img.shields.io/badge/Platform-Android15-blue)
![Tool](https://img.shields.io/badge/Tool-Memfetch-orange)
![Focus](https://img.shields.io/badge/Focus-Mobile_Forensics-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)



---

## 🎯 Objective

Perform **live process memory acquisition** on a **rooted Android 15 (OnePlus 13R)** device using **Memfetch**, targeting a **medical IoT mobile app (EMAY Pulse Oximeter)**.
This lab demonstrates how sensitive health data remains in memory (heap, stack, static) even after app use — revealing forensic visibility of **data remanence** in Android devices.

---

## 🧰 Tools & Environment

| Tool / Device                              | Purpose                                    |
| ------------------------------------------ | ------------------------------------------ |
| **OnePlus 13R (Android 15, Kernel 6.1.x)** | Test device (rooted)                       |
| **Memfetch (ARM64 build)**                 | Process memory acquisition utility         |
| **ADB (Android Debug Bridge)**             | File transfer & shell access               |
| **EMAY Pulse Oximeter App**                | Target process: `com.jack.emaybloodoxygen` |
| **Linux Host PC**                          | Analysis & storage of dumps                |

---

## ⚙️ Step 1 — Setup & Deployment

1. Push Memfetch binary to device

```
adb push memfetch_oneplus /data/local/tmp/memfetch_oneplus
```

2. Switch to root shell

```
adb shell
su
```

3. Create working directory

```
mkdir -p /data/local/tmp/emay_plaintext_dk
cd /data/local/tmp/emay_plaintext_dk
```

---

## 🔎 Step 2 — Identify Target Process

Locate the EMAY Pulse Oximeter process:

```
ps -A | grep emay
```

Example output:

```
24441   1633  8873448  248780  … com.jack.emaybloodoxygen
```

![Identifying Target PID](screenshots/2.png)
*Fig-2.1 — EMAY process identified via `ps` command.*

---

## 💾 Step 3 — Execute Memfetch

Attach Memfetch to the process and start dumping:

```
../memfetch_oneplus 24441
```

Memfetch uses ptrace-like read operations to capture live pages from the app process.

![Memfetch Execution](screenshots/3.png)
*Fig-3.1 — Memfetch attached to EMAY app and began page capture.*

---

## 📈 Step 4 — Acquisition Progress

Memory regions (stack, heap, .data) are dumped into files such as `mem-000.bin`, `map-000.bin`, and an index file `mfetch.lst`.

![Memory Dump Progress](screenshots/4.png)
*Fig-4.1 — Dump in progress; over 3,500 regions successfully captured.*

---

## 📂 Step 5 — Verify Dump Directory

List all created dump files:

```
ls -la /data/local/tmp/emay_plaintext_dk
```

Expect:

* Hundreds of `mem-###.bin` and `map-###.bin`
* Index file `mfetch.lst`

![Dump Directory Contents](screenshots/5.png)
*Fig-5.1 — Memory dump directory with hundreds of binary segments.*

---

## 🧾 Step 6 — Validate Dump Completion

Navigate to `/data/local/tmp` and confirm your directories:

```
ls -la /data/local/tmp
```

Contains multiple acquisition sessions:

* `beurer_login_devika/`
* `emay_plaintext_dk/`
* `emay_sha512_dk/`

![Top-Level Directory](screenshots/6.png)
*Fig-6.1 — Directory structure showing completed EMAY memory captures.*

---

## 💻 Step 7 — Pull Dump for Analysis

Transfer dumps to host machine:

```
adb pull /data/local/tmp/emay_plaintext_dk ./emay_plaintext_dk
```

Extract readable strings:

```
strings mem-*.bin | grep -a "SpO2"
strings mem-*.bin | grep -a "PR"
```

Expected: health telemetry fragments such as **SpO₂**, **PR(bpm)**, and timestamps.

![EMAY App UI](screenshots/1.png)
*Fig-1.1 — EMAY Pulse Oximeter app displaying recorded health metrics before acquisition.*

---

## 📦 Step 8 — Artifacts & Outputs

| Artifact             | Description                       |
| -------------------- | --------------------------------- |
| `mem-*.bin`          | Raw memory dumps                  |
| `map-*.bin`          | Virtual memory maps               |
| `mfetch.lst`         | Memory region index               |
| `emay_plaintext_dk/` | Full plaintext capture directory  |
| `emay_sha512_dk/`    | Secondary encrypted / hashed dump |

**Completion message:**

```
Done (3569 matching). Have a nice day.
```

✅ Successful acquisition confirmed.

---

## 🧠 Key Learnings

| Concept                   | Explanation                                                        |
| ------------------------- | ------------------------------------------------------------------ |
| Memory Forensics          | Gained hands-on experience with Android volatile memory extraction |
| Process Segmentation      | Differentiated between .data, heap, and stack storage              |
| Data Persistence          | Verified residual plaintext in app memory buffers                  |
| Forensic Chain of Custody | Documented tools, process, and results for reproducibility         |

---

## 🔐 Security & Privacy Implications

This experiment reveals that **health data from IoT medical apps** may persist in RAM and remain recoverable after use.

**Recommendations:**

1. Zeroize memory after use (`memset_s()`, `explicit_bzero()`).
2. Encrypt in-RAM structures for transient health data.
3. Use `mlock()` to prevent swapping sensitive pages.
4. Adopt hardware key storage for cryptographic operations.
5. Perform forensic reviews to confirm no plaintext data remains in memory.

---

## 🔗 Related Learning Labs

| Repository                                                                                                    | Focus                                     |
| ------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| [Learning-Nmap](https://github.com/DEVIKA-KISH/Learning-Nmap)                                                 | Network scanning & port enumeration       |
| [Learning-WebApp-Security-BurpSuite](https://github.com/DEVIKA-KISH/Learning-WebApp-Security-BurpSuite)       | Web application vulnerability testing     |
| [Learning-Malware-Analysis-with-Ghidra](https://github.com/DEVIKA-KISH/Learning-Malware-Analysis-with-Ghidra) | Static malware reverse engineering        |
| **Learning-Memfetch (this repo)**                                                                             | Android memory forensics & data remanence |

---

## 🧩 Summary

Demonstrates mobile memory forensics, IoT data acquisition, and forensic investigation of process memory in a live Android 15 environment — hands-on learning in reverse engineering, digital forensics, and privacy-focused analysis.

---

## 👩‍💻 Author

**Devika Kishor**
Master’s Student in Cybersecurity | Florida Institute of Technology


---


Would you like me to give you the next **Ghidra malware-analysis `README`** in the same clean, copy-paste style (no Markdown rendering issues, screenshots auto-linked)?
