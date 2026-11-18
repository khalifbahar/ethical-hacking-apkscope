# APKScope

**Android Manifest Security Analyzer**

*IKB31603 Ethical Hacking | UniKL MIIT | October 2026 | Group Project*

APKScope is a Python command-line tool that performs **static analysis** of Android APK files. It reads `AndroidManifest.xml` and flags common security misconfigurations, giving each finding a severity rating. The APK is **never installed, executed or modified**.

## Table of Contents
1. [Attack Category](#attack-category)
2. [Attack Lifecycle Mapping](#attack-lifecycle-mapping)
3. [What APKScope Checks](#what-apkscope-checks)
4. [Requirements](#requirements)
5. [Installation](#installation)
6. [Usage](#usage)
7. [Output Format](#output-format)
8. [Ethical Considerations](#ethical-considerations)
9. [Rules of Engagement (RoE)](#rules-of-engagement-roe)
10. [Kill Switch and Emergency Procedures](#kill-switch-and-emergency-procedures)
11. [Responsible Disclosure](#responsible-disclosure)
12. [Legal Disclaimer](#legal-disclaimer)
13. [Limitations](#limitations)
14. [Team](#team)
15. [License](#license)

## Attack Category
Mobile Application Attack

## Attack Lifecycle Mapping
| Stage | What APKScope does |
|---|---|
| Reconnaissance | Collects package name, app name, version, permissions and SDK versions. |
| Enumeration | Identifies components (activities, services, receivers, providers) explicitly marked as exported. |
| Vulnerability Assessment | Flags insecure manifest settings (debuggable, allowBackup, cleartext traffic, risky permissions, low minSdkVersion) and assigns severity. |
| Reporting | Writes structured JSON and text reports to a local directory. |

No exploitation or dynamic analysis is performed.

## What APKScope Checks
| Check | Condition that triggers a finding | Severity |
|---|---|---|
| Debuggable flag | `android:debuggable="true"` | High |
| Backup allowed | `android:allowBackup` is not `false` (includes when it is absent) | Medium |
| Cleartext traffic | `usesCleartextTraffic="true"`, or not set and target SDK is below 28 | Medium |
| Exported components | Component has `android:exported="true"` (one finding per component) | Medium |
| High-risk permissions | App requests any of 15 sensitive permissions (camera, microphone, location, contacts, SMS, storage, phone state, call log) | Medium |
| Low minimum SDK | `minSdkVersion` is below 21 | Low |

## Requirements
- Python 3.9 or newer (Python 3.12 or 3.13 recommended)
- [androguard](https://github.com/androguard/androguard) (installed through `requirements.txt`)
- An APK file that you own or are explicitly authorized to analyze

## Installation

**Windows (Command Prompt)**
```bash
git clone https://github.com/khalifbahar/ethical-hacking-apkscope.git
cd ethical-hacking-apkscope
py -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

**Linux / Kali / macOS**
```bash
git clone https://github.com/khalifbahar/ethical-hacking-apkscope.git
cd ethical-hacking-apkscope
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

A virtual environment is used because Kali Linux protects system Python packages and blocks global `pip install`.

## Usage
```bash
# Basic usage (reports go to ./output)
python3 apkscope.py path/to/app.apk

# Custom output directory
python3 apkscope.py path/to/app.apk -o ./my_reports
```
On Windows use `python` or `py` instead of `python3`.

When started, the tool displays the legal disclaimer and asks:

```
Do you own this APK or have explicit authorization to analyze it? (yes/no):
```
Answering anything other than `yes` or `y` stops the tool before any analysis happens.

*[Insert screenshot: installation]*
*[Insert screenshot: usage and terminal output]*

## Output Format
Each run creates two files in the output directory, named with a timestamp:

- `apkscope_report_YYYYMMDD_HHMMSS.json`
- `apkscope_report_YYYYMMDD_HHMMSS.txt`

**JSON report fields:** `apk_path`, `package_name`, `app_name`, `version_name`, `version_code`, `min_sdk`, `target_sdk`, `permissions`, `high_risk_permissions`, `findings`, `total_findings`.
Each entry in `findings` contains `issue`, `severity` and `description`.

**Text report layout:**
```
APKScope Report - <package name>
Generated: <timestamp>
============================================================

Package: <package name>
App Name: <app name>
Version: <version name> (<version code>)
Min SDK: <min> | Target SDK: <target>

Total Findings: <n>

1. [<Severity>] <issue>
   <description>
```

Reports are stored locally only. The `output/` directory is excluded from version control through `.gitignore`.

*[Insert screenshot: JSON report]*
*[Insert screenshot: text report]*

## Ethical Considerations
- Static analysis only. The tool has no code that installs, launches or modifies an APK.
- The user must confirm ownership or authorization before every analysis.
- A legal disclaimer referencing the Computer Crimes Act 1997 is shown on every run.
- A kill switch (Ctrl+C) stops the tool immediately.
- Results are saved locally and are never uploaded or transmitted.

## Rules of Engagement (RoE)
- **Authorized scope:** Only APKs you own or have explicit written permission to analyze.
- **Permitted techniques:** Static analysis of the APK manifest only.
- **Prohibited actions:** Installing, executing, modifying, repackaging or exploiting the APK. Analyzing any APK without authorization. Sharing reports of third-party apps without the owner's permission.
- **Emergency stop:** Press Ctrl+C.

## Kill Switch and Emergency Procedures
Pressing **Ctrl+C** triggers a signal handler that prints `[!] Kill switch activated. Aborting cleanly...` and exits. Reports are written only after analysis finishes, so stopping during analysis produces no report.

If you realize an APK was analyzed without proper authorization:
1. Stop the tool immediately (Ctrl+C).
2. Delete the generated reports in the output directory.
3. Do not share or keep the APK or its results.
4. Inform your lecturer or team lead.

## Responsible Disclosure
If you analyze a third-party app with permission and find issues, report them privately to the developer or vendor. Do not publish findings before the owner has had reasonable time to fix them.

## Legal Disclaimer
This tool is for educational and authorized security analysis only. Analyzing APKs you do not own or have permission to inspect may violate the Computer Crimes Act 1997 (Act 563) of Malaysia and other laws. The authors accept no liability for misuse.

## Limitations
- Only manifest-level issues are checked. Code, resources, libraries and certificates are not analyzed.
- Exported component detection is basic: only components with an explicit `android:exported="true"` are reported. Intent filters and the default export behavior of older target SDKs are not considered, so results can include false negatives.
- The Network Security Configuration file is not inspected.
- XAPK containers and split APKs are not supported by the parsing library.
- The high-risk permission list is fixed (15 permissions) and does not cover every sensitive permission.
- Requires androguard.
- Intended for educational use.

## Team
| Member | Name | Student ID | Contribution |
|---|---|---|---|
| Person 1 | *[name]* | *[ID]* | Project structure, legal disclaimer, error handling and crash fixes |
| Person 2 | *[name]* | *[ID]* | Core analysis logic, permission extraction, high-risk permission list |
| Person 3 | *[name]* | *[ID]* | Kill switch (signal handler for Ctrl+C) |
| Person 4 | *MUHAMMAD FAKHRUL IZZAT BIN AZIZUL* | *52215125311* | README, report content, output file formatting |
| Person 5 | *[name]* | *[ID]* | Exported component detection, SDK version checks, practical testing |

## License
MIT License. See [LICENSE](LICENSE).


## License
MIT License

## Output
JSON and text reports saved in the output directory.
