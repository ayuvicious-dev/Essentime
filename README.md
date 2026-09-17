# Essentime

Timer PWA sederhana dan tenang. Bikin timer sebanyak yang kamu mau (menyeduh teh, meditasi, masak, dsb.), berjalan paralel, tersinkron ke akunmu lewat Firebase — jadi bisa lanjut dari perangkat lain.

## Fitur

- Masuk/daftar dengan **email & kata sandi** (Firebase Authentication)
- Timer tersimpan per akun di **Cloud Firestore**, real-time di semua perangkat
- Bikin timer lewat menit+detik manual atau tombol preset (1/3/5/10/15 menit)
- Mulai, jeda, atur ulang, dan hapus timer — beberapa timer bisa berjalan bersamaan
- Bunyi lonceng + notifikasi browser saat timer selesai
- PWA: bisa di-install ke layar utama, app shell tetap tampil saat offline

## Struktur proyek

```
essentime/
├── .nojekyll
├── README.md
├── index.html          # seluruh UI + logika app (satu file)
├── manifest.json        # manifest PWA
├── service-worker.js    # cache app shell untuk offline
└── icons/                # ikon PWA (72–512px) + maskable + favicon
```

## Menjalankan

1. Buat repo GitHub baru, unggah seluruh isi folder ini.
2. Aktifkan **GitHub Pages** (Settings → Pages → Deploy from branch → `main` / root).
3. Buka `https://<username>.github.io/<repo>/`.

## Konfigurasi Firebase

Proyek ini sudah memakai konfigurasi Firebase project `essentime-11301` (tertanam di `index.html`). Di [Firebase Console](https://console.firebase.google.com/) untuk project tersebut, pastikan:

1. **Authentication → Sign-in method** → aktifkan **Email/Password**.
2. **Firestore Database** → buat database (mode production).
3. **Firestore → Rules**, pakai aturan berikut supaya tiap pengguna hanya bisa mengakses timer miliknya sendiri:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId}/timers/{timerId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```

4. **Authentication → Settings → Authorized domains** → tambahkan domain GitHub Pages-mu (mis. `<username>.github.io`).

Kalau suatu saat ingin memakai project Firebase lain, ganti objek `firebaseConfig` di bagian atas skrip pada `index.html`.

## Data yang tersimpan

Setiap timer disimpan di `users/{uid}/timers/{timerId}` dengan field: `name`, `durationSeconds`, `remainingSeconds`, `status` (`idle` / `running` / `paused` / `finished`), `endAt`, `createdAt`.
