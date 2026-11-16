# Lab8Web – PHP dan Database MySQL (CRUD Data Barang)

| | |
|---|---|
| **Nama** | Azizah Rachmatania |
| **NIM** | 312510159 |
| **Kelas** | I251B |
| **Mata Kuliah** | Pemrograman Web |
| **Dosen Pengampu** | Agung Nugroho, S.Kom., M.Kom. |

---

## Tujuan Praktikum
1. Memahami konsep dasar database.
2. Memahami konsep dasar CRUD menggunakan PHP.
3. Membuat program CRUD sederhana menggunakan PHP.

## Struktur Project
```
lab8_php_database/
├── koneksi.php   # koneksi PHP ke MySQL
├── index.php     # Read   – menampilkan data
├── tambah.php    # Create – menambah data
├── ubah.php      # Update – mengubah data
├── hapus.php     # Delete – menghapus data
├── style.css     # tampilan halaman
├── database.sql  # script database, tabel, dan data awal
├── gambar/       # folder penyimpanan gambar barang
└── screenshots/  # bukti hasil praktikum
```

---

## Langkah Praktikum

### Gambar 1 – XAMPP Control Panel
![XAMPP Control Panel](screenshots/1.%20APACHE%20%26%20%20XAMPP%20.png)

**Fungsi:** XAMPP Control Panel adalah alat untuk menyalakan dan mematikan layanan server di komputer lokal. Dua layanan yang dipakai adalah **Apache** (web server yang menjalankan file PHP) dan **MySQL** (database server yang menyimpan data).

**Tujuan:** Menyiapkan lingkungan server agar aplikasi PHP dan database bisa berjalan.

**Langkah:**
1. Buka XAMPP Control Panel.
2. Klik **Start** pada baris **Apache**.
3. Klik **Start** pada baris **MySQL**.
4. Pastikan kedua modul berwarna hijau dan port tampil (Apache 80 dan 443, MySQL 3306).

**Hasil:** Log di bagian bawah menampilkan `Status change detected: running` untuk Apache dan MySQL. Artinya kedua layanan sudah aktif.

---

### Gambar 2 – phpMyAdmin
![phpMyAdmin](screenshots/2.%20phpmyadmin.png)

**Fungsi:** phpMyAdmin adalah aplikasi berbasis web untuk mengelola database MySQL (membuat database dan tabel, menjalankan query SQL, melihat isi data) tanpa perlu mengetik di command line.

**Tujuan:** Membuat dan melihat database yang akan dipakai aplikasi CRUD.

**Langkah:**
1. Pastikan Apache dan MySQL sudah berjalan.
2. Buka `http://localhost/phpmyadmin/` di browser.
3. Pilih database `latihan1` di panel kiri, lalu pilih tabel `data_barang`.
4. Klik tab **Browse** untuk melihat isi tabel.

**Hasil:** Tabel `data_barang` berisi 3 baris data (HP Samsung, HP Xiaomi, HP OPPO) dengan kolom `id_barang`, `kategori`, `nama`, `gambar`, `harga_beli`, `harga_jual`, dan `stok`.

---

### Gambar 3 – Membuat Database dan Tabel
![Membuat tabel](screenshots/3.%20buat%20tabel.png)

**Fungsi:** Database adalah tempat penyimpanan data yang terstruktur. Tabel adalah bagian dari database yang berisi data dalam bentuk baris dan kolom.

**Tujuan:** Membuat database `latihan1` dan tabel `data_barang` sesuai rancangan studi kasus data barang.

**Rancangan tabel:**

| Nama Field | Tipe Data | Ukuran | Keterangan |
|---|---|---|---|
| id_barang | INT | 10 | Primary Key, Auto Increment |
| kategori | VARCHAR | 30 | |
| nama | VARCHAR | 50 | |
| gambar | VARCHAR | 100 | menyimpan path file gambar |
| harga_beli | DECIMAL | 10,0 | |
| harga_jual | DECIMAL | 10,0 | |
| stok | INT | 4 | |

**Langkah:**
1. Di phpMyAdmin, buka tab **SQL**.
2. Jalankan perintah berikut (tersedia juga di `database.sql`), lalu klik **Go**.

```sql
CREATE DATABASE latihan1;

CREATE TABLE data_barang (
  id_barang int(10) auto_increment PRIMARY KEY,
  kategori varchar(30),
  nama varchar(50),
  gambar varchar(100),
  harga_beli decimal(10,0),
  harga_jual decimal(10,0),
  stok int(4)
);
```

**Hasil:** Database `latihan1` dan tabel `data_barang` muncul di panel kiri phpMyAdmin.

---

### Gambar 4 – Data Awal (INSERT) dan Tampilan Pertama
![Data awal](screenshots/4.%20data%20awal.png)

**Fungsi:** Perintah `INSERT INTO` dipakai untuk memasukkan data baru ke tabel.

**Tujuan:** Mengisi tabel dengan 3 data contoh supaya halaman `index.php` punya data untuk ditampilkan.

**Langkah:**
1. Jalankan perintah INSERT di tab **SQL**.

```sql
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES
('Elektronik', 'HP Samsung Android', 'gambar/hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'gambar/hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'gambar/hp_oppo.jpg', 1800000, 2300000, 5);
```
2. Buka `http://localhost/lab8_php_database/`.

**Hasil:** Ketiga data tampil di tabel. Kolom gambar masih menampilkan teks alternatif (bukan foto) karena file gambar belum dimasukkan ke folder `gambar/`. Ini normal dan diperbaiki pada Gambar 6.

---

### Gambar 5 – Tes Koneksi Database (`koneksi.php`)
![Tes koneksi](screenshots/5.%20Koneksi%20.png)

**Fungsi:** `koneksi.php` menghubungkan PHP ke MySQL. File ini di-*include* oleh file lain (`index.php`, `tambah.php`, `ubah.php`, `hapus.php`) supaya konfigurasi koneksi cukup ditulis satu kali.

**Tujuan:** Memastikan PHP bisa terhubung ke database `latihan1`.

**Kode:**
```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false)
{
    echo "Koneksi ke server gagal.";
    die();
} #else echo "Koneksi berhasil";
?>
```

**Penjelasan kode:**
- `$host`, `$user`, `$pass`, `$db` berisi alamat server, nama pengguna, kata sandi (kosong secara bawaan XAMPP), dan nama database.
- `mysqli_connect()` membuka koneksi dan hasilnya disimpan di `$conn`.
- Jika koneksi gagal (`false`), tampil pesan lalu `die()` menghentikan program.

**Langkah:**
1. Buat file `koneksi.php`.
2. Hapus tanda `#` pada `else echo "Koneksi berhasil";` untuk pengujian.
3. Buka `http://localhost/lab8_php_database/koneksi.php`.
4. Setelah berhasil, kembalikan tanda `#` supaya pesan tidak muncul di halaman lain.

**Hasil:** Muncul tulisan **Koneksi berhasil**, artinya PHP sudah terhubung ke MySQL.

---

### Gambar 6 – Read: Menampilkan Data (`index.php`)
![Halaman index](screenshots/6.%20index.png)

**Fungsi:** Menampilkan seluruh data dari tabel `data_barang` dalam bentuk tabel HTML. Ini adalah operasi **Read** pada CRUD.

**Tujuan:** Membaca data dari database dan menampilkannya di browser, lengkap dengan link menuju fitur tambah, ubah, dan hapus.

**Kode inti:**
```php
include("koneksi.php");
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);
```
```php
<?php while ($row = mysqli_fetch_array($result)): ?>
<tr>
    <td><img src="<?= htmlspecialchars($row['gambar']); ?>" width="80"></td>
    <td><?= htmlspecialchars($row['nama']); ?></td>
    ...
</tr>
<?php endwhile; ?>
```

**Penjelasan kode:**
- `include("koneksi.php")` memanggil file koneksi.
- `mysqli_query()` menjalankan perintah SELECT, hasilnya disimpan di `$result`.
- `while` dan `mysqli_fetch_array()` mengambil data satu baris per putaran, lalu dicetak ke dalam `<tr>`.
- `htmlspecialchars()` mengamankan teks agar tag HTML dari data tidak dijalankan browser.
- Jika tabel kosong, ditampilkan tulisan "Belum ada data".

**Langkah:**
1. Buat file `index.php` dan `style.css`.
2. Masukkan file gambar (`hp_samsung.jpg`, `hp_xiaomi.jpg`, `hp_oppo.jpg`) ke folder `gambar/`.
3. Buka `http://localhost/lab8_php_database/`.

**Hasil:** Tabel menampilkan gambar, nama, kategori, harga beli, harga jual, stok, serta link **Ubah** dan **Hapus**. Link **Tambah Barang** ada di atas tabel.

---

### Gambar 7 – Create: Form Tambah Barang (`tambah.php`)
![Form tambah barang](screenshots/7.%20tambah%20barang.png)

**Fungsi:** Form untuk memasukkan data barang baru. Ini adalah operasi **Create** pada CRUD.

**Tujuan:** Menerima input dari pengguna (termasuk file gambar) lalu menyimpannya ke database.

**Kode inti:**
```php
if (isset($_POST['submit']))
{
    $nama = $_POST['nama'];
    ...
    $file_gambar = $_FILES['file_gambar'];

    if ($file_gambar['error'] == 0)
    {
        $filename = str_replace(' ', '_', basename($file_gambar['name']));
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        if (move_uploaded_file($file_gambar['tmp_name'], $destination))
            $gambar = 'gambar/' . $filename;
    }

    $stmt = mysqli_prepare($conn,
      'INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) VALUES (?, ?, ?, ?, ?, ?)');
    mysqli_stmt_bind_param($stmt, 'ssiiis', ...);
    mysqli_stmt_execute($stmt);
    header('location: index.php');
}
```

**Penjelasan kode:**
- `isset($_POST['submit'])` memeriksa apakah tombol Simpan sudah ditekan.
- `$_POST` menampung isi form teks, sedangkan `$_FILES` menampung file yang diupload.
- `move_uploaded_file()` memindahkan file dari folder sementara ke folder `gambar/`.
- Query memakai *prepared statement* (tanda `?`) supaya aman dari SQL Injection.
- `header('location: index.php')` mengarahkan pengguna kembali ke halaman daftar.
- Form wajib memakai `method="post"` dan `enctype="multipart/form-data"` agar file bisa terkirim.

**Langkah:**
1. Klik **Tambah Barang** di `index.php`.
2. Isi nama (contoh: HP Vivo Android), kategori (Hand Phone), harga jual (2300000), harga beli (2500000), stok (5).
3. Pilih file gambar lewat tombol **Choose File**.
4. Klik **Simpan**.

---

### Gambar 8 – Hasil Tambah Barang
![Berhasil tambah barang](screenshots/8.%20berhasil%20tambah%20barang.png)

**Fungsi:** Membuktikan bahwa data baru tersimpan di database dan terbaca oleh halaman Read.

**Hasil:** Setelah klik Simpan, halaman otomatis kembali ke `index.php` dan baris baru **HP Vivo Android** (kategori Hand Phone) muncul di urutan paling bawah beserta gambarnya. Berarti proses Create dan upload gambar berhasil.

---

### Gambar 9 – Update: Form Ubah Barang (`ubah.php`)
![Form ubah barang](screenshots/9.%20ubah%20barang.png)

**Fungsi:** Form untuk mengedit data barang yang sudah ada. Ini adalah operasi **Update** pada CRUD.

**Tujuan:** Menampilkan data lama ke dalam form, lalu menyimpan perubahan ke database.

**Kode inti:**
```php
// ambil data lama berdasarkan id dari URL
$id = (int) $_GET['id'];
$stmt = mysqli_prepare($conn, 'SELECT * FROM data_barang WHERE id_barang = ?');
...
// saat form disimpan
UPDATE data_barang SET nama=?, kategori=?, harga_jual=?, harga_beli=?, stok=? WHERE id_barang=?
```

**Penjelasan kode:**
- Link **Ubah** mengirim id lewat URL, misalnya `ubah.php?id=4`, dibaca dengan `$_GET['id']`.
- Data lama diambil dengan `SELECT ... WHERE id_barang = ?` lalu diisikan ke atribut `value` pada form.
- Fungsi `is_select()` memberi atribut `selected` pada kategori yang sesuai agar dropdown menampilkan pilihan lama.
- `<input type="hidden" name="id">` membawa id barang ke proses simpan tanpa terlihat pengguna.
- Bagian `WHERE id_barang = ?` wajib ada agar hanya satu baris yang berubah.
- Jika pengguna tidak memilih gambar baru, kolom `gambar` tidak diubah, jadi gambar lama tetap dipakai.

**Langkah:**
1. Klik **Ubah** pada baris yang ingin diedit.
2. Ubah isi form (pada contoh: kategori dan harga).
3. Klik **Simpan**.

---

### Gambar 10 – Hasil Ubah Barang
![Berhasil ubah barang](screenshots/10.%20berhasil%20ubah%20barang.png)

**Fungsi:** Membuktikan data hasil edit sudah tersimpan.

**Hasil:** Baris **HP Vivo Android** kini berkategori Elektronik dengan harga beli 2500000 dan harga jual 2300000, sesuai isi form ubah. Berarti proses Update berhasil.

---

### Gambar 11 – Delete: Konfirmasi Hapus (`hapus.php`)
![Konfirmasi hapus](screenshots/11.%20hapus%20barang.png)

**Fungsi:** Menghapus satu data dari tabel. Ini adalah operasi **Delete** pada CRUD.

**Tujuan:** Menghapus data berdasarkan id, dengan konfirmasi dulu agar tidak terhapus karena salah klik.

**Kode inti:**
```php
// index.php
<a href="hapus.php?id=<?= $row['id_barang']; ?>"
   onclick="return confirm('Yakin ingin menghapus data ini?');">Hapus</a>
```
```php
// hapus.php
$id = (int) $_GET['id'];
$stmt = mysqli_prepare($conn, 'DELETE FROM data_barang WHERE id_barang = ?');
mysqli_stmt_bind_param($stmt, 'i', $id);
mysqli_stmt_execute($stmt);
header('location: index.php');
```

**Penjelasan kode:**
- `confirm()` pada JavaScript memunculkan kotak "Yakin ingin menghapus data ini?". Jika memilih Cancel, penghapusan dibatalkan.
- Jika memilih OK, browser membuka `hapus.php?id=...`, lalu `DELETE ... WHERE id_barang = ?` menghapus baris yang sesuai.
- Tanpa klausa `WHERE`, seluruh data tabel akan terhapus.

**Langkah:**
1. Klik **Hapus** pada baris yang ingin dihapus (contoh: HP Vivo Android).
2. Pada kotak konfirmasi, klik **OK**.

---

### Gambar 12 – Hasil Hapus Barang
![Berhasil hapus barang](screenshots/12.%20berhasil%20hapus%20barang.png)

**Hasil:** Baris **HP Vivo Android** sudah tidak ada. Tersisa 3 data awal (Samsung, Xiaomi, OPPO). Berarti proses Delete berhasil dan keempat operasi CRUD (Create, Read, Update, Delete) sudah berjalan.

---

## Perbaikan dari Modul
| Masalah di modul | Perbaikan |
|---|---|
| Header "Harga Jual" dan "Harga Beli" urutannya terbalik dengan isinya | Disamakan urutannya |
| Kolom Aksi hanya menampilkan `id_barang` | Diganti link Ubah dan Hapus |
| Link "Tambah Barang" tidak ada di `index.php` | Ditambahkan |
| `enctype="multipart/formdata"` (salah ketik) di `ubah.php` | Menjadi `multipart/form-data` |
| Semua `is_select()` memakai `'Komputer'` | Disesuaikan dengan nilai opsinya |
| Query memasukkan input langsung (rentan SQL Injection) | Memakai *prepared statement* |
| Path gambar tidak konsisten (`hp_samsung.jpg` vs `gambar/...`) | Semua disimpan sebagai `gambar/namafile` |
| Kolom `nama` varchar(30), padahal rancangan 50 | Dibuat varchar(50) |

## Kesimpulan
Aplikasi CRUD (Create, Read, Update, Delete) data barang berhasil dibuat menggunakan PHP dan MySQL melalui ekstensi `mysqli`. Seluruh fitur sudah diuji dan berjalan dengan baik: data dapat ditampilkan, ditambah (termasuk upload gambar), diubah, dan dihapus. Praktikum ini juga menunjukkan pentingnya pemisahan file koneksi, penggunaan *prepared statement* untuk keamanan, serta konfirmasi sebelum menghapus data.
