Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 4: Konsistensi dan Replikasi pada Sistem Terdistribusi
AI yang digunakan: Gemini
Prompt: Buatkan rangkuman materi Konsistensi dan Replikasi pada Sistem Terdistribusi (Modul 04) dalam format Markdown, mencakup Teorema CAP, spektrum model konsistensi, trade-off latensi, strategi replikasi, penanganan konflik (CRDT & OT), dan protokol replikasi.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penjabaran spektrum 7 model konsistensi (dari Linearizable hingga Eventual), rumus Quorum-based replication, serta penjelasan cara kerja CRDT dan Operational Transformation.

# Konsistensi dan Replikasi pada Sistem Terdistribusi

## 1. Konsep Konsistensi & Teorema CAP
**Konsistensi** menjamin bahwa semua node dalam sistem memiliki data yang sama (atau tampak sama) pada waktu tertentu meskipun salinan data tersebar di berbagai lokasi fisik.
* **Teorema CAP**: Dalam sistem terdistribusi yang pasti mengalami gangguan jaringan (*Partition Tolerance*), perancang sistem harus memilih antara **CP** (menjaga konsistensi dengan menolak transaksi saat jaringan terputus) atau **AP** (tetap melayani permintaan dengan risiko stok/data sementara tidak sinkron).

## 2. Model Konsistensi (Spektrum Strong ke Weak)
1. **Linearizability**: Varian terkuat dari *strong consistency*; setiap operasi tampak dieksekusi secara atomik dan instan secara global *real-time* (contoh: `etcd`, ZooKeeper, Google Spanner).
2. **Sequential Consistency**: Semua operasi tampak dieksekusi dalam urutan yang sama di semua node, meski tidak harus *real-time*.
3. **Causal Consistency**: Operasi yang memiliki hubungan sebab-akibat harus terlihat berurutan oleh semua node (misal: komentar tidak boleh muncul sebelum postingan induknya).
4. **Read-Your-Writes Consistency**: Pengguna dipastikan selalu langsung melihat data yang baru saja ditulisnya sendiri.
5. **Monotonic Read Consistency**: Pembacaan berulang oleh pengguna tidak akan pernah menampilkan versi data yang lebih lama daripada yang sudah pernah dilihatnya.
6. **Eventual Consistency (BASE)**: Jika tidak ada pembaruan baru, seluruh replika pada akhirnya akan memiliki data yang sama. Mengutamakan *Basically Available*, *Soft-state*, dan *Eventual consistency* (contoh: DynamoDB, Cassandra).

## 3. Trade-off Latensi dengan Konsistensi
* **Konsistensi Tinggi ($\uparrow$) $\rightarrow$ Latensi Tinggi ($\uparrow$)**: Sistem harus menunggu sinkronisasi lintas jaringan selesai sebelum membalas klien.
* **Konsistensi Rendah ($\downarrow$) $\rightarrow$ Latensi Rendah ($\downarrow$)**: Sistem langsung membalas klien dari node terdekat dan menyebarkan pembaruan di latar belakang.

## 4. Replikasi Data & Strategi Sinkronisasi
**Replikasi** adalah pemeliharaan salinan data di beberapa node untuk meningkatkan *availability*, *performance* (baca dari node terdekat), *reliability*, dan *scalability*.
* **Arsitektur/Arah Replikasi**:
  * *Master-Slave (Primary-Secondary)*: Satu master menangani *write*, *slave* menangani *read* (contoh: PostgreSQL Streaming Replication).
  * *Multi-Master (Peer-to-Peer)*: Semua node menerima *read* dan *write* (contoh: Apache Ignite 3, Cassandra).
* **Waktu Sinkronisasi**: *Synchronous* (menunggu semua replika *commit*), *Semi-synchronous* (menunggu minimal 1 replika, seperti pada MySQL), dan *Asynchronous* (langsung *commit* di sumber).
* **Cara Sinkronisasi**: *Pull-based* (replika mengecek berkala), *Push-based* (sumber mengirim perubahan via *Change Data Capture* / WebSockets / SSE), dan *Hybrid*.

## 5. Penanganan Konflik Pembaruan Konkuren
* **Last Write Wins (LWW)**: Pembaruan dengan *timestamp* paling akhir menimpa data sebelumnya.
* **Vector Clock**: Mendeteksi urutan kausalitas dan konflik antar-node.
* **CRDT (Conflict-free Replicated Data Types)**: Struktur data terdistribusi yang dapat digabungkan (*merge*) secara matematis tanpa konflik di tiap node (contoh: *library* Yjs untuk aplikasi kolaboratif).
* **Operational Transformation (OT)**: Mengubah indeks/parameter operasi yang masuk berdasarkan riwayat perubahan lokal agar dokumen akhir tetap identik (contoh: Google Docs).

## 6. Protokol Replikasi
1. **Primary-Based**: Terpusat pada satu node utama (*primary*).
2. **Quorum-Based**: Operasi *write* dan *read* sah jika memenuhi syarat kuorum $W + R > N$ (di mana $N$ adalah total node, $W$ jumlah node konfirmasi tulis, dan $R$ jumlah node konfirmasi baca).
3. **State Machine Replication**: *Leader* mengurutkan perintah dalam log dan seluruh replika mengeksekusi perintah dalam urutan identik.
4. **Gossip Protocol**: Setiap node secara berkala memilih 1–3 *peer* acak untuk menyebarkan perubahan status/data hingga merata ke seluruh klaster (digunakan pada Cassandra).
5. **Chain Replication**: Node disusun berantai; operasi *write* masuk ke *head* dan diteruskan berurutan hingga *tail*, sedangkan operasi *read* dilayani langsung oleh *tail*.
