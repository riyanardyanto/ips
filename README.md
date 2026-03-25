# IPS Form Generator

Project ini adalah aplikasi HTML client-side untuk membuat file IPS baru berdasarkan template Excel.

Pengguna cukup membuka halaman [ips.html](ips.html), mengisi form utama, lalu mengunduh hasil Excel yang sudah terisi tanpa perlu membuka atau mengedit template secara manual.

## Fitur Utama

- 100% client-side, tanpa backend
- Dapat berjalan offline
- Template workbook default sudah di-embed ke dalam HTML
- Mengisi field form langsung ke cell Excel target
- Validasi visual untuk field wajib
- Tombol `Unduh hasil` hanya aktif jika field wajib valid
- Mendukung trigger checkbox `Quality`, `Delivery`, `Cost`, `Other`
- Nama file hasil unduhan dibuat otomatis dari nilai form

## File Project

- [ips.html](ips.html)
  Aplikasi utama. Berisi UI, styling, logic JavaScript, embedded workbook template, dan patching XML `.xlsx`.

- [2026_01_DD_IPS_SPM_LU21_EQUIPMENT_Problem Descript (template kosong IPS new).xlsx](2026_01_DD_IPS_SPM_LU21_EQUIPMENT_Problem%20Descript%20(template%20kosong%20IPS%20new).xlsx)
  Template workbook sumber yang dipakai untuk embed default workbook.

## Cara Menjalankan

Tidak perlu install dependency.

1. Buka [ips.html](ips.html) di browser.
2. Tunggu workbook default selesai dimuat.
3. Isi field wajib:
   - Tanggal
   - Line
   - Technology
   - Problem Description
4. Pilih trigger jika diperlukan.
5. Klik `Unduh hasil`.

## Mapping Form ke Excel

Field utama yang diisi ke sheet `IPS V4.1`:

| Field | Cell | Keterangan |
| --- | --- | --- |
| Problem Description | `B19` | Otomatis diubah ke uppercase saat diproses |
| Tanggal | `P8` | Format `yyyy-mm-dd` |
| Line | `P6` | Dipilih dari dropdown |
| Technology | `Q14` | Dipilih dari dropdown |

Trigger checkbox:

| Trigger | Cell | Nilai saat dicentang |
| --- | --- | --- |
| Quality | `C6` | `Q` |
| Delivery | `C8` | `D` |
| Cost | `C10` | `C` |
| Other | `C12` | `O` |

## Validasi

Field wajib menggunakan validasi visual:

- label diberi indikator `*`
- field kosong diberi border merah
- tombol download disabled sampai semua field wajib terisi
- status error juga ditampilkan pada area status

## Nama File Hasil

Nama file output dibentuk otomatis dari konfigurasi field.

Pola saat ini:

```text
{tanggal}_IPS_SPM_{line}_{technology}_{problem-description}.xlsx
```

Contoh:

```text
2026_01_15_IPS_SPM_LU21_PM100_BEARING NOISE.xlsx
```

## Arsitektur Singkat

Konfigurasi utama disimpan di dalam [ips.html](ips.html):

- `FIELD_CONFIG`
  Menyimpan definisi field seperti label, target cell, tipe input, required, transform value, options dropdown, dan kontribusi ke nama file.

- `FILE_NAME_TEMPLATE`
  Menentukan urutan bagian nama file hasil unduhan.

- `UI_TEXT`
  Menyimpan status text dan string UI utama.

Workbook diproses langsung di browser dengan membaca dan mengubah XML di dalam file `.xlsx`.

## Catatan Teknis

- Sheet target dikunci ke `IPS V4.1`
- Value type target dikunci ke `text`
- Workbook default di-load otomatis saat halaman dibuka
- Setelah download berhasil, field form dibersihkan kembali
- Project ini tidak memerlukan koneksi internet untuk penggunaan normal

## Maintenance

Jika template Excel berubah:

1. Perbarui file template `.xlsx` di folder project.
2. Jika workbook default embedded ikut berubah, regenerate nilai `DEFAULT_WORKBOOK_BASE64` di [ips.html](ips.html).
3. Sesuaikan `FIELD_CONFIG` jika ada perubahan cell mapping, dropdown, atau aturan transform.

## Batasan

- Saat ini hanya mendukung satu template embedded default
- Target sheet masih fixed ke `IPS V4.1`
- Struktur form masih ditulis di HTML, meskipun label, placeholder, mapping, dan status text sudah dipusatkan ke konfigurasi JavaScript