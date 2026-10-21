# Peramalan Nilai Ekspor Pertanian Indonesia dengan LSTM

Peramalan nilai ekspor bulanan 14 komoditas pertanian Indonesia (data BPS, Jan 2022 sampai Des 2025) menggunakan Long Short-Term Memory (LSTM). Notebook mencakup eksplorasi data, praproses, pemodelan, pencarian parameter, peramalan Jan–Feb 2026, dan visualisasi hasil.

## Isi repositori

| Berkas | Keterangan |
|---|---|
| `peramalan_ekspor_pertanian_lstm.ipynb` | Notebook utama dengan analisis dan seluruh output |
| `data/ekspor.xlsx` | Nilai ekspor bulanan per komoditas (juta US$), sumber BPS |
| `hasil_grid_search.csv` | Skor validasi 72 kombinasi parameter (18 konfigurasi × 4 titik epoch) |

## Pendekatan

- Satu LSTM bersama untuk 14 komoditas, dinormalisasi per komoditas (MinMax), dengan sliding window.
- Peramalan rekursif multi-langkah (6 bulan untuk Jul–Des 2025, 2 bulan untuk Jan–Feb 2026).
- Parameter dipilih pada data validasi (Jan–Jun 2025), bukan pada data uji, untuk menghindari kebocoran informasi.
- Hasil dibandingkan dengan tiga pembanding sederhana: naif, musiman naif, dan rata-rata historis.
- Metrik: RMSE, MAE, dan NRMSE (RMSE dibagi rentang data latih) agar komoditas berskala berbeda dapat dibandingkan.

## Hasil ringkas

| | NRMSE |
|---|---|
| Jul–Des 2025, parameter awal | 0,232 |
| Jul–Des 2025, parameter terbaik (`look_back=3`, `units=64`, `lr=0.01`, `epochs=150`) | **0,218** |
| Jul–Des 2025, rata-rata historis (pembanding) | 0,208 |
| Jan–Feb 2026 terhadap angka aktual | 0,152 |
| Jan–Feb 2026, naif (pembanding) | 0,148 |

LSTM bekerja baik pada komoditas yang stabil (Lada Hitam, Rumput Laut, Sarang Burung, Lain-lain) tetapi belum mengungguli pembanding sederhana secara keseluruhan. Dengan hanya 48 titik per komoditas dan deret yang bergeser level (Kopi, Buah-buahan), hasil ini wajar dan dibahas lengkap di bagian keterbatasan notebook.

## Menjalankan

Kebutuhan: Python 3.12 dengan TensorFlow 2.20, pandas, scikit-learn, matplotlib, dan openpyxl.

```bash
pip install tensorflow pandas scikit-learn matplotlib openpyxl jupyter
jupyter notebook peramalan_ekspor_pertanian_lstm.ipynb
```

Menjalankan seluruh notebook memakan waktu beberapa menit karena grid search dibaca dari `hasil_grid_search.csv`. Untuk menjalankan ulang grid search dari awal (sekitar 20 menit), ubah `ULANG_GRID = True` pada Bagian 6.

## Sumber data

Badan Pusat Statistik (BPS), Nilai Ekspor Bulanan Produk Pertanian menurut Komoditas (juta US$).
