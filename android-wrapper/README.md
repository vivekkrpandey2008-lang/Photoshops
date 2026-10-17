# PhotoCraft Android wrapper (APK build path)

This repository is a Rust/egui photo editor, not a native Android app. The existing `apps/photocraft-web` target runs the same editor as WebAssembly. This folder documents the next packaging stage; it is not a prebuilt APK.

## Requirements
- Node.js LTS + npm
- Rust toolchain compatible with the repository (`rust-version` in root `Cargo.toml`)
- `wasm32-unknown-unknown` Rust target
- Trunk, wasm-bindgen tooling (Trunk downloads its matching tools)
- Android Studio with Android SDK, platform tools, and a JDK supported by that Android Studio version

## Build the editor for web first
From the repository root:

```sh
rustup target add wasm32-unknown-unknown
cargo install trunk --locked
cd apps/photocraft-web
trunk build --release
cd ../..
```

The output is `dist/web`. Test it on a real Android browser over HTTPS before wrapping it. WebGPU/WebGL2 availability and the editor's desktop-oriented controls may vary by phone.

## Create the Android shell
From the repository root, after the web build:

```sh
npm init -y
npm install @capacitor/core @capacitor/android
npm install -D @capacitor/cli
npx cap init PhotoCraft com.example.photocraft --web-dir dist/web
npx cap add android
npx cap sync android
npx cap open android
```

In Android Studio, build a debug APK using **Build > Build Bundle(s) / APK(s) > Build APK(s)**. For a release APK, configure signing in Android Studio before building. Change `com.example.photocraft` to a unique application ID you own before release.

## Important limitations
- This wrapper does not by itself redesign every desktop toolbar for small touchscreens. The existing editor UI needs real-device testing and potentially UI changes.
- PhotoCraft's web build requires a modern browser engine with WebGPU or WebGL2; performance depends on the device.
- Opening/saving uses browser-style file selection/download behavior; test Android file access carefully.
- The APK must be built with Android SDK/Gradle installed. No APK is included in this source bundle.
