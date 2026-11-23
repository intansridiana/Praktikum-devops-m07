# Blameless Postmortem - Insiden Kegagalan Deployment Manual
## Ringkasan Insiden
Proses serah-terima aplikasi dari peran Developer ke Operations mengalami
kegagalan berulang saat dijalankan secara manual, akibat dokumentasi
serah-terima (HANDOVER.md) yang tidak mencantumkan detail environment
setup yang dibutuhkan.
## Kronologi (timeline)
- 16:57 - Artefak (src/, HANDOVER.md) diserahkan ke Operations
- 16:59 - Percobaan pertama menjalankan aplikasi gagal: ModuleNotFoundError
  (Flask belum terpasang)
- Percobaan instalasi dependensi gagal: error externally-managed-environment
- Instalasi ulang berhasil menggunakan flag --break-system-packages
- 17:02 - Aplikasi berhasil berjalan dan lulus verifikasi (curl merespons 200)

## Dampak (waktu terbuang, jumlah kegagalan)
- Lead Time manual: ±5-6 menit
- Jumlah kegagalan (failed attempts): 2 kali
- Jumlah potensi pertanyaan yang tertahan karena aturan simulasi: 2
  (soal kebutuhan virtual environment dan metode instalasi dependensi)

## Akar Masalah pada SISTEM (bukan pada orang)
Akar masalah bukan kelalaian individu Developer dalam menulis dokumen,
melainkan tidak adanya standar/template serah-terima yang mewajibkan
pencantuman metode setup environment (virtual environment, versi Python,
dependensi terkunci). Sistem kerja tidak memiliki mekanisme verifikasi
otomatis sebelum artefak dianggap "siap serah-terima".

## Tindakan Perbaikan (action items) + penanggung jawab peran
- [Peran Dev] Menyusun template HANDOVER.md standar yang wajib mencantumkan
  metode setup environment sebelum serah-terima berikutnya.
- [Peran Dev/Ops bersama] Mengimplementasikan skrip otomasi (setup.sh) yang
  menyertakan pembuatan virtual environment dan instalasi dependensi
  terkunci secara otomatis, sehingga proses tidak lagi bergantung pada
  interpretasi manual masing-masing pihak.

## Pelajaran yang Diambil
Otomasi (setup.sh) berhasil menghilangkan kebutuhan komunikasi manual dan
mengurangi jumlah langkah yang harus diingat Operations dari beberapa
langkah menjadi satu perintah. Namun otomasi memindahkan titik risiko dari
faktor manusia (miskomunikasi) ke faktor infrastruktur (ketergantungan pada
koneksi jaringan saat instalasi dependensi), sehingga kestabilan lingkungan
kerja tetap menjadi faktor penting yang harus diperhatikan.
