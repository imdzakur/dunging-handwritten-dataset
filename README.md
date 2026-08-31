# Dataset Aksara Dunging (Iban)

Dataset citra tulisan tangan untuk **aksara Dunging**, sistem tulisan yang digunakan untuk bahasa Iban. Dataset ini dibuat dari nol karena belum tersedia dataset publik untuk aksara ini, dan ditujukan untuk penelitian klasifikasi citra serta dokumentasi aksara daerah.

Seluruh sampel ditulis tangan oleh **satu penulis**. Batasan ini dan dampaknya terhadap generalisasi model dijelaskan di bagian [Batasan yang Diketahui](#batasan-yang-diketahui). Baca bagian tersebut sebelum menggunakan dataset ini untuk melaporkan angka akurasi.

---

## Ringkasan

| Properti | Nilai |
|---|---|
| Jumlah kelas (huruf) | 59 |
| Jumlah penulis | 1 |
| Total citra dalam repo ini | 7.440 |
| Citra asli sebelum augmentasi | 5.900 (100 per kelas, tidak disertakan di repo ini) |
| Resolusi | 224 x 224 piksel |
| Format | PNG |
| Kanal warna | Grayscale |
| Lisensi | CC BY 4.0 |

---

## Struktur Folder

Dataset sudah dibagi dan siap dipakai. Setiap split disusun satu subfolder per kelas.

```
train/
├── <nama_kelas_1>/
├── <nama_kelas_2>/
└── ...
val/
└── ...
test/
└── ...
```

Citra mentah sebelum augmentasi dan pembagian tidak disertakan di repo ini.

---

## Cara Dataset Dibuat

- **Penulisan:** seluruh glyph ditulis tangan oleh satu orang menggunakan [ISI: alat tulis, misal pulpen gel 0.5 mm] di atas [ISI: media, misal kertas HVS putih polos].
- **Digitasi:** [ISI: dipindai dengan scanner / difoto dengan kamera ponsel], lalu dipotong per glyph.
- **Praproses:** citra dikonversi ke grayscale dan diseragamkan ke 224 x 224 piksel.
- **Pelabelan:** label ditentukan saat penulisan, bukan setelahnya, sehingga tidak ada proses menebak kelas dari citra.
- **Augmentasi:** citra latih diperbanyak dari 5.900 menjadi 7.440 lewat augmentasi. Teknik dan parameter yang dipakai tidak terdokumentasi dan tidak dapat direkonstruksi. Lihat bagian Batasan yang Diketahui.

---

## Rujukan Bentuk Aksara

Bentuk setiap glyph dicocokkan terhadap [ISI: nama sumber rujukan, misal tabel aksara dari buku atau publikasi X, halaman Y].

Rujukan ini menentukan benar atau tidaknya seluruh dataset. Jika terdapat perbedaan bentuk antara rujukan yang dipakai di sini dengan sumber lain, dataset ini mengikuti rujukan di atas.

---

## Pembagian Data

Rasio pembagian yang dipakai adalah 70 persen train, 15 persen validation, dan 15 persen test.

| Split | Jumlah citra | Proporsi |
|---|---|---|
| Train | 5.208 | 70% |
| Validation | 1.116 | 15% |
| Test | 1.116 | 15% |
| **Total** | **7.440** | **100%** |

Angka per split di atas dihitung dari rasio, bukan dari penghitungan langsung isi folder. Perlu diverifikasi sebelum dikutip.

Catatan: 7.440 tidak habis dibagi 59, hasilnya 126,1. Artinya jumlah citra per kelas tidak seragam.

---

## Cara Menggunakan

Struktur satu folder per kelas kompatibel dengan loader standar.

```python
# Contoh dengan torchvision
from torchvision import datasets, transforms

tf = transforms.Compose([
    transforms.Grayscale(num_output_channels=1),
    transforms.Resize((224, 224)),
    transforms.ToTensor(),
])

train = datasets.ImageFolder("train", transform=tf)
val = datasets.ImageFolder("val", transform=tf)
test = datasets.ImageFolder("test", transform=tf)

print(len(train.classes))  # 59
```

Catatan: loader gambar umumnya memuat PNG sebagai tiga kanal RGB meskipun isinya grayscale. Baris `Grayscale(num_output_channels=1)` di atas memaksa satu kanal. Kalau memakai model pralatih yang mengharapkan tiga kanal, hapus baris tersebut.

---

## Batasan yang Diketahui

Bagian ini ditulis eksplisit agar angka hasil eksperimen tidak dibaca melebihi apa yang sebenarnya dibuktikan.

- **Satu penulis.** Seluruh 5.900 citra asli ditulis oleh satu orang. Model yang dilatih di sini belajar mengenali satu gaya tulisan tangan, bukan aksara Dunging secara umum. Akurasi tinggi pada test set tidak membuktikan model mampu membaca tulisan orang lain.
- **Satu kondisi perekaman.** Alat tulis, media, pencahayaan, dan alat digitasi seragam untuk seluruh sampel. Variasi yang biasa muncul di dunia nyata seperti ketebalan tinta, tekstur kertas, bayangan, dan sudut pengambilan foto tidak terwakili.
- **Augmentasi tidak terdokumentasi.** Teknik dan parameter augmentasi yang menghasilkan 7.440 citra dari 5.900 citra asli tidak tercatat. Akibatnya proses pembuatan dataset ini tidak dapat direproduksi.
- **Kemungkinan kebocoran data antar split.** Rasio 70, 15, 15 diterapkan pada total 7.440, yaitu jumlah setelah augmentasi. Ini mengindikasikan pembagian dilakukan setelah augmentasi, bukan sebelumnya. Jika benar, hasil augmentasi dari satu citra asli dapat muncul di train sekaligus di test, dan akurasi yang diukur pada test set akan lebih tinggi daripada kemampuan sebenarnya. Dugaan ini tidak dapat diverifikasi maupun dibantah karena citra mentah tidak disimpan.
- **Citra mentah tidak disertakan.** Pembagian train, val, dan test tidak bisa diulang dengan seed berbeda, dan pengguna lain tidak bisa memverifikasi sendiri komposisi tiap split.
- **Augmentasi bukan pengganti variasi antar-penulis.** Transformasi geometris mengubah citra secara visual, tetapi tidak mereproduksi perbedaan bentuk goresan antar orang. Penambahan jumlah citra lewat augmentasi tidak menambah keragaman gaya tulisan.
- **Ketergantungan pada satu rujukan.** Jika penulis konsisten menuliskan satu glyph dengan bentuk yang keliru, kesalahan itu akan konsisten di seluruh dataset dan tidak akan terdeteksi oleh proses pelatihan maupun evaluasi.
- **Distribusi per kelas belum diverifikasi.** Jumlah citra per kelas pada tiap split belum dihitung satu per satu, sehingga kemungkinan ketidakseimbangan kelas belum bisa dikesampingkan.
- **Belum ada evaluasi lintas penulis.** Tidak tersedia held-out set berisi tulisan orang lain, sehingga kemampuan generalisasi dataset ini belum pernah diukur.

Rencana perbaikan yang paling berdampak, berurutan: mengulang pengumpulan dengan mencatat proses augmentasi dan menyimpan citra mentah, lalu menambah penulis kedua dan ketiga untuk membentuk test set lintas penulis.

---

## Lisensi

Dataset ini dilisensikan di bawah [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

Anda bebas menyalin, menyebarkan, dan memodifikasi dataset ini untuk keperluan apa pun, termasuk komersial, selama mencantumkan atribusi kepada penulis. Teks lengkap ada di file [LICENSE](./LICENSE).

---

## Sitasi

Jika dataset ini digunakan, mohon rujuk sebagai:

```
Muhammad Dzaky Ramdani Syakur. (2026). Dataset Aksara Dunging (Iban).
GitHub. https://github.com/imdzakur/dunging-handwritten-dataset
```

---

## Konteks

Dataset ini disusun pada tahun 2025 sebagai bagian dari mata kuliah Kerja Praktik di Telkom University.