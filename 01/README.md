Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Pengenalan Sistem Terdistribusi dan Terdesentralisasi (Modul 01) dalam format Markdown yang terstruktur berdasarkan agenda pembahasan pada slide perkuliahan.

Bagian yang disesuaikan dari keluaran prompt ada pada: Penyesuaian struktur sub-bab agar mengikuti urutan slide Agenda (mulai dari Arti Penting hingga Tren Masa Depan) serta penambahan detail contoh kasus dan penjelasan CAP Theorem.

# Pengenalan Sistem Terdistribusi dan Terdesentralisasi

## 1. Arti Penting Sistem Terdistribusi dan Terdesentralisasi
Layanan digital modern menghadapi pertumbuhan volume data dan jumlah pengguna yang sangat masif sehingga membutuhkan:
* **Skalabilitas**: Kemampuan sistem untuk berkembang menangani peningkatan beban kerja tanpa mengorbankan performa.
* **Fault-Tolerant**: Kemampuan sistem untuk tetap beroperasi secara normal meskipun terjadi kegagalan pada sebagian komponen perangkat keras maupun perangkat lunak.
* **Ketersediaan Tinggi (24/7)**: Tanpa arsitektur ini, layanan skala global seperti Google, Netflix, maupun ekosistem Blockchain tidak mungkin beroperasi.

## 2. Pengertian & Karakteristik Sistem Terdistribusi
Menurut Andrew S. Tanenbaum dan Maarten van Steen (2007), **sistem terdistribusi** adalah sekumpulan komputer independen yang tampak bagi penggunanya sebagai satu sistem yang koheren (*single coherent system*).
* **Karakteristik Utama**: Transparansi lokasi (akses, replikasi, dan kegagalan tidak terlihat oleh pengguna), skalabilitas, *fault tolerance*, kinerja tinggi, dan heterogenitas perangkat keras/lunak.

## 3. Arsitektur Sistem Dasar
* **Client-Server**: Klien meminta layanan/data ke server terpusat melalui jaringan internet.
* **Peer-to-Peer (P2P)**: Setiap node berperan setara sebagai klien sekaligus penyedia sumber daya tanpa server pusat (contoh: BitTorrent, jaringan cryptocurrency).
* **Hybrid**: Menggabungkan arsitektur *client-server* (untuk koordinasi/supernodes) dan P2P (untuk pertukaran data langsung).

## 4. Pengertian & Perbandingan dengan Sistem Terdesentralisasi
* **Sentralisasi**: Hanya memiliki satu titik kontrol utama.
* **Desentralisasi**: Distribusi kontrol ke banyak pihak tanpa *single point of failure*, di mana keputusan diambil secara kolektif melalui mekanisme konsensus.
* **Perbedaan Fokus**:
  * **Sistem Terdistribusi**: Fokus pada **pembagian beban kerja** (*workload sharing*). Contoh: Google File System (GFS), MinIO/Silo Object Storage.
  * **Sistem Terdesentralisasi**: Fokus pada **pembagian kontrol/otoritas** (*control sharing*). Contoh: Blockchain, DNS, BitTorrent.

## 5. Studi Kasus Penerapan Nyata
* **Sistem Terdistribusi**: GFS / HDFS (penyimpanan big data), Apache Kafka & Pulsar (*messaging & streaming*), platform streaming & media sosial (Netflix, YouTube, TikTok).
* **Sistem Terdesentralisasi**: Blockchain (Bitcoin, Ethereum, Solana/Near/Aptos), Domain Name System (DNS), dan jaringan BitTorrent.

## 6. CAP Theorem (Brewer's Theorem) & Konsistensi Data
Dalam penyimpanan data terdistribusi, tidak mungkin menjamin ketiganya secara bersamaan saat terjadi gangguan jaringan (*Partition*):
* **Consistency (C)**: Setiap operasi baca menghasilkan data terbaru yang ditulis atau *error*.
* **Availability (A)**: Setiap permintaan ke node yang aktif selalu mendapat respons meskipun datanya belum tentu yang paling baru.
* **Partition Tolerance (P)**: Sistem tetap berjalan meski komunikasi antar-node terputus.

**Trade-off saat terjadi Partition:**
* **AP (Availability + Partition Tolerance)**: Tetap melayani permintaan dengan risiko data sementara tidak sinkron (*Eventual Consistency*, cocok untuk media sosial). Contoh: MongoDB (konfigurasi tertentu).
* **CP (Consistency + Partition Tolerance)**: Menolak permintaan demi menjaga kesamaan data (*Strong Consistency*, wajib untuk perbankan). Contoh: Apache Cassandra (dengan pengaturan kuorum).

## 7. Tantangan dan Tren Masa Depan
* **Tantangan**: Latensi & *throughput* jaringan, ancaman keamanan (DDoS, *Sybil Attack* yaitu pembuatan ribuan identitas palsu untuk memanipulasi jaringan P2P), kompleksitas manajemen node, dan tata kelola (*governance*).
* **Tren Masa Depan**: *Cloud-native* (Kubernetes, Docker), *Edge & Fog Computing*, Web3 & DeFi, serta *Federated Learning* (AI terdistribusi).
