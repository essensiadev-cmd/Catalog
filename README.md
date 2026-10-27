# Essensia Catalog

PWA katalog demo aplikasi. Login memakai Firebase Auth, data di Firestore, hosting di GitHub Pages.

## Struktur
- `index.html` — seluruh aplikasi (UI + logika). **Update fitur cukup di file ini.**
- `manifest.json`, `service-worker.js`, `icons/` — PWA (jarang diubah).
- `.nojekyll` — agar GitHub Pages menyajikan file apa adanya.

## Setup sekali saja
1. **Firebase Console → Authentication → Sign-in method**: aktifkan *Email/Password* dan *Google*.
2. **Authentication → Settings → Authorized domains**: tambahkan `USERNAME.github.io`.
3. **Firestore Database → Create database**, lalu tab **Rules**, tempel:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{db}/documents {
    match /users/{uid} {
      allow read: if true;
      allow create, delete: if request.auth != null && request.auth.uid == uid;
      allow update: if (request.auth != null && request.auth.uid == uid)
        || request.resource.data.diff(resource.data).affectedKeys().hasOnly(['views']);
    }
    match /slugs/{s} {
      allow read: if true;
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
    match /catalogs/{id} {
      allow read: if true;
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow update: if (request.auth != null && resource.data.uid == request.auth.uid)
        || request.resource.data.diff(resource.data).affectedKeys().hasOnly(['clicks']);
      allow delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
  }
}
```
4. Upload semua file ke repo GitHub → **Settings → Pages → Deploy from branch (main / root)**.

## Link publik
Di menu **Setting** isi *Nama link*, mis. `essensia`. Link: `https://USERNAME.github.io/REPO/?u=essensia` — bisa dibuka siapa saja tanpa login.

## Catatan
- `apiKey` Firebase web memang publik; keamanan data dijaga oleh Firestore Rules di atas.
- Jika setelah update `index.html` tampilan lama masih muncul, tutup dan buka ulang aplikasi sekali.
