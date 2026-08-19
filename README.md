# OpenVape
Easy desktop wrapper and installer for OpenVape &amp; A Opensource Version of Vape Client.

## 🚀 For Users
Go to the **[Releases](../../releases)** tab on the right side of this repository, download the latest installer setup `.exe`, run it, and launch OpenVape instantly from your desktop!

> **Credits:** Original OpenVape/Vape Client concepts and core architecture belong to their respective original owners and creators. This repository provides an independent, simplified desktop installer wrapper.

# OpenVape

A research-oriented recovery project for the Vape 4.21 client, launcher, and local service.

## Disclaimer & Copyright Notice

This project is an **educational research-oriented** reverse engineering recovery project aimed at software compatibility analysis and technical research.

- Restricted to educational, research, and self-authorized environment testing purposes only.
- Before using this project, you **must legally own a valid license** for Vape.
- Users must ensure compliance with local laws, software license agreements, and server rules.
- Project authors bear no responsibility for any violations committed by users.

## Independence Statement

This project is an independent research work conducted on a voluntary basis:

- Has not received any form of external funding, sponsorship, or commercial partnership.
- Does not represent and is not affiliated with any organization, group, or individual.
- Unrelated to any political stances, ideologies, or commercial interests.
- Project content reflects solely the author's personal technical research interests.

The project is independently initiated and maintained by an individual. All views and achievements represent technical research perspectives only and do not imply any other stance.

## Project Composition

| Repository | Description | Actions Artifacts |
| --- | --- | --- |
| [VapeLoader](https://github.com/OpenVapeCN/VapeLoader) | Rebuilt Windows launcher; responsible for local login, Minecraft process selection, injection orchestration, and loading progress display | `vape-v4-controller-windows-x64` |
| [VapeService](https://github.com/OpenVapeCN/VapeService) | Java 17 local experimental service; provides Online REST API, Zeus TCP link, and JSON data persistence | `vape421-service` |
| [VapeV4.21](https://github.com/OpenVapeCN/VapeV4.21) | Recovered Java product layer, along with Windows x64 JNI/JVMTI bridging DLL and injector | `windows-x64-injection-bundle` |

## System Requirements

- Windows 10/11 x64;
- JDK 17, required to run VapeService;
- Supported Minecraft test instance utilizing a 64-bit JVM;

## Minecraft Compatibility

| Minecraft | Vanilla | Forge | Fabric |
| --------- | :-----: | :---: | :----: |
| 1.7.10    |    ✓    |   ✓   |   -    |
| 1.8.9     |    ✓    |   ✓   |   -    |
| 1.12.2    |    ✓    |   ✓   |   -    |
| 1.16.5    |         |       |        |
| 1.21.11   |    ✓    |   ✓   |   ✓    |
| 26.2      |    ✓    |   ✓   |   ✓    |

- Minecraft 1.21.11 Forge has been verified on Forge 61.0.8, Minecraft 1.21.11 Fabric targets Fabric Loader 0.19.3, Minecraft 26.2 Forge has been verified on Forge 65.1.0, and Minecraft 26.2 Fabric is targeted at Fabric Loader 0.19.3;
- Supports injection into Lunar Client instances;
- Supports injection into Badlion Client 1.8.9;
- Other Fabric versions are outside the current scope of support;
- Minecraft 1.16.5 support is poor; some mappings, rendering, and module functions may not work properly.
- **For version 26.2, please inject after entering a server or single-player world.**

## Downloading Build Artifacts

All three repositories are built via GitHub Actions, eliminating the need to compile locally:

1. Open the Actions page of the corresponding repository;
2. Go to the latest successful workflow run;
3. Download the matching artifact from the Artifacts section at the bottom of the page;
4. Extract each and place all required runtime files into the same directory.

Quick Links:

- [VapeLoader / Windows Build](https://github.com/OpenVapeCN/VapeLoader/actions) — Download `vape-v4-controller-windows-x64`
- [VapeService / CI](https://github.com/OpenVapeCN/VapeService/actions) — Download `vape421-experimental-service-0.1.0`
- [VapeV4.21 / CI](https://github.com/OpenVapeCN/VapeV4.21/actions) — Download `windows-x64-injection-bundle`

Actions artifacts are only retained for **14 days**. If no downloadable artifacts are displayed on the page, please log in to GitHub and manually trigger a build via **Run workflow**.

Extracting `windows-x64-injection-bundle` yields two files: `Vape421Native.dll` and `Vape421Injector.exe`, which must reside in the same directory as `vape-v4-controller-windows-x64.exe`.

## Quick Start

### 1. Start Local Service

Open PowerShell in the `vape421-experimental-service-0.1.0` service directory, and run the following command to start the service (requires JDK 17):

```powershell
java -jar .\vape421-experimental-service-0.1.0.jar `
  --bind-address 127.0.0.1 `
  --http-port 8080 `
  --zeus-port 8091 `
  --data-file .\data\vape-service.json
```

If using the `start-service.cmd` script provided in this repository, it defaults to starting from `C:\Program Files\Java\jdk-17\bin\java.exe`. If your local JDK installation location differs, adjust the `JAVA_COMMAND` variable inside the script.

Keep the Service window open. Default endpoints are:

| Service | Address |
| --- | --- |
| Online HTTP API | [http://127.0.0.1:8080](http://127.0.0.1:8080) |
| Zeus TCP | `127.0.0.1:8091` |
| Local Data | `data/vape-service.json` |

### 2. Start Minecraft

Launch a supported Minecraft instance utilizing a 64-bit JVM and wait for the game window to appear. You can also launch a Lunar Client instance with Forge enabled. The Loader will list any `java.exe` or `javaw.exe` processes whose window titles include `Minecraft` or `Lunar Client`.

### 3. Load via Loader

Run `vape-v4-controller-windows-x64.exe`:

1. Enter any username and click login;
2. Select the Minecraft process from the list;
3. Wait for the Loader to indicate that loading has finished.

The current VapeService is a local experimental service: login matches solely by username and **does not verify passwords or HWIDs**. A local account is created the first time a username appears, and reused with a long-term token subsequently.

Upon successful injection, `Vape421Native.dll` will generate a `vape421-native.log` in the same directory. When troubleshooting loading issues, inspect this log and the Service console output first.

## Direct Injection Mode

`Vape421Injector.exe` can be used to inject the DLL directly.

Use the arrow keys to select a Java process and press Enter to inject; it can also be used via scripts:

```powershell
.\Vape421Injector.exe <pid> .\Vape421Native.dll
```

Online services (including configuration saving) will not function correctly when injected via this direct method.

## Frequently Asked Questions

### 1. Loader Cannot See the Game

The Loader discovers games using Java processes and visible window titles—only window titles containing `Minecraft`, `Lunar`, or `Feather` will be listed.

Please wait until the game reaches the main menu, confirm a 64-bit JVM is being used, and double-check the chosen PID, game version, and runtime environment. Supported versions include 1.7.10, 1.8.9, 1.12.2, 1.21.11, and 26.2 Vanilla, along with 1.21.11/26.2 Fabric. Do not attempt to inject launcher helper JVMs or other Java programs with similar titles.

### 2. Loader Freezes After Selecting the Game Process

If Minecraft has already been injected with Vape once, the Loader will freeze if you attempt to inject into the exact same game process a second time, regardless of whether the first injection loaded successfully.

If the initial injection fails, restart Minecraft and try again.

### 3. Loader Prompts: Vape421Native.dll was not found beside the Loader

`VapeV421Native.dll` and the Loader must be placed in the same directory.

### 4. Loader Prompts: Product DLL did not acknowledge... or Unexpected...

Vape V4 supports 1.7.10, 1.8.9, 1.12.2, 1.21.11, and 26.2 Vanilla, as well as 1.21.11/26.2 Fabric. Other Fabric versions are not currently supported.

This indicates that Vape injection failed. Check if Minecraft is a supported version, restart Minecraft, and retry injection.

If the problem persists, check `vape421-native.log` and report the issue.

### 5. Loader Shows "Finished Loading", but No Notification Appears In-Game

This means Vape injection failed. Verify that Minecraft is a supported version, restart Minecraft, and retry injection.

If the issue persists, check `vape421-native.log` and report the issue.

### 6. Injection Error Codes, Field/Method Mapping Failures, or Game Crashes

Verify that Minecraft is a supported version, restart Minecraft, and retry injection.

If the issue persists, check `vape421-native.log` and report the issue.

### 7. ClickGUI Opens But Cannot Be Clicked, Mouse Position is Wrong, or Keys Get Stuck

Open an Issue to report the problem, providing a detailed description of how it occurs.

### 8. Render Modules Cause OpenGL 1283, HUD Disappearing, Visual Glitches, or Crashes

Compatibility with Minecraft 1.16.5 and other unlisted 1.16+ versions remains poor; for 1.21.11 Forge use verified Forge 61.0.8, for 1.21.11 Fabric use Fabric Loader 0.19.3, for 26.2 Forge use verified Forge 65.1.0, and for 26.2 Fabric use Fabric Loader 0.19.3.

### 9. Configurations Disappear, Saving Fails, Friends/Party Status Abnormal

The Service utilizes a single local JSON file to save accounts and configurations; data paths are relative to the startup directory, so launching from a different directory will look like a brand new account was created. Corrupted files, lack of write permissions, full disks, running multiple Service instances simultaneously, or abnormal exits can affect saving. New Zeus connections for the same account will replace old ones, and high-frequency online statuses will not auto-recover following service restarts, system sleep, or network switches.

Always use an explicit `--data-file` path and regularly back up files to avoid two Service instances writing to the same file simultaneously. The current Service is an experimental compatibility layer; certain public configurations, comments, reports, tags, and pagination behaviors are simplified implementations, meaning a successful connection does not guarantee full feature parity with original online features.

## Building from Source

Each sub-repository contains its own independent build and verification instructions:

- [VapeLoader README](https://github.com/OpenVapeCN/VapeLoader#readme): CMake + Visual Studio 2022, Windows x64;
- [VapeService README](https://github.com/OpenVapeCN/VapeService#readme): Gradle Wrapper + JDK 17;
- [VapeV4.21 README](https://github.com/OpenVapeCN/VapeV4.21#readme): Gradle, CMake, Visual Studio 2022, and JNI/JVMTI toolchains.

## Acknowledgments

- [OpenVapeCN](https://github.com/OpenVapeCN): Project initiator, maintainer, and contributors.
- [GPT-5.6 Sol](https://chatgpt.com/): Primary contributor to the heavy lifting of the project.
- [CFR](https://github.com/leibnitz27/cfr): Assisted in generating recovered implementation code.
- [Ghidra](https://ghidra-sre.org/): Static analysis tool used for reverse engineering.
- [Steesha](https://blog.steesha.cn/): Provided the Vape main program and partial reverse engineering results.
- [John Xina](https://github.com/spec-johnxina/): Provided Vape V4.21 dump files.
- [cubk](https://github.com/cubk1/): Provided Vape V3 reverse engineering code, laying the foundation for mappings.
- [LvStrnggg](https://github.com/LvStrnggg/zkm-flow): Provided ideas for ZKM control flow reverse engineering.

## License

All code in this project is licensed under the [CC0 1.0 Universal](LICENSE) license.

You are free to:

- Freely copy, distribute, and transmit the source code and build artifacts of this project
- Freely modify, redevelop, and integrate into commercial or non-commercial projects
- Freely use for any purpose without paying fees or acquiring extra licenses
- Freely choose whether or not to attribute, or whether to release derivative works under the same license
