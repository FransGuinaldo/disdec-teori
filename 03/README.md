Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Sinkronisasi dan Koordinasi pada Sistem Terdistribusi (Modul 03) dalam format Markdown, meliputi perbedaan data race dan race condition, sinkronisasi waktu fisik (NTP & PTP), waktu logis (Vector Clocks), mutual exclusion, dan pemilihan koordinator.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penyusunan tabel perbandingan Data Race vs Race Condition, penjabaran langkah perhitungan Vector Clocks, serta ringkasan algoritma Mutual Exclusion dan Leader Election.

# Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

## 1. Pentingnya Sinkronisasi: Data Race vs Race Condition
Sinkronisasi diperlukan untuk menyelaraskan proses, *state*, dan akses sumber daya bersama di berbagai node agar tidak terjadi konflik data maupun *deadlock*.

| Parameter | Data Race | Race Condition |
| :--- | :--- | :--- |
| **Fokus Utama** | Akses memori secara bersamaan (*concurrent memory access*). | Urutan dan waktu eksekusi instruksi (*timing & execution sequence*). |
| **Level** | Low-level (akses memori fisik / CPU). | High-level (alur logika aplikasi / *business logic*). |
| **Syarat / Penyebab** | Minimal 2 *thread* mengakses memori yang sama, minimal 1 operasi *write*, tanpa *lock*. | Kebenaran hasil bergantung pada urutan eksekusi yang tidak dapat diprediksi. |
| **Dampak** | *Undefined behavior*, korupsi memori, nilai tertimpa. | Bug logika bisnis (misal: stok barang salah hitung, saldo minus). |
| **Solusi** | Mutex / Lock, tipe data *Atomic*. | *Atomic Check-and-Act*, *Database Transaction Lock*. |

## 2. Sinkronisasi Waktu Fisik
Penyelarasan *real-time clock* antar-node terhadap standar internasional **UTC** (*Coordinated Universal Time*) yang berbasis **TAI** (*International Atomic Time*) menggunakan referensi satelit GPS / *Atomic Clock*:
* **NTP (Network Time Protocol)**: Protokol standar (RFC 5905) berbasis hierarki *Stratum* untuk menyinkronkan jam komputer melalui jaringan IP dengan akurasi milidetik (implementasi di Linux: `chrony`).
* **PTP (Precision Time Protocol - IEEE 1588)**: Protokol sinkronisasi waktu dengan presisi jauh lebih tinggi (hingga sub-mikrodetik) yang memerlukan dukungan *hardware timestamping* pada *Network Interface Card* (NIC) (implementasi di Linux: `linuxptp` dengan `ptp4l` dan `phc2sys`).

## 3. Sinkronisasi Waktu Logis (Vector Clocks)
Untuk melacak hubungan kausalitas antar-proses secara lebih akurat dibandingkan Lamport Scalar Clock, digunakan **Vector Clocks** (array dinamis berukuran $N$ sesuai jumlah proses):
* Setiap proses $P_i$ memulai vektor dengan nilai nol, misal `[0, 0, 0]`.
* Setiap terjadi *internal event* atau sebelum mengirim pesan, $P_i$ menambah elemen indeksnya sendiri sebesar $+1$.
* Saat menerima pesan bervektor $V_{msg}$, $P_i$ memperbarui setiap elemen dengan nilai maksimum ($\max(V_{lokal}[k], V_{msg}[k])$), lalu menambah indeksnya sendiri sebesar $+1$.
* **Aturan Perbandingan**: Event $A$ terjadi sebelum $B$ ($A \rightarrow B$) jika seluruh elemen vektor $A \le B$ dan minimal ada satu elemen yang lebih kecil. Jika tidak memenuhi syarat tersebut, kedua event bersifat *concurrent* (independen).

## 4. Mutual Exclusion Terdistribusi
Mekanisme untuk memastikan hanya satu proses yang dapat memasuki *Critical Section* (mengakses sumber daya bersama) pada satu waktu dengan memenuhi prinsip *Mutual Exclusion*, *Progress*, *Bounded Waiting*, dan *Fairness*:
1. **Centralized Algorithm**: Satu node koordinator mengatur izin akses. Sederhana, namun rentan *single point of failure* dan *bottleneck*.
2. **Ricart-Agrawala Algorithm**: Terdistribusi penuh menggunakan *timestamp*. Proses meminta izin ke semua node lain dan masuk *Critical Section* setelah menerima balasan `OK` dari seluruh node (membutuhkan $2 \times (N - 1)$ pesan).
3. **Token-Based Algorithm**: Sebuah token unik diedarkan antar-node (misal dalam topologi cincin). Hanya pemegang token yang boleh mengakses sumber daya. Sangat hemat pesan (1 pesan per entri), namun sulit mendeteksi jika token hilang.

## 5. Pemilihan Koordinator (Leader Election)
Ketika koordinator mengalami kegagalan atau sistem baru berjalan, node harus memilih pemimpin secara otomatis:
* **Bully Algorithm**: Node dengan ID tertinggi yang mendeteksi kegagalan akan mengambil alih dan "mengintimidasi" node ber-ID lebih rendah untuk tunduk.
* **Ring Algorithm**: Pesan pemilihan diedarkan melingkar melewati semua node aktif untuk mengumpulkan daftar ID, lalu node dengan ID tertinggi ditetapkan sebagai koordinator.
* **Berbasis Konsensus (Raft / Paxos / Zab)**: Digunakan pada sistem produksi modern seperti Kubernetes (`etcd` menggunakan Raft) dan Apache ZooKeeper (menggunakan Zab).
