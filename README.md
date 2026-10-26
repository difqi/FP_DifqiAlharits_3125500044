# Final Project Oprec PENSHIP — Deteksi Objek Menggunakan YOLOv8

## Identitas

- **Nama:** Difqi Alharits
- **NRP:** 3125500044
- **Program Studi:**   D3 Teknik Informatika

## Project Roboflow

- **Project ID:** `difqis-workspace/penship-object-detection-igimu`
- **Dataset Version:** 1
- **Nama Version:** `PENSHIP_YOLOv8_Baseline_640`
- **Roboflow Universe:** https://universe.roboflow.com/difqis-workspace/penship-object-detection-igimu

Dataset digunakan untuk melatih model object detection dengan tiga kelas, yaitu `bottle`, `laptop`, dan `pencil`. Seluruh anotasi dilakukan secara manual menggunakan bounding box di Roboflow.

## Dataset

Dataset terdiri dari **104 gambar asli** yang diambil secara manual menggunakan kamera smartphone POCO F3.

Dataset meliputi:
- `bottle`: botol plastik berlogo maupun tanpa label, dengan variasi ukuran 220 ml, 600 ml, dan 1,5 liter.
- `laptop`: laptop dalam kondisi terbuka maupun tertutup.
- `pencil`: pensil kayu dengan variasi posisi dan sudut.
- Foto campuran dengan beberapa objek target.
- Foto background tanpa objek target, ditandai sebagai Null.

**Distribusi foto asli per kategori:**

| Kategori | Jumlah |
|---|---:|
| Bottle | 55 |
| Laptop | 16 |
| Pencil | 17 |
| Campuran | 11 |
| Background | 5 |
| **Total** | **104** |

Pengambilan foto dilakukan dengan variasi jarak, sudut kamera, pencahayaan, posisi objek, dan latar belakang. Metode pengumpulan menggunakan foto manual tanpa ekstraksi frame video.

Dataset dibagi menjadi training, validation, dan testing. Proses preprocessing menggunakan Resize 640 × 640 dengan metode Fit (black edges) untuk mempertahankan proporsi gambar. Tidak diterapkan augmentasi tambahan pada Roboflow untuk versi baseline.

## Training

Training dilakukan menggunakan Google Colab dengan akselerator NVIDIA Tesla T4.

| Parameter | Konfigurasi |
|---|---|
| Model | YOLOv8n pretrained |
| Epochs | 50 |
| Batch | 16 |
| Image Size | 640 |
| GPU | Tesla T4 |
| Dataset | Roboflow Version 1 |
| Optimizer | Auto (Ultralytics) |

Model menggunakan bobot awal `yolov8n.pt` dan dilakukan fine-tuning pada dataset tiga kelas.

Hasil terbaik disimpan sebagai `best.pt`. Grafik training dan evaluasi tersedia pada folder `grafik/`.

## Pengembangan Kode

Pengembangan dari notebook dasar panitia meliputi:

1. Penyesuaian konfigurasi Roboflow agar menggunakan dataset Version 1 milik peserta.
2. Penambahan evaluasi terpisah pada test set menggunakan `model.val(split="test")` untuk mengukur performa model pada data pengujian.
3. Penambahan kode inferensi untuk menguji beberapa foto baru menggunakan `model.predict()`.
4. Penambahan pengujian confidence threshold pada nilai 0,10, 0,25, dan 0,50 untuk menganalisis deteksi yang terlewat dan potensi false positive.
5. Penambahan penyimpanan hasil evaluasi ke Google Drive sebagai cadangan.

## Hasil Eksperimen

### Hasil baseline

| Skenario | Epochs | Batch | Optimizer | mAP50 | mAP50-95 |
|---|---:|---:|---|---:|---:|
| Baseline — Validation | 50 | 16 | Auto | 0,902 | 0,675 |
| Baseline — Test | 50 | 16 | Auto | 0,987 | 0,829 |

Kedua baris tersebut merupakan evaluasi model yang sama pada subset berbeda, bukan dua eksperimen training terpisah.

### Evaluasi per kelas pada test set

| Kelas | Precision | Recall | mAP50 | mAP50-95 |
|---|---:|---:|---:|---:|
| Bottle | 1,000 | 0,950 | 0,995 | 0,867 |
| Laptop | 0,842 | 1,000 | 0,972 | 0,799 |
| Pencil | 0,866 | 1,000 | 0,995 | 0,821 |

Evaluasi test dilakukan pada 10 gambar dengan 15 objek berlabel.

## Analisis dan Kendala

Grafik training memperlihatkan tren penurunan box loss, classification loss, dan distribution focal loss. Grafik mAP50 dan mAP50-95 menunjukkan peningkatan selama proses training.

Hasil pengujian pada test set memperoleh mAP50 sebesar 98,7% dan mAP50-95 sebesar 82,9%. Namun, ukuran test set yang kecil membuat hasil ini belum cukup untuk menjamin kemampuan generalisasi model pada berbagai kondisi nyata.

Pengujian menggunakan foto baru menunjukkan bahwa model berhasil mendeteksi botol plastik berlogo maupun polos, beberapa botol dalam satu gambar, serta laptop terbuka. Akan tetapi, ditemukan kegagalan deteksi pada pensil tertentu dan botol yang berada dalam posisi rebah. Pengubahan confidence threshold belum sepenuhnya menyelesaikan masalah tersebut.

Beberapa kendala yang ditemui selama pengerjaan meliputi:
- Dataset terbatas sebanyak 104 foto asli.
- Variasi pensil dan orientasi botol belum mencukupi untuk seluruh kondisi pengujian.
- Perlunya menjaga ketepatan bounding box pada objek panjang dan tipis.
- Kesalahan path saat menampilkan hasil inferensi dan menyimpan hasil evaluasi, yang diperbaiki dengan penggunaan objek `Path` serta direktori output dari Ultralytics.

Pengembangan lebih lanjut dapat dilakukan melalui penambahan variasi dataset, perbaikan anotasi, serta eksperimen augmentasi dan hyperparameter yang terkontrol.

## Kesimpulan

Model YOLOv8n berhasil dilatih untuk mendeteksi tiga kelas objek menggunakan dataset 104 gambar asli. Hasil baseline memperlihatkan performa test yang tinggi, tetapi pengujian pada foto baru mengungkapkan keterbatasan pada beberapa orientasi objek dan kondisi pengambilan gambar.

Proyek ini menunjukkan bahwa kualitas dan keberagaman dataset, konsistensi anotasi, serta pengujian di luar dataset merupakan faktor penting dalam pengembangan sistem object detection.

## Video Demo

https://drive.google.com/file/d/1h4_YIYW8vRb2h9wb4HfTpAj2c7uSzhB2/view?usp=drive_link
