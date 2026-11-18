Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 2: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi (Modul 02) dalam format Markdown sesuai agenda perkuliahan, mencakup manajemen proses, jenis IPC, protokol komunikasi, hingga konsep logical clock.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penambahan rincian mekanisme Lamport Logical Clock beserta contoh alur Sales, Product, dan Invoice Service sesuai studi kasus pada slide.

# Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

## 1. Konsep & Manajemen Proses
Proses adalah instansiasi program yang sedang dieksekusi di memori (RAM) dan dijadwalkan oleh sistem operasi untuk dijalankan oleh CPU. Dalam sistem terdistribusi, manajemen proses mencakup:
* **Process Creation**: Pembuatan proses baru, alokasi memori/sumber daya, dan inisialisasi *Process Control Block* (PCB).
* **Process Migration**: Pemindahan proses yang sedang berjalan dari satu CPU/node ke node lain untuk penyeimbangan beban (*load balancing*) atau penanganan kegagalan.
* **Process Monitoring**: Pemantauan status, penggunaan CPU/memori, dan performa proses.

## 2. Inter-Process Communication (IPC) pada Sistem Terdistribusi
Karena proses berjalan pada node fisik yang berbeda dan tidak memiliki memori fisik bersama (*shared memory*), komunikasi dilakukan melalui jaringan dengan pendekatan:
* **Message Passing**: Pertukaran pesan langsung antar-proses melalui jaringan (contoh: Socket TCP/UDP, standar MPI / *Message Passing Interface* seperti OpenMPI dan `mpi4py`).
* **Remote Procedure Call (RPC)**: Mekanisme yang memungkinkan program memanggil fungsi/prosedur di komputer lain seolah-olah pemanggilan lokal menggunakan *Client/Server Stub* (contoh: gRPC).
* **Message-Oriented Middleware (MOM)**: Infrastruktur perantara (*message broker*) yang memfasilitasi pengiriman pesan secara asinkron menggunakan *Message Queue* atau pola *Publish/Subscribe* (contoh protokol: AMQP, MQTT, XMPP, DDS).

## 3. Komunikasi Sinkron vs Asinkron & Permasalahannya
* **Sinkron**: Pengirim berhenti (*blocking*) menunggu balasan dari penerima sebelum melanjutkan proses (contoh: HTTP request-response, RPC standar).
* **Asinkron**: Pengirim langsung melanjutkan eksekusi setelah mengirim pesan tanpa menunggu balasan seketika (contoh: Message Queue / Pub-Sub).
* **Masalah Utama IPC**:
  1. *Reliability*: Kehilangan paket diatasi dengan *acknowledgement* (ACK), transmisi ulang, dan protokol TCP.
  2. *Ordering*: Menjaga urutan logis pesan menggunakan FIFO *pipes*, *message queue*, atau urutan RPC.
  3. *Security*: Enkripsi (SSL/TLS) dan autentikasi (OAuth2).
  4. *Performance*: Optimalisasi latensi dan *bandwidth*.

## 4. Protokol Komunikasi
* **Lapisan Transport**: TCP (andal, terurut), UDP (cepat, tanpa koneksi), serta QUIC (fondasi HTTP/3 di atas UDP).
* **Lapisan Aplikasi / API**: HTTP/HTTPS sebagai transport untuk arsitektur REST, GraphQL, dan gRPC.

## 5. Sinkronisasi, Logical Clock, & Koordinasi Proses
* **Sinkronisasi Jam**: Setiap node memiliki jam perangkat keras lokal yang rentan bergeser (*clock drift*). Sinkronisasi waktu fisik menggunakan NTP atau PTP (lebih presisi dari NTP) berbasis sumber *Atomic Clock* / GPS.
* **Lamport Logical Clock**: Digunakan untuk menentukan urutan sebab-akibat (*causal order*) tanpa bergantung pada jam fisik:
  * Setiap kejadian lokal menaikkan nilai *counter* lokal sebesar $+1$.
  * Saat mengirim pesan, nilai *counter* disertakan.
  * Saat menerima pesan, penerima membandingkan *counter* lokal dengan *counter* pesan, mengambil nilai maksimum, lalu menambahkannya dengan $+1$ ($\max(L_{lokal}, L_{pesan}) + 1$).
  * *Pengembangan lanjutan*: Vector Clocks, Plausible Clocks, Interval Tree Clocks, dan Blum Clocks.
* **Koordinasi & Isu Terkini**: Menggunakan layanan koordinasi seperti Apache ZooKeeper, etcd, Consul, atau KRaft. Tren saat ini mencakup *Edge/Fog Computing*, *Observability/Telemetry*, dan orkestrasi kontainer (Kubernetes).
