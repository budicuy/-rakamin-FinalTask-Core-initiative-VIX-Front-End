# Laporan Pengerjaan Tugas Magang: E-Commerce Product Catalog

**Status**: Selesai  
**Tanggal**: 6 Februari 2026  
**Teknologi**: Vue.js 3, Vite, Bun, Vanilla CSS

---

## 1. Pendahuluan
Dokumen ini berisi laporan teknis dan dokumentasi lengkap mengenai pengembangan aplikasi web "E-Commerce Product Catalog". Aplikasi ini bertujuan untuk menampilkan produk dari **FakeStoreAPI** dengan logika tampilan dinamis berdasarkan kategori produk (*Men's Clothing*, *Women's Clothing*, atau *Unavailable*).

## 2. Spesifikasi Teknis & Color Palette
Sesuai arahan, aplikasi dibangun dengan spesifikasi berikut:
- **Framework**: Vue.js (Composition API)
- **Build Tool**: Vite
- **Package Manager**: Bun
- **Styling**: Vanilla CSS (Tanpa Framework CSS seperti Bootstrap/Tailwind)
- **Font**: Inter (Google Fonts)

### Color Palette (CSS Variables)
Warna-warna berikut diimplementasikan sebagai variabel CSS untuk konsistensi:
- **Navy**: `#002772` (Dominan Pria)
- **Pale Pink**: `#FDE2FF` (Background Wanita)
- **Dark**: `#1E1E1E` (Teks Utama)
- **Purple**: `#720060` (Dominan Wanita)
- **Pale Blue**: `#D6E6FF` (Background Pria)
- **Dark Grey**: `#3F3F3F` (Teks Deskripsi)
- **Silver**: `#DCDCDC` (Border & Unavailable)
- **White**: `#FFFFFF` (Background Card)

---

## 3. Tahapan Instalasi

### Prasyarat
Sistem operasi menggunakan Linux dan telah terinstall **Bun** (versi 1.3.5).

### Langkah 1: Inisialisasi Project
Project dibuat menggunakan perintah `create vite` melalui Bun.

```bash
bun create vite app --template vue
```

### Langkah 2: Instalasi Dependencies
Masuk ke direktori project dan menginstall dependencies yang dibutuhkan.

```bash
cd app
bun install
```

---

## 4. Tahapan Pengerjaan & Implementasi

### A. Integrasi API & Logika (App.vue)
Aplikasi memanggil API `https://fakestoreapi.com/products/{id}`.

1.  **State Management**:
    Menggunakan `ref` untuk menyimpan:
    -   `index`: ID produk saat ini (mulai dari 1).
    -   `product`: Data produk yang diambil.
    -   `loading`: Status loading indikator.
    -   `category`: Kategori untuk penentuan styling.

2.  **Logic Fetching**:
    -   Mengambil data berdasarkan `index`.
    -   Jika kategori adalah *"men's clothing"* atau *"women's clothing"*, data disimpan.
    -   Jika kategori lain, data produk dianggap `unavailable`.
    -   Jika `index` > 20, reset kembali ke 1.

3.  **Loading Indicator**:
    Menambahkan state `loading = true` sebelum fetch dan `loading = false` setelah selesai, memastikan UX yang responsif.

```javascript
// Cuplikan Logika Fetch
const fetchProduct = async () => {
  loading.value = true;
  try {
    const res = await fetch(`https://fakestoreapi.com/products/${index.value}`);
    const data = await res.json();
    
    if (data.category === "men's clothing" || data.category === "women's clothing") {
      product.value = data;
      category.value = data.category;
    } else {
      product.value = null; // Tampilkan halaman unavailable
      category.value = data.category;
    }
  } finally {
    loading.value = false;
  }
}
```

### B. Styling & Desain (style.css)
Desain diimplementasikan secara "Clean & Modern" menggunakan Vanilla CSS.

1.  **Struktur Halaman Dinamis**:
    Class pada container utama berubah sesuai kategori produk menggunakan Vue Class Binding:
    -   `.page-men-section`: Tema Biru/Navy.
    -   `.page-women-section`: Tema Pink/Ungu.
    -   `.page-unavailable`: Tema Abu-abu (Silver).

2.  **Komponen UI**:
    -   **Card**: Menggunakan shadow halus dan border-radius.
    -   **Loader**: Spinner CSS kustom.
    -   **Buttons**: Styling tombol berubah warna (outline/fill) mengikuti tema kategori aktif.

### C. Konfigurasi Font & Aset
Menambahkan font **Inter** dari Google Fonts pada `index.html` untuk tampilan tipografi yang modern.

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

---

## 5. Fitur Utama

1.  **Dynamic Theming**: Background dan warna tombol berubah otomatis sesuai jenis kelamin kategori produk.
2.  **Smart Navigation**: Tombol "Next Product" akan looping dari ID 1 sampai 20 terus menerus.
3.  **Unavailable Handling**: Produk di luar kategori Pria/Wanita ditampilkan dengan desain khusus "Sad Face" agar user tidak melihat data yang tidak relevan.
4.  **Responsive**: Tampilan tetap rapi di berbagai ukuran layar.

---

## 6. Cara Menjalankan Aplikasi

Untuk menjalankan aplikasi di lingkungan lokal (Dev Server):

```bash
bun run dev
```

Aplikasi akan berjalan di `http://localhost:5173`.

---

**Lampiran**:
-   *File Source Code Utama*: `src/App.vue`, `src/style.css`
-   *Endpoint API Test*: `curl https://fakestoreapi.com/products/1` (Berhasil)
