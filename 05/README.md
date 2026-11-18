Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 5: Fault Tolerance dan Praktik Load Balancing dengan Nginx
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Fault Tolerance (Modul 05) beserta panduan praktikum Load Balancing aplikasi Python Blacksheep menggunakan Docker Compose dan Nginx dalam format Markdown.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penggabungan materi teori (05--fault-tolerant-sys.pdf) dan langkah teknis praktikum (web-app-py-blacksheep-load-balancing-nginx.pdf), termasuk konfigurasi nginx.conf, docker-compose.yml, serta contoh pola Circuit Breaker dan Retry.

# Fault Tolerance dan Load Balancing

## 1. Pengertian & Arti Penting Fault Tolerance
**Fault Tolerance** adalah kemampuan sistem untuk tetap mempertahankan operasional yang tepat meskipun terjadi kegagalan atau kesalahan pada satu atau lebih komponennya. Kemampuan ini mutlak diperlukan pada sistem *high-availability*, *mission-critical*, dan *life-critical*.

## 2. Jenis dan Klasifikasi Fault
* **Berdasarkan Sifat/Durasi**:
  * *Transient Fault*: Kegagalan sementara akibat gangguan sesaat (misal: sambaran petir/lonjakan listrik).
  * *Intermittent Fault*: Muncul dan hilang berulang kali (misal: perangkat keras mengalami *overheat*).
  * *Permanent Fault*: Kerusakan permanen hingga komponen diganti/diperbaiki.
  * *Byzantine Fault*: Komponen gagal dengan memberikan hasil acak, tidak konsisten, atau menyesatkan (*traitor*).
* **Berdasarkan Sumber**: *Hardware faults* (*disk crash*, mati listrik), *Software faults* (*bugs*, *memory leak*, *race condition*), dan *Communication faults* (*packet loss*, *network partition*).

## 3. Teknik & Design Patterns Mencapai Fault Tolerance
1. **Redundancy and Replication**: Menduplikasi sumber daya ke berbagai node untuk menghilangkan *single point of failure*.
2. **Load Balancing**: Mendistribusikan trafik permintaan ke beberapa *instance* aplikasi aktif menggunakan *reverse proxy* seperti Nginx atau HAProxy.
3. **Checkpointing and Rollback Recovery**: Menyimpan *state* sistem secara berkala ke penyimpanan permanen (*checkpoint*) agar sistem dapat pulih dari titik terakhir saat terjadi kegagalan tanpa mengulang dari awal.
4. **Heartbeat and Failure Detection**: Pengiriman sinyal periodik (*heartbeat*) antar-node untuk mendeteksi komponen yang mati/tidak responsif.
5. **Retry & Circuit Breaker Pattern**:
   * *Retry* (misal dengan *library* Python `tenacity`): Mencoba ulang koneksi eksternal yang gagal sementara.
   * *Circuit Breaker* (misal dengan *library* `pybreaker`): Bertindak sebagai *state machine* (`Closed`, `Open`, `Half-Open`) untuk memutus sementara panggilan ke layanan yang bermasalah guna mencegah kegagalan berantai (*cascading failure*).
6. **Consensus and Coordination**: Menyepakati status atau pemilihan *leader* di tengah gangguan jaringan (Raft, Paxos).

## 4. Actor Model & Implementasi Bahasa Pemrograman
* **Actor Model**: Unit komputasi independen (*Actor*) yang berkomunikasi lewat pesan asinkron (*Mailbox*) dan diawasi secara hierarkis oleh *Supervisor* (jika *worker actor* gagal, *supervisor* melakukan *restart*).
* **Dukungan Bahasa/Library**: Erlang/Elixir & Pony (bawaan *runtime*), Python (`Xoscar`, `tenacity`, `pybreaker`), dan Java (`Apache Pekko`, `Resilience4j`).

## 5. Ringkasan Praktik: Load Balancing Blacksheep dengan Nginx & Docker
Untuk mencapai *high-availability*, aplikasi web ASGI Python (**Blacksheep**) dijalankan dalam beberapa *instance* di balik **Nginx** sebagai *load balancer*:
1. **Inisialisasi Aplikasi**: Membuat proyek MVC Blacksheep (`blacksheep create`) menggunakan lingkungan Python 3.14.4 (`uv venv`).
2. **Konfigurasi Nginx (`nginx/nginx.conf`)**: Mendefinisikan blok `upstream backend { server bs_app:80; }` dan meneruskan trafik melalui `proxy_pass http://backend;`.
3. **Orkestrasi (`docker-compose.yml`)**: Menjalankan layanan `bs_app` dan `nginx` (mengekspos port `80:80`), lalu melakukan *horizontal scaling* menjadi 2 kontainer aplikasi dengan perintah:
   ```bash
   docker-compose up --build -d --scale bs_app=2

Verifikasi: Ketika http://localhost diakses berulang kali, log kontainer (docker logs) menunjukkan distribusi request HTTP 200 OK dibagi ke load-balancing-bs_app-1 dan load-balancing-bs_app-2.
