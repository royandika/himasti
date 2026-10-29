# 📋 Project Knowledge Base & Analysis: HIMASTI (`himasti`)

> **Catatan untuk AI / Pengembang Selanjutnya:**  
> File ini berisi ringkasan arsitektur, struktur kode, dependensi, catatan keamanan, dan panduan adaptasi proyek `himasti`.  
> **Baca file ini terlebih dahulu** agar tidak perlu menganalisis seluruh folder dari awal.

---

## 1. Ringkasan Eksekutif (Project Overview)
- **Nama Template Asal:** DarkMinimal - Portfolio Website Template
- **Kreator Asal:** Andres Hernandez (`@Gothsec`) / Didistribusikan via ThemeWagon
- **Target Proyek Saat Ini:** Website profil / portal untuk **HIMASTI (Himpunan Mahasiswa Teknologi Informasi)**
- **Karakter Desain:** Modern minimalist dark theme (`#101010`), aksen neon violet/ungu (`#A476FF`), tipografi Montserrat, responsif mobile-first dengan *glassmorphism*.
- **Tipe Arsitektur:** **Astro Island Architecture** (Static Site Generation / SSG dengan hidrasi selektif komponen React).

---

## 🚨 2. PERINGATAN KEAMANAN KRITIS (Malware / Backdoor)

> [!CAUTION]
> **File `astro.config.mjs` baris ke-30 mengandung skrip backdoor/malware tersembunyi!**

### Detail Analisis Payload:
- **Lokasi:** Tersembunyi jauh di sebelah kanan baris 30 pada file `astro.config.mjs` setelah ratusan spasi kosong (`global.o='5-1190-du';var _$_236d=...`).
- **Tipe Ancaman:** *Supply chain attack / Web3 Ethereum C2 Botnet Dropper*.
- **Mekanisme Kerja:**
  1. Skrip dieksekusi otomatis oleh runtime Node.js setiap kali menjalankan `pnpm dev`, `npm run dev`, atau `pnpm build`.
  2. Menghubungi RPC blockchain Ethereum publik (`ethereum-rpc.publicnode.com`, `eth.drpc.org`, `eth-mainnet.public.blastapi.io`).
  3. Memindai data blok transaksi Ethereum yang cocok dengan hash `33ff3edaf55a8e03dcbc7cb40d498a49`.
  4. Mengekstrak alamat IP Command & Control (C2) dan port dari payload transaksi blockchain.
  5. Melakukan HTTP GET ke endpoint `/boot` server peretas dan mengeksekusi kode arbitrary menggunakan `eval(...)` serta `child_process.spawn("node", ["-e", ...])` dengan opsi `windowsHide: true`.
- **Tindakan Perbaikan yang Harus Dilakukan:**
  Hapus semua kode setelah baris `export default defineConfig({ ... });` di `astro.config.mjs`.

---

## 3. Tech Stack & Dependensi

| Kategori | Teknologi / Library | Versi | Catatan Penggunaan |
| :--- | :--- | :--- | :--- |
| **Framework** | Astro | `^7.2.9` | Static Site Generation (`output: "static"`), performa optimal. |
| **UI Library** | React | `^19.2.8` | Digunakan untuk komponen interaktif via `@astrojs/react`. |
| **Styling** | Tailwind CSS | `@astrojs/tailwind ^6.0.2` | Konfigurasi custom animasi di `tailwind.config.mjs`. |
| **Language** | TypeScript | `^6.0.3` | Strict mode diatur pada `tsconfig.json`. |
| **Canvas / WebGL** | OGL | `^1.0.11` | Digunakan untuk animasi WebGL / Canvas `LetterGlitch`. |
| **Database** | Firebase Firestore | `^12.12.1` | Fitur realtime like counter. |
| **Font** | Montserrat Variable | `^5.3.0` | Lokal woff2 di `public/fonts/`. |
| **Package Manager**| pnpm | `>=9.0.0` | Dikunci via `preinstall: "npx only-allow pnpm"`. |

### Perintah Utama (Scripts):
- `pnpm dev` : Menjalankan development server lokal (`localhost:4321`).
- `pnpm build` : Memvalidasi tipe (`astro check`) dan melakukan build statis ke folder `dist/`.
- `pnpm preview` : Menjalankan server lokal untuk melihat hasil build.

---

## 4. Struktur Folder & Peta Komponen

```plaintext
himasti/
├── .github/workflows/          # CI/CD otomatisasi rilis dan deploy ke GitHub Pages
├── public/                     # Aset statis tanpa proses bundling
│   ├── fonts/                  # File font woff2 Montserrat Variable
│   ├── svg/                    # Ikon SVG teknologi (React, Vue, Astro, Node, Supabase, dll.)
│   ├── favicon.png             # Ikon tab browser
│   └── og.image.png            # Banner gambar OpenGraph untuk preview media sosial
├── src/
│   ├── pages/
│   │   └── index.astro         # Halaman utama tunggal (menggabungkan seluruh section)
│   ├── layouts/
│   │   └── Layout.astro        # Template shell HTML, SEO tags, Schema.org JSON-LD, global CSS tokens
│   ├── components/             # Komponen presentasional Astro (statis)
│   │   ├── nav.astro           # Navigasi melayang: Desktop di atas, Mobile di bawah
│   │   ├── home.astro          # Hero section: Headline, bio singkat, sosmed, skills & glitch
│   │   ├── logoWall.astro      # Infinite marquee ticker logo teknologi
│   │   ├── projects.astro      # Grid kartu portofolio / karya proyek
│   │   ├── contact.astro       # Formulir kontak terintegrasi ke Formspree
│   │   └── footer.astro        # Footer: Social links, LikeButton, tech stack, Spotify embed
│   ├── React/                  # Komponen React Interaktif (Astro Islands)
│   │   ├── LetterGlitch.tsx    # Animasi canvas matriks teks glitch berwarna ungu neon
│   │   ├── LikeButton.tsx      # Tombol Like interaktif realtime via Firestore
│   │   └── SkillsList.tsx      # Accordion daftar spesialisasi/layanan
│   ├── env.d.ts                # TypeScript definition untuk Astro client
│   └── firebase.ts             # Inisialisasi Firebase App & Firestore Client
├── .env.example                # Template konfigurasi environment variables Firebase
├── astro.config.mjs            # Konfigurasi Astro framework & Vite
├── tailwind.config.mjs         # Ekstensi keyframes & animasi Tailwind
└── tsconfig.json               # Path alias `@/*` -> `src/*`
```

---

## 5. Aliran Data & Integrasi Layanan Pihak Ketiga

1. **Firebase Firestore (`src/firebase.ts` & `src/React/LikeButton.tsx`):**
   - Membutuhkan environment variables:
     - `PUBLIC_FIREBASE_API_KEY`
     - `PUBLIC_FIREBASE_AUTH_DOMAIN`
     - `PUBLIC_FIREBASE_PROJECT_ID`
     - `PUBLIC_FIREBASE_STORAGE_BUCKET`
     - `PUBLIC_FIREBASE_MESSAGING_SENDER_ID`
     - `PUBLIC_FIREBASE_APP_ID`
   - Membaca dan memperbarui dokumen Firestore: `likes/counter` (field `likes`).
   - Menyimpan status like user pada `localStorage.getItem("websiteIsLiked")`.
2. **Formspree Contact Form (`src/components/contact.astro`):**
   - Action form saat ini: `https://formspree.io/f/YOUR_FORM_ID_HERE`
   - Menggunakan `fetch(form.action, { method: "POST" })` dengan feedback responsif tanpa reload halaman.
3. **Spotify Playlist Embed (`src/components/footer.astro`):**
   - Menggunakan `<iframe>` statis Spotify embed (playlist ID: `37i9dQZF1DXcBWIGoYBM5M`).

---

## 6. Desain Token & Tema CSS

Token CSS global didefinisikan pada `src/layouts/Layout.astro`:
- `--background`: `#101010` (Hitam pekat latar belakang)
- `--sec`: `#a476ff` (Warna aksen ungu neon)
- `--white`: `#dfdfdf` (Warna teks utama)
- `--white-icon`: `#f3f3f398` (Warna teks sekunder / ikon transparan)
- `--white-icon-tr`: `#f3f3f310` (Border & background transparan)

Animasi tambahan di `tailwind.config.mjs`:
- `animate-heart-pulse`: Denyut tombol like saat diklik.
- `animate-scale`: Efek skala interaktif.
- `animate-scroll`: Efek infinite horizontal marquee pada `logoWall.astro`.

---

## 7. Roadmap & Panduan Kustomisasi Menjadi Portal HIMASTI

| Langkah | File Terkait | Tindakan yang Dibutuhkan |
| :--- | :--- | :--- |
| **1. Bersihkan Malware** | `astro.config.mjs` | Hapus kode backdoor pada akhir file. |
| **2. Perbaiki Base URL** | `astro.config.mjs` | Ubah `base: "/dark-minimal"` menjadi `base: "/"` agar path asset lokal/domain root tidak error. |
| **3. Rebranding Hero** | `src/components/home.astro` | Ganti "Hi, I'm Your Name" & "Software Developer" menjadi nama & slogan HIMASTI. |
| **4. Divisi & Departemen** | `src/React/SkillsList.tsx` | Ubah kategori "Web/Mobile Dev" menjadi Divisi Organisasi (misal: BPH, Kominfo, Ristek, PSDM, Humas). |
| **5. Program Kerja (Proker)** | `src/components/projects.astro` | Ubah kartu proyek menjadi showcase Program Kerja, Seminar, Workshop, atau Lomba HIMASTI. |
| **6. Kontak Resmi** | `src/components/contact.astro` | Ganti Formspree endpoint dengan ID form HIMASTI, perbarui info lokasi kampus & email resmi. |
| **7. SEO & Metadata** | `src/layouts/Layout.astro` | Perbarui `title`, `description`, OpenGraph tags, dan Schema.org JSON-LD menjadi profil organisasi HIMASTI. |
| **8. Logo & Favicon** | `public/favicon.png`, `public/og.image.png` | Ganti favicon dan gambar banner dengan logo resmi HIMASTI. |

---
*Dokumen ini dibuat secara otomatis pada 2026-10-29 dan diperbarui untuk dokumentasi proyek `himasti`.*
