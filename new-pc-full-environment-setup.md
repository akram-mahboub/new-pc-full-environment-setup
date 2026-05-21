# 🖥️ New PC — Full Dev Environment Setup

> Complete environment setup for the **Tabib** project on any new PC.
> Covers: Git, Node.js, Python, VS Code + extensions, Flutter, Android Studio, emulators.
>
> **Tabib stack:** React/Vite/TS web · Node/Express/TS backend · Python/FastAPI AI service · Flutter mobile.

---

## 📋 Master Checklist

| # | Tool | Version | Purpose |
|---|------|---------|---------|
| 1 | Git | latest | Version control + Flutter prerequisite |
| 2 | nvm-windows | latest | Manage Node.js versions |
| 3 | Node.js | 22.11.0 LTS | Backend (Express/TS) + Web (Vite/React) |
| 4 | Python | 3.11.15 | AI service (FastAPI + transformers) |
| 5 | JDK | 17 (Temurin) | Flutter Android builds |
| 6 | VS Code | latest | Main code editor |
| 7 | Flutter SDK | 3.44.0 (or 3.24.x) | Mobile app |
| 8 | Android Studio | latest | Android SDK + emulator |
| 9 | Emulator / device | — | Run the app (AVD, real phone, etc.) |
| 10 | Docker Desktop | latest | Deployment (optional, install later) |

**Disk budget:** ~8 GB (tools + Android SDK).

---

## 🧹 Step 0 — Cleanup (if reusing an old PC / old drive)

To avoid version conflicts:

1. Windows **Settings → Apps → Installed apps**
2. Uninstall any **old** Python, Node.js, Java/JDK.
3. You'll reinstall clean versions below.
4. Keep project repos / SDK folders on `D:` — those are fine to reuse.

> **Drive note:** Runtimes/IDEs → install on `C:`.
> SDK folders, projects, datasets, models → can live on `D:`.
> Environment variables work across drives — just point them at the real path.

---

## 1️⃣ Git

- **Download:** https://git-scm.com/download/win
- Install with default options.

Configure:
```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global core.autocrlf true
git config --global init.defaultBranch main
```

Verify: `git --version`

---

## 2️⃣ nvm-windows (Node Version Manager)

Lets you install and switch between Node.js versions.

- **Download:** https://github.com/coreybutler/nvm-windows/releases
- Download **`nvm-setup.exe`** from the latest release.
- Install with defaults.

---

## 3️⃣ Node.js 22.11.0 LTS

Tabib `package-lock.json` requires `node: ">=18"` — Node 22 LTS is the best pick.

After installing nvm-windows, open a **new** PowerShell:

```powershell
nvm install 22.11.0
nvm use 22.11.0
node --version      # v22.11.0
npm --version       # 10.x
```

- **Direct installer (alternative to nvm):** https://nodejs.org/dist/v22.11.0/node-v22.11.0-x64.msi
- **Node download page:** https://nodejs.org/en/download

### Global npm packages (optional)

```powershell
npm install -g pnpm        # faster package manager
npm install -g typescript  # tsc CLI
npm install -g tsx         # run TS files directly
npm install -g nodemon     # auto-restart backend
```

---

## 4️⃣ Python 3.11.15

Tabib AI service needs `torch`, `transformers`, `sentence-transformers`,
`faiss-cpu`, `fastapi`. **Python 3.11** is the safest (best FAISS/torch
wheel support; faster than 3.10; avoids 3.12/3.13 compatibility issues).

- **Download page:** https://www.python.org/downloads/release/python-31115/
- Scroll to **Files** → **Windows installer (64-bit)**.

⚠️ **During install — CRITICAL:**
- ✅ Check **"Add python.exe to PATH"** (bottom of first screen)
- ✅ Check **"Install pip"**

Verify:
```powershell
python --version    # Python 3.11.15
pip --version
```

### AI service dependencies

```powershell
cd D:\path\to\darija_triage
python -m venv .venv
.venv\Scripts\activate
pip install --upgrade pip
pip install -r requirements.txt
```

> **GPU (NVIDIA) users:** install PyTorch with CUDA instead of plain torch:
> ```powershell
> pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
> ```

---

## 5️⃣ JDK 17 (Temurin)

Required for Flutter's Android Gradle builds.

- **Download:** https://adoptium.net/temurin/releases/?version=17
- Select: **Windows** / **x64** / **JDK** / **17 - LTS**
- During install, check ✅ **"Set JAVA_HOME variable"**.

Verify: `java -version`

---

## 6️⃣ VS Code

- **Download:** https://code.visualstudio.com/

### Required Extensions

Open VS Code → **Extensions** panel (left sidebar) → install each:

#### Flutter / Dart (mobile)
| Extension | Publisher | Purpose |
|-----------|-----------|---------|
| **Dart** | Dart Code | Dart language support |
| **Flutter** | Dart Code | Flutter tooling, run/debug, devices |

#### JavaScript / TypeScript (web + backend)
| Extension | Publisher | Purpose |
|-----------|-----------|---------|
| **ESLint** | Microsoft | JS/TS linting |
| **Prettier - Code formatter** | Prettier | Auto-formatting |
| **Tailwind CSS IntelliSense** | Tailwind Labs | Tailwind class autocomplete |
| **ES7+ React/Redux snippets** | dsznajder | React snippets |

#### Python (AI service)
| Extension | Publisher | Purpose |
|-----------|-----------|---------|
| **Python** | Microsoft | Python language support |
| **Pylance** | Microsoft | Fast IntelliSense / type checking |
| **Jupyter** | Microsoft | Notebooks for AI experiments |

#### General / Productivity
| Extension | Publisher | Purpose |
|-----------|-----------|---------|
| **GitLens** | GitKraken | Git history & blame |
| **Error Lens** | Alexander | Inline error display |
| **Thunder Client** | Thunder Client | Test REST APIs in-editor |
| **DotENV** | mikestead | `.env` syntax highlighting |
| **Material Icon Theme** | Philipp Kief | File icons |
| **Better Comments** | Aaron Bond | Color-coded comments |
| **PostgreSQL** | Weijan Chen | Query the Supabase DB |

> **Tip:** Sign in to VS Code with GitHub and enable **Settings Sync** —
> next new PC pulls all extensions + settings automatically.

---

## 7️⃣ Flutter SDK

The Flutter SDK is the framework for the Tabib mobile app. It is just a
folder of files — there is no traditional installer.

**Download links:**
- Install page (Windows): https://docs.flutter.dev/install/windows
- Manual install instructions: https://docs.flutter.dev/install/manual
- All versions archive (stable / beta): https://docs.flutter.dev/install/archive

**Setup steps:**

1. Download the Flutter SDK `.zip` from the install page.
2. Create a clean folder — **no spaces, no special characters, no admin folders**:
   - ✅ Good: `C:\src\flutter` or `D:\src\flutter`
   - ❌ Bad: `C:\Program Files\flutter` (needs admin rights, has a space —
     Flutter auto-updates itself and will fail here)
3. Extract the zip into that folder. You should end up with:
   ```
   D:\src\flutter\bin\flutter.bat
   ```
4. Add Flutter to **PATH**:
   - Press `Win` → search **"Edit the system environment variables"** → open it
   - Click **Environment Variables**
   - Under **User variables**, select **`Path`** → **Edit** → **New**
   - Add exactly the `bin` folder: `D:\src\flutter\bin`
   - Click **OK** on every window
5. **Close all open terminals**, then open a fresh PowerShell.

**Verify:**
```powershell
flutter --version
```

You should see the Flutter version, channel (stable), and the bundled Dart
version. The Dart SDK comes inside Flutter — you do **not** install Dart
separately.

**About versions:** Tabib's `pubspec.yaml` requires `flutter: '>=3.24.0'`
with no upper limit, so the latest stable (3.44.0) works fine — install it
globally and you're done.

**FVM (Flutter Version Management) — optional, probably not needed yet.**
FVM lets you install several Flutter versions at once and pin each project
to a specific one (via a `.fvmrc` file in the project). It solves the
problem where `flutter upgrade` changes the version for *every* project on
your PC and can break older ones. For Tabib right now — a single project
with a permissive version constraint — a plain global install is simpler.
Reach for FVM only if you (a) add a second Flutter project needing a
different version, (b) work in a team that must share an identical version,
or (c) want to test an upgrade without disturbing your working setup.

If/when you do want it:
```powershell
dart pub global activate fvm
cd D:\path\to\tabib_mobile
fvm install 3.44.0
fvm use 3.44.0
```
FVM website: https://fvm.app

---

## 8️⃣ Android Studio + Android SDK

Android Studio is the official Android IDE. Even though you'll write code in
VS Code, Android Studio is still needed because it installs and manages the
**Android SDK**, **platform tools**, and the **emulator** that Flutter
depends on for Android builds.

**Download:** https://developer.android.com/studio
**Install guide:** https://developer.android.com/studio/install

**Setup steps:**

1. Run the installer and launch Android Studio.
2. On first launch the **Setup Wizard** runs — choose **Standard** install.
   It will download the Android SDK, an SDK platform, build tools, and the
   emulator. This download is large (~3–5 GB) and is the slowest part of the
   whole setup.
3. To keep your `C:` drive free, you can change the SDK location to
   `D:\Android\Sdk` (Settings → Languages & Frameworks → Android SDK).

**Required SDK components** — in Android Studio go to
**Settings → Languages & Frameworks → Android SDK** and make sure these are
ticked:

- ✅ Android SDK Platform 35 (and/or 34)
- ✅ Android SDK Build-Tools
- ✅ Android SDK Command-line Tools
- ✅ Android SDK Platform-Tools
- ✅ Android Emulator

**Set environment variables** (Win → "Edit the system environment variables"):
```
ANDROID_HOME      = D:\Android\Sdk
ANDROID_SDK_ROOT  = D:\Android\Sdk
```
Add these to **Path**:
```
%ANDROID_HOME%\platform-tools
%ANDROID_HOME%\emulator
%ANDROID_HOME%\cmdline-tools\latest\bin
```

**Connect Flutter to the Android toolchain:**
```powershell
flutter doctor --android-licenses    # type 'y' to accept each license
flutter doctor -v                     # confirm the Android toolchain is green
```

---

## 9️⃣ Running the App — Emulator Options

To run the Tabib mobile app you need somewhere for it to run. You have
several options — pick whichever fits your hardware.

### Option A — Android Studio Emulator (AVD) — the default

This is the standard choice and works well for most people.

1. In Android Studio, open **Device Manager** (Tools menu or right sidebar).
2. Click **Create Device**.
3. Pick a phone profile — **Pixel 7** or **Pixel 8** are good choices.
4. Pick a system image — **API 34 (Android 14)** or **API 35**. Download it
   if prompted.
5. Click **Finish**, then press ▶ to launch the virtual phone.

Useful commands:
```powershell
flutter devices                   # list connected devices/emulators
flutter emulators                  # list available emulators
flutter emulators --launch <id>    # launch a specific one
adb devices                        # confirm ADB sees the device
```

**Speed matters:** the emulator is much faster with hardware acceleration.
On Windows, enable **Windows Hypervisor Platform**:
Control Panel → Programs → Turn Windows features on or off → tick
**Windows Hypervisor Platform** and **Virtual Machine Platform** → restart.
Without this, the emulator will feel sluggish.

### Option B — A Real Android Phone (recommended if available)

This is actually the **best option** — fastest, most accurate, no RAM cost,
and you test on real hardware.

1. On the phone: **Settings → About phone** → tap **Build number** 7 times
   to unlock Developer Options.
2. **Settings → Developer options** → enable **USB debugging**.
3. Connect the phone to the PC with a USB cable.
4. Accept the "Allow USB debugging" prompt on the phone.
5. Run `flutter devices` — your phone should appear.
6. `flutter run` will install and launch the app on it.

You can also debug **wirelessly** over Wi-Fi once connected once
(`adb tcpip 5555` then `adb connect <phone-ip>`).

### Option C — Flutter Windows Desktop (quick UI testing only)

Flutter can run the app as a native Windows desktop window — no emulator
needed. This is handy for quickly checking UI/layout, but it is **not** a
real test: Android-only features (camera, GPS, permissions, push) won't
behave the same.

```powershell
flutter config --enable-windows-desktop
flutter run -d windows
```
Requires **Visual Studio Build Tools** with the "Desktop development with
C++" workload: https://visualstudio.microsoft.com/downloads/

### Option D — Flutter Web (fastest preview)

Flutter can also run the app in Chrome. Fastest to launch, good for a quick
look at screens, but networking, native plugins, and mobile gestures differ
from a real device.

```powershell
flutter run -d chrome
```

### Option E — Third-Party Emulators (Genymotion, etc.)

There are alternative Android emulators such as **Genymotion**
(https://www.genymotion.com) which some developers find lighter or faster
than the stock AVD. They work with Flutter via ADB. However:

- ⚠️ Genymotion's desktop version is **free only for personal use**;
  business use needs a license.
- ⚠️ Gaming-oriented emulators (BlueStacks, LDPlayer, NoxPlayer) are **not
  recommended** for development — they are tuned for games, can be hard to
  connect to ADB reliably, and may bundle unwanted software.

**Recommendation:** the stock **Android Studio AVD (Option A)** is perfectly
good and is what the Flutter team supports best. If you own an Android phone,
**use it (Option B)** — it's the fastest and most reliable. Reach for
Genymotion only if the stock emulator is too slow on your machine and you
have no physical device.

### iOS Simulator — macOS only

> ⚠️ The **iOS Simulator cannot run on Windows or Linux** — this is an Apple
> restriction, not a Flutter limitation.

To build and test the iOS version of Tabib you need access to a Mac:

| Option | Notes |
|--------|-------|
| A physical Mac | Install **Xcode** from the App Store → iOS Simulator is built in |
| Cloud Mac rental | MacStadium (https://www.macstadium.com), MacinCloud (https://www.macincloud.com) |
| CI build service | Codemagic (https://codemagic.io) — Flutter-focused, builds iOS without owning a Mac |

On a Mac:
```bash
sudo xcode-select --install   # command-line tools
open -a Simulator              # launch the iOS Simulator
flutter devices                # Flutter detects the simulator
```
Flutter macOS install guide: https://docs.flutter.dev/install/macos

**For Tabib Phase 1, focus on Android.** iOS can wait until you have Mac
access — none of the Android setup work is wasted.

---

## 🔟 Docker Desktop (Optional — install later)

For containerizing the backend / AI service and deployment.

## 9️⃣ Docker Desktop (Optional — install later)

For containerizing the backend / AI service and deployment.

- **Download:** https://www.docker.com/products/docker-desktop/

Skip for now; install when you reach the deployment phase.

---

## 🔧 Recommended Extras

| Tool | Link | Purpose |
|------|------|---------|
| Windows Terminal | https://github.com/microsoft/terminal | Better terminal |
| PowerToys | https://github.com/microsoft/PowerToys | Productivity utilities |
| Supabase CLI | `npm install -g supabase` | Local Supabase / migrations |
| DBeaver | https://dbeaver.io/download/ | Database GUI |
| Postman | https://www.postman.com/downloads/ | API testing |

---

## ✅ Final Verification

Run in a **new** PowerShell window:

```powershell
git --version
node --version       # v22.11.0
npm --version        # 10.x
python --version     # Python 3.11.15
pip --version
java -version        # 17.0.x
flutter --version    # 3.44.0
dart --version       # 3.5+
adb --version
docker --version     # if installed
```

All print versions → environment is ready. ✅

---

## 🧪 Get Tabib Running

### Backend (Node/Express/TS)
```powershell
cd D:\path\to\tabib-backend
Remove-Item -Recurse -Force node_modules, package-lock.json
npm install
npm run dev          # starts on port 3001
```

### Web Frontend (React/Vite)
```powershell
cd D:\path\to\tabib-web
Remove-Item -Recurse -Force node_modules, package-lock.json
npm install
npm run dev
```

### AI Service (Python/FastAPI)
```powershell
cd D:\path\to\darija_triage
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

### Mobile App (Flutter)
```powershell
cd D:\path\to\tabib_mobile
flutter clean
flutter pub get
dart run build_runner build --delete-conflicting-outputs
flutter run --dart-define=API_BASE_URL=http://10.0.2.2:3001
```

---

## 📝 Install Order (Recommended)

**Session 1 (~1-2 hrs):**
1. Git
2. nvm-windows + Node 22.11.0
3. Python 3.11.15
4. VS Code + extensions

**Session 2 (~2-3 hrs):**
5. JDK 17
6. Flutter SDK + PATH
7. Android Studio + SDK + emulator
8. `flutter doctor` verification

**Later:**
9. Docker Desktop
10. Extras (DBeaver, Postman, Supabase CLI)

---

## 🚨 Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| `command not recognized` | Restart terminal after changing env vars |
| Multiple Python versions clash | Uninstall old ones; keep one in PATH |
| Node native modules fail | Delete `node_modules` + `package-lock.json`, reinstall |
| Flutter path with spaces | Use `C:\src\flutter`, not `Program Files` |
| Gradle build fails | Ensure JDK 17 is active |
| App can't reach backend in emulator | Use `10.0.2.2` instead of `localhost` |
| Python 3.12/3.13 ML lib errors | Stick to Python 3.11 |
