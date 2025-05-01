# Dokumentasi Probability Power Spectral Density

## 📘 Pengantar
Repositori ini berisi dua bagian utama kode Python untuk pengolahan dan analisis data seismik menggunakan pustaka **ObsPy**:

1. **Penggabungan data mSEED** dari beberapa sumber (stasiun atau waktu berbeda).
2. **Analisis Probability Power Spectral Density (PPSD)** untuk memvisualisasikan spektrum noise.

Setiap bagian dijelaskan langkah per langkah agar mudah dipelajari.

---

## 1. Penggabungan File mSEED
###  Tujuan:
Menggabungkan dua file MiniSEED (`.mseed`) yang berasal dari stasiun atau waktu yang sama, agar data yang diperoleh menjadi kontinu.
###  Penjelasan Kode:
Import fungsi read() dari Obspy untuk membaca file data seismik , lalu definisikan path ke file pertama.
```python
from obspy import read
file1 = "CTR0_EHZ_VG_00.011000.mseed"
file1 = "CTR0_EHZ_VG_00.012000.mseed"
```
file mSEED biasanya dinamai mengikuti format standar yang mencerminkan informasi metadata. Untuk menggabungkan data dengan jumlah file atau folder yang lebih dari satu, maka diperlukan path file selanjutnya untuk melakukan penggabungan data 
```python
st1 = read(file1)  
st2 = read(file2)  
```
Baca file path pertama dan kedua lalu gabungkan st1 dan st2 untuk menjadi satu stream.
```python
st.merge(method=1)  
output_file = "merged_file.mseed"  
st.write(output_file, format="MSEED")  
print(f"File mSEED berhasil digabungkan menjadi: {output_file}") 
```
Proses merge dengan `method=1` yang akan menginterpolasi bila ada overlap atau gap. Simpan hasil gabungan stream ke file baru dengan nama `merged_file.mseed.` Tampilkan konfirmasi di konsol bahwa file berhasil dibuat.
```python
1 Trace(s) In STream:
VG.CTR0.00.EHZ | 2024-12-7 6:49:59.000000 Z - 2024-12-14 7:00:01.000000 Z | 100.0 Hz, 8700201 samples
```
##  2. Probability Power Spectral Density (PPSD)
###  Tujuan:
Menggunakan metode PPSD untuk melihat karakteristik spektrum noise dari data seismik.
### Penjelasan Kode:
Mengimpor fungsi untuk membaca waveform dan metadata XML.
```python
from obspy import read, read_inventory
st = read("CTR0.EHZ.VG.00..mseed")
tr = st.select(id="VG.CTRO.00.EHZ")[0]
inv = read_inventory("CTR0.EHZ.VG.00.xml")
```
Untuk mengetahui informasi dan spesifikasi data seismik dapat menggunakan `print(st.__str__(extended=True))`. Selanjutnya membaca metadata respons instrumen untuk kanal tersebut dan Membuat objek `PPSD` dengan metadata. 
```python
from obspy.signal import PPSD
ppsd = PPSD(tr.stats, metadata=inv)
```
Menambahkan stream data ke dalam PPSD untuk analisis.
```python
ppsd.add(st)
>>True
```
Menampilkan hasil PPSD standar dan kumulatif.
```python
ppsd.plot()
ppsd.plot(cumulative=True)
```
![Plot PPSD](CTR0_2024full.png)

---
Untuk melihat distribusi noise dapat menggunakan colormap standar PQLX untuk visualisasi.
```python
from obspy.imaging.cm import pqlx
ppsd.plot(cmap=pqlx)
```
![Plot PPSD](CTR0_7hari_PSD.png)


## Penutup
Dokumentasi ini diharapkan dapat memberikan pemahaman yang lebih terstruktur mengenai proses penggabungan data seismik dan analisis spektral menggunakan metode PPSD dengan bantuan pustaka ObsPy. Langkah-langkah yang dijelaskan secara terperinci bertujuan untuk membantu pengguna, khususnya pemula, agar dapat memahami logika kerja di balik pemrosesan data mSEED dan pentingnya representasi statistik dalam mengevaluasi kualitas sinyal seismik. Dengan pendekatan ini, diharapkan proses analisis data dapat berjalan lebih efisien dan akurat, serta menjadi dasar kuat untuk analisis lanjutan seperti monitoring seismik atau pemodelan sumber gempa.

Ucapan terima kasih saya sampaikan kepada instansi yang telah menyediakan akses data seismik, serta kepada semua pihak yang telah mendukung tersusunnya dokumentasi dan pengolahan data ini. Tanpa kontribusi data dan dukungan teknis dari berbagai pihak, proses pembelajaran dan penelitian ini tidak akan berjalan dengan optimal.

