Materi Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

Prompt: Buatkan ringkasan materi kuliah mengenai pengenalan sistem terdistribusi dan terdesentralisasi, termasuk karakteristik, perbedaan, dan arsitekturnya.
Bagian yang disesuaikan dari keluaran prompt ada pada: Menambahkan poin spesifik terkait arsitektur dasar (Client-Server, P2P, Hybrid) serta penjelasan CAP Theorem dari modul kuliah.

---

# Pengenalan Sistem Terdistribusi dan Terdesentralisasi

## 1. Arti Penting & Pengertian Dasar
* **Sistem Terdistribusi**: Sekumpulan komputer independen yang bekerja sama dan terlihat oleh pengguna sebagai satu sistem tunggal yang koheren.
* **Sistem Terdesentralisasi**: Fokus pada distribusi kontrol tanpa adanya *single point of failure*, di mana keputusan diambil secara kolektif melalui konsensus (contoh: Blockchain, DNS, BitTorrent).
* **Perbedaan Utama**: Sistem terdistribusi berfokus pada pembagian beban kerja (*workload*), sedangkan sistem terdesentralisasi berfokus pada pembagian kontrol kekuasaan/keputusan.

## 2. Arsitektur Sistem Dasar
* **Client-Server**: Klien meminta layanan atau data ke server pusat melalui interne
* **Peer-to-Peer (P2P)**: Setiap node memiliki kedudukan setara dan saling terhubung langsung tanpa server pusat (contoh: BitTorrent, jaringan kripto).
* **Hybrid**: Kombinasi antara struktur client/server dan P2P atau menggunakan *supernodes* untuk efisiensi komunikasi (contoh arsitektur aplikasi pesan instan).

## 3. Karakteristik & Tantangan Utama
* **Karakteristik**: Transparansi lokasi, skalabilitas tinggi, *fault tolerance*, heterogenitas perangkat, serta performa tinggi.
* **Teorema CAP (Brewer's Theorem)**: Dalam sistem terdata terdistribusi, sebuah sistem hanya bisa memilih dua dari tiga elemen secara bersamaan:
  * **Consistency (C)**: Setiap data/pembacaan menghasilkan nilai terbaru atau error.
  * **Availability (A)**: Setiap permintaan ke node yang aktif selalu merespon tanpa error.
  * **Partition Tolerance (P)**: Sistem tetap berjalan meskipun komunikasi antar node terputus.
* **Konsistensi Data**: Pilihan antara *Strong Consistency* (data langsung sinkron seperti pada sistem perbankan) atau *Eventual Consistency* (data akan sinkron seiring waktu seperti pada media sosial).
