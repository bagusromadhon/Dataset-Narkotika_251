# Dataset Putusan Narkotika — PN NGANJUK

> Kumpulan **50 dokumen putusan pengadilan** perkara narkotika dan psikotropika
> dari PN NGANJUK, beserta ringkasan terstrukturnya, disiapkan sebagai dataset untuk
> tugas dan praktikum **Temu Kembali Informasi (Information Retrieval)**.

![status](https://img.shields.io/badge/dokumen-50-blue)
![format](https://img.shields.io/badge/format-PDF%20%2B%20XLSX-green)
![lisensi](https://img.shields.io/badge/lisensi-Dokumen%20Publik-lightgrey)
![bahasa](https://img.shields.io/badge/bahasa-Indonesia-red)

---

## Daftar Isi
- [Tentang Dataset](#tentang-dataset)
- [Struktur Repository](#struktur-repository)
- [Kamus Data](#kamus-data)
- [Statistik Dataset](#statistik-dataset)
- [Metodologi Akuisisi](#metodologi-akuisisi)
- [Cara Menggunakan](#cara-menggunakan)
- [Potensi Pemanfaatan](#potensi-pemanfaatan)
- [Keterbatasan](#keterbatasan)
- [Etika dan Privasi](#etika-dan-privasi)
- [Lisensi dan Sitasi](#lisensi-dan-sitasi)
- [Tim Penyusun](#tim-penyusun)

---

## Tentang Dataset

Putusan pengadilan adalah dokumen publik yang kaya informasi hukum: identitas perkara, dakwaan,
tuntutan penuntut umum, keterangan saksi, barang bukti, pertimbangan hakim, hingga amar putusan.
Sifatnya **terbuka dan tidak terikat Hak atas Kekayaan Intelektual (HaKI)**, sehingga sangat cocok
dijadikan sumber pengetahuan sekaligus dataset untuk domain Temu Kembali Informasi.

Dataset ini memusatkan perhatian pada satu klasifikasi perkara — **Narkotika dan Psikotropika** —
dan satu lembaga peradilan — **PN NGANJUK**. Pembatasan ini disengaja: dokumen dalam satu
klasifikasi memiliki struktur dan kosakata yang homogen, sehingga cocok untuk menguji pengaruh
*preprocessing* dan skema pembobotan terhadap kualitas pencarian tanpa terganggu keragaman domain.

| Atribut | Nilai |
|---|---|
| Sumber | [Direktori Putusan Mahkamah Agung RI](https://putusan3.mahkamahagung.go.id/direktori.html) |
| Direktori | Pidana Khusus |
| Klasifikasi | Narkotika dan Psikotropika |
| Lembaga Peradilan | PN NGANJUK |
| Rentang tahun putusan | 2023–2024 |
| Jumlah dokumen | 50 berkas PDF |
| Status putusan | **bukan** Berkekuatan Hukum Tetap |
| Bahasa | Indonesia |
| Tanggal pengambilan | 14 November 2026 |

---

## Struktur Repository

```
Dataset-Narkotika_251/
├── Dataset/
│   └── Narkotika.zip        # 50 dokumen putusan (*.pdf)
├── Overview/
│   └── Overview.xlsx        # ringkasan terstruktur 50 putusan
└── README.md                # dokumentasi ini
```

### Isi `Narkotika.zip`
Berisi 50 berkas `*.pdf` dengan nama asli sebagaimana diberikan server Direktori Putusan saat
diunduh, berpola `putusan_<nomor>_pid.sus_<tahun>_pn_njk_<kode-waktu>.pdf`. Setiap berkas dapat
dipetakan ke nomor putusannya pada `Overview.xlsx` melalui bagian `<nomor>` dan `<tahun>` pada
nama berkas — contoh `putusan_111_pid.sus_2024_pn_njk_....pdf` berpadanan dengan
`111/Pid.Sus/2024/PN Njk` pada kolom **No Putusan**.

---

## Kamus Data

### Lembar `Overview` (kolom wajib sesuai soal)

| Kolom | Tipe | Keterangan |
|---|---|---|
| `No` | integer | Nomor urut 1–50 |
| `No Putusan` | string | Nomor register perkara, format `<nomor>/Pid.Sus/<tahun>/PN Njk` |
| `Lembaga Peradilan` | string | Pengadilan yang menjatuhkan putusan (PN NGANJUK) |
| `Barang Bukti` | teks panjang | Daftar/uraian barang bukti, diekstrak dari frasa *"barang bukti berupa …"* pada bagian pertimbangan maupun amar |
| `Amar Putusan` | teks panjang | Isi amar, diekstrak dari penanda *"M E N G A D I L I"* hingga *"Demikianlah diputuskan …"* |

---

## Statistik Dataset

| Metrik | Nilai |
|---|---|
| Jumlah dokumen | 50 |
| Nomor putusan unik | 50 |
| Rata-rata panjang amar putusan | 3,018 karakter |
| Rata-rata panjang uraian barang bukti | 976 karakter |
| Amar berhasil diekstrak (≥ 50 karakter) | 50 / 50 |
| Barang bukti berhasil diekstrak (≥ 20 karakter) | 50 / 50 |

### Sebaran menurut tahun putusan

| Tahun | Jumlah |
|---|---|
| 2024 | 35 |
| 2023 | 15 |

---

## Metodologi Akuisisi

1. **Penelusuran manual.** Direktori Putusan disaring langsung melalui antarmuka web pada
   direktori *Pidana Khusus* → klasifikasi *Narkotika dan Psikotropika* → PN NGANJUK, mencakup
   putusan tahun 2023–2024, sesuai ketentuan tugas yang mengharuskan pengambilan
   dilakukan secara manual (bukan dengan *scraper* otomatis terhadap situs sumber).
2. **Penyaringan.** Dari seluruh kandidat yang ditemukan, putusan yang **tidak** memiliki lampiran
   PDF (berkas belum diunggah pengadilan) dikeluarkan. Putusan berstatus *Berkekuatan Hukum Tetap*
   juga dikeluarkan sesuai ketentuan tugas; pada data tingkat pertama (PN NGANJUK) status ini
   tidak ditampilkan pada setiap halaman detail, sehingga pemeriksaannya dilakukan melalui daftar
   dan isi putusan itu sendiri.
3. **Pengunduhan.** Setiap berkas PDF diunduh satu per satu dari halaman resmi Direktori Putusan,
   menghasilkan 50 dokumen unik yang lolos penyaringan pada langkah 2.
4. **Ekstraksi.** Teks setiap PDF dibaca dengan `pdfplumber`, dibersihkan dari pengaruh *watermark*
   diagonal, nomor halaman, dan disclaimer kepaniteraan, lalu kolom `Barang Bukti` serta
   `Amar Putusan` diekstrak dengan pola regex yang memanfaatkan sifat formulaik naskah putusan
   Indonesia (penanda baku seperti "MENGADILI", "barang bukti berupa", "DEMIKIANLAH diputuskan").
5. **Pemeriksaan mutu.** Jumlah dokumen, keunikan nomor putusan, serta keberhasilan ekstraksi tiap
   kolom diperiksa (lihat [Statistik Dataset](#statistik-dataset)) sebelum dataset dipublikasikan.

---

## Cara Menggunakan

### Mengunduh dan membongkar

```bash
git clone https://github.com/<username>/Dataset-Narkotika_251.git
cd Dataset-Narkotika_251
unzip Dataset/Narkotika.zip -d Dataset/pdf
```

### Memuat ringkasan dengan pandas

```python
import pandas as pd

overview = pd.read_excel('Overview/Overview.xlsx', sheet_name='Overview')
print(overview.shape)
print(overview.loc[0, 'Amar Putusan'][:400])
```

### Membaca teks lengkap salah satu putusan

```python
import pdfplumber, glob

berkas = sorted(glob.glob('Dataset/pdf/*.pdf'))[0]
with pdfplumber.open(berkas) as pdf:
    teks = '\n'.join(h.extract_text() or '' for h in pdf.pages)
print(teks[:1000])
```

### Membangun pencarian sederhana (TF-IDF)

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

korpus = overview['Amar Putusan'].fillna('').tolist()
vec = TfidfVectorizer()
X = vec.fit_transform(korpus)

kueri = vec.transform(['sabu-sabu golongan I pidana penjara tujuh tahun'])
skor = cosine_similarity(kueri, X).ravel()
for i in skor.argsort()[::-1][:5]:
    print(round(skor[i], 4), overview.loc[i, 'No Putusan'])
```

---

## Potensi Pemanfaatan

- **Mesin pencari dokumen hukum** — *preprocessing*, pengindeksan, dan pencarian pada teks putusan
  dengan TF-IDF, BM25, maupun model *dense retrieval*.
- **Pra-pemrosesan teks hukum berbahasa Indonesia** — tokenisasi, penghapusan *stopword*, dan
  *stemming* pada ragam bahasa hukum yang formal dan panjang.
- **Ekstraksi informasi** — pengenalan entitas seperti nama terdakwa, barang bukti, pasal, dan
  besaran pidana (*Named Entity Recognition*).
- **Peringkasan otomatis** — peringkasan ekstraktif maupun abstraktif atas naskah putusan.
- **Analisis deskriptif putusan** — pola lama pidana penjara dan besaran denda pada perkara narkotika,
  serta hubungannya dengan jenis dan berat barang bukti.
- **Klasifikasi dokumen** — prediksi pasal yang didakwakan atau tingkat pemidanaan.

---

## Keterbatasan

- **Cakupan sempit.** Hanya satu pengadilan dan satu klasifikasi perkara, sehingga tidak mewakili
  praktik peradilan narkotika di Indonesia secara keseluruhan. Jangan dipakai menarik simpulan
  yuridis umum.
- **Ukuran kecil.** 50 dokumen memadai untuk tugas dan eksperimen TKI klasik, tetapi terlalu kecil
  untuk melatih model bahasa dari nol.
- **PDF hasil pindaian.** Sebagian kecil putusan berpotensi merupakan hasil pindaian tanpa lapisan
  teks sehingga `pdfplumber` tidak dapat membacanya dengan baik; dokumen semacam ini memerlukan OCR.
- **Ekstraksi berbasis aturan serta gangguan watermark.** Kolom `Barang Bukti` dan `Amar Putusan`
  dihasilkan regex pada teks yang sebagian tercampur *watermark* diagonal milik Mahkamah Agung.
  Pola penulisan yang menyimpang dari kelaziman dapat menghasilkan potongan tidak lengkap —
  **selalu verifikasi ke berkas PDF asli** untuk keperluan yang menuntut ketepatan tinggi.
- **Bukan nasihat hukum.** Dataset ini disusun untuk keperluan akademik pengolahan bahasa alami,
  bukan untuk rujukan penanganan perkara.

---

## Etika dan Privasi

Dokumen putusan pengadilan bersifat **terbuka untuk umum** dan dipublikasikan sendiri oleh
Mahkamah Agung RI melalui Direktori Putusan, sehingga tidak terikat HaKI. Meski demikian, naskah
putusan memuat **identitas pribadi** (nama terdakwa, saksi, alamat). Pengguna dataset diminta:

1. memakai dataset hanya untuk keperluan penelitian, pendidikan, dan pengembangan sistem;
2. **tidak** menggunakannya untuk mempermalukan, memprofilkan, atau merugikan pihak mana pun;
3. melakukan **anonimisasi** terlebih dahulu bila hasil olahan akan dipublikasikan luas;
4. menghormati asas **praduga tak bersalah** — putusan dalam dataset ini belum berkekuatan hukum
   tetap sehingga masih terbuka upaya hukum lanjutan.

---

## Lisensi dan Sitasi

Dokumen putusan merupakan **produk badan peradilan negara** dan berstatus dokumen publik.
Berkas pendukung dalam repository ini (`Overview.xlsx`, `README.md`) dirilis untuk keperluan
akademik; silakan dipakai ulang dengan mencantumkan sumber.

```bibtex
@misc{dataset_narkotika_pn_nganjuk,
  title  = {Dataset Putusan Narkotika PN NGANJUK (2023--2024)},
  author = {Bagus Romadon},
  year   = {2026},
  note   = {UTS Temu Kembali Informasi, Teknik Informatika,
            Universitas Muhammadiyah Malang},
  howpublished = {\url{https://github.com/<username>/Dataset-Narkotika_251}}
}
```

**Sumber data:** Direktori Putusan Mahkamah Agung Republik Indonesia —
<https://putusan3.mahkamahagung.go.id/direktori.html>

---

## Tim Penyusun

| Nama | NIM |
|---|---|
| Bagus Romadon | 202310370311251 |

Tugas dikerjakan secara individu.

**Mata kuliah:** Temu Kembali Informasi (*Information Retrieval*)
**Dosen Pengampu:** Galih W. W
**Program Studi:** Teknik Informatika, Fakultas Teknik, Universitas Muhammadiyah Malang
**Semester:** Ganjil 2026/2027
