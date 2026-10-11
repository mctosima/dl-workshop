# Workshop Deep Learning — Teknik Telekomunikasi ITERA

Workshop dasar *deep learning* dua sesi untuk mahasiswa Teknik Telekomunikasi,
berfokus pada *hands-on* dengan PyTorch.

| Sesi | Topik | Studi kasus |
|---|---|---|
| 1 | *Artificial Neural Network*: struktur, fungsi aktivasi, *feed forward*, *backpropagation* | Deteksi intrusi jaringan (NSL-KDD) |
| 2 | RNN & LSTM (*time series*) | Prediksi trafik jaringan |

Halaman workshop: <https://mctm.web.id/dl-workshop>

## Persiapan (peserta)

1. Instal [uv](https://docs.astral.sh/uv/):
   ```bash
   # macOS / Linux
   curl -LsSf https://astral.sh/uv/install.sh | sh
   # Windows (PowerShell)
   powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
   ```
2. Clone repo ini lalu instal dependensi:
   ```bash
   git clone https://github.com/mctosima/dl-workshop.git
   cd dl-workshop
   uv sync
   ```
3. Jalankan materi Sesi 1:
   ```bash
   uv run jupyter lab                  # buka sesi-1-ann/konsep_ann.ipynb & eda_nslkdd.ipynb
   uv run python sesi-1-ann/train.py   # training ANN di NSL-KDD (±15 detik di CPU)
   ```
   `train.py` mengunduh NSL-KDD otomatis ke `sesi-1-ann/data/` dan menyimpan model,
   grafik loss, *confusion matrix*, serta `metrics.json` di `sesi-1-ann/outputs/`.
   Untuk bereksperimen, ubah angka di `sesi-1-ann/config.py` (epoch, *learning rate*,
   *batch size*, jumlah neuron) lalu jalankan ulang.

## Struktur repo

```
sesi-1-ann/                  # Sesi 1: ANN untuk deteksi intrusi jaringan
  konsep_ann.ipynb           # konsep ANN langkah demi langkah (data sintetis)
  eda_nslkdd.ipynb           # eksplorasi data (EDA) NSL-KDD sebelum training
  train.py                   # alur training utama — dibaca dari atas ke bawah
  config.py                  # semua pengaturan eksperimen
  data.py                    # unduh & baca NSL-KDD
  preprocessing.py           # label, one-hot, split, scaling, DataLoader
  model.py                   # arsitektur MLP 122-64-32-1
  training.py                # training loop + early stopping
  evaluation.py              # prediksi & metrik
  plots.py                   # grafik loss & confusion matrix
  data/, outputs/            # dibuat otomatis (tidak di-commit)
eda-web/                     # website statis EDA NSL-KDD (buka index.html)
  index.html, style.css, app.js  # halaman & grafik SVG (tanpa pustaka, bisa offline)
  data.js                    # ringkasan data hasil build_data.py (di-commit)
  build_data.py              # hitung ulang data.js dari KDDTrain+/KDDTest+
paper/                       # paper EDA + MLP NSL-KDD (Typst)
  main.typ, refs.bib         # naskah & daftar pustaka
  scripts/experiments.py     # 5 seed, ablasi log1p, analisis per kategori & ambang -> results.json
  scripts/make_figures.py    # gambar paper -> img/
slides/
  theme/itera.typ            # tema touying (university) + palet ITERA
  logos/                     # logo ITERA
  scripts/make_figures.py    # gambar matplotlib untuk slide
  scripts/check_toy_example.py  # verifikasi angka contoh hitung manual
  sesi-1/main.typ, main.pdf  # slide Sesi 1 (Typst)
```

## Website EDA NSL-KDD

Halaman interaktif yang merangkum `eda_nslkdd.ipynb`: komposisi data, kualitas, label,
fitur kategorikal dan numerik, korelasi, serta perbandingan data latih vs uji.
Situs ini statis dan tidak butuh server. Cukup buka `eda-web/index.html` di *browser*
(fontnya dimuat dari Google Fonts bila online; jika offline dipakai font sistem).

```bash
open eda-web/index.html                    # macOS (Windows: start, Linux: xdg-open)
python3 -m http.server -d eda-web 8000     # alternatif: http://localhost:8000
uv run python eda-web/build_data.py        # hitung ulang data.js (mengunduh NSL-KDD bila perlu)
```

## Build slide (pengajar)

Butuh [Typst](https://typst.app) ≥ 0.14 (paket `touying` dan `cetz` diunduh otomatis).

```bash
uv run python slides/scripts/make_figures.py      # regenerasi gambar
uv run python slides/scripts/check_toy_example.py # cek angka contoh
cd slides && typst compile --root . sesi-1/main.typ sesi-1/main.pdf
```

## Build paper

```bash
uv run python paper/scripts/experiments.py    # ±1,5 menit di CPU
uv run python paper/scripts/make_figures.py
typst compile --root paper paper/main.typ paper/main.pdf
```
