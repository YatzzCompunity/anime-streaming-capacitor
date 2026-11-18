# Anime Streaming — Capacitor (APK)

Aplikasi streaming anime dayat (data dari API Samehadaku) dibungkus jadi
aplikasi Android dengan [Capacitor](https://capacitorjs.com/). Isi `www/`
adalah aplikasi web-nya (export dari artifact `anime-streaming`).

- App ID: `com.dayat.animestreaming`
- App name: `Anime Streaming`
- Mode: Guest / Mode Tamu (belum ada login — lihat "Login" di bawah)

Folder `android/` TIDAK disimpan di repo — project Android-nya di-generate
dari template Capacitor saat build (`npx cap add android`), atau lokal
dengan perintah yang sama. Yang disimpan di repo hanya source-nya
(`www/`, `assets/`, config, workflow).

## Cara dapat APK-nya

### Opsi A — GitHub Actions (paling gampang, tanpa install apa-apa)

1. Push folder ini ke repo GitHub (misal `YatzzCompunity/anime-streaming-capacitor`).
2. Buka tab **Actions** → jalankan workflow **Build Anime Streaming APK**
   (atau push ke `main`, workflow jalan otomatis).
3. Download artifact `anime-streaming-debug-apk` → install file
   `app-debug.apk` di HP.

Catatan dari pengalaman build ZMusic: JANGAN tambah step
`android-actions/setup-android` — runner `ubuntu-latest` sudah membawa
Android SDK, dan step itu yang dulu merusak build.

### Opsi B — Android Studio (di laptop)

1. Buka Android Studio → Open → pilih folder `android/`.
2. Tunggu Gradle sync selesai.
3. Run (tombol ▶) ke HP/emulator, atau Build → Build APK.

## Ikon & splash

Ikon launcher dan splash screen TIDAK disimpan sebagai PNG di repo
(mereka file biner). Sumbernya adalah `assets/icon.svg` dan
`assets/splash.svg`; file PNG-nya di-generate otomatis saat build oleh
`@capacitor/assets` (langkah "Generate Android icons & splash" di
workflow). Untuk generate lokal: `npx @capacitor/assets generate --android`.

## Update isi aplikasi web-nya

Kalau aplikasi web-nya berubah (artifact `anime-streaming` diedit):

```bash
# 1. Export ulang artifact-nya ke www/index.html
#    (file hasil export: workspace/your_files/anime-streaming/anime-streaming.html)
cp ~/workspace/your_files/anime-streaming/anime-streaming.html www/index.html

# 2. Sync ke project Android
npx cap sync android
```

Lalu build ulang APK seperti di atas.

## Rilis ke Play Store / APK release (signing)

APK dari workflow di atas adalah **debug** (tidak bisa naik Play Store).
Untuk release:

1. Buat keystore sekali saja:
   `keytool -genkey -v -keystore anime-streaming.keystore -alias anime -keyalg RSA -keysize 2048 -validity 10000`
   Simpan file + password-nya baik-baik. JANGAN commit keystore ke repo.
2. Simpan sebagai GitHub Secrets: `KEYSTORE_BASE64` (isi file keystore
   dalam base64), `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`.
3. Tambah step signing sebelum `assembleRelease` (atau build release
   lokal di Android Studio dengan keystore yang sama).

## Login

Aplikasi ini masih **mode tamu** — belum ada sistem login. Kalau mau ada
login, langkah berikutnya adalah pasang login di aplikasi web-nya +
server auth sendiri (Node + JWT, tanpa Supabase — sesuai keputusan
dayat 2026-11-19), lalu export ulang dan sync seperti di atas.
