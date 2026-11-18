Nama : Francisco G I dc Corbafo
---
Nim : 255410003
---
Materi Minggu 7: Cloud Computing, Virtualisasi, Cloud Native App, dan Praktik Containerized App (Docker & Podman)
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Cloud Computing (Modul 07) beserta panduan praktik membangun Containerized App Flask menggunakan Docker dan Podman dalam format Markdown sesuai agenda perkuliahan.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penjabaran 6 karakteristik NIST, matriks tanggung jawab model layanan (On-Premises, IaaS, PaaS, SaaS), komponen Cloud Native App, serta langkah teknis pembuatan Dockerfile hingga eksekusi di Docker dan Podman.

# Cloud Computing dan Containerized Application

## 1. Pengertian & Karakteristik Cloud Computing
Menurut definisi **NIST**, *Cloud Computing* adalah model yang memungkinkan akses jaringan yang mudah, tersedia di mana saja, dan bersifat *on-demand* terhadap kumpulan sumber daya komputasi yang dapat dikonfigurasi (jaringan, server, penyimpanan, aplikasi) secara cepat dengan upaya manajemen minimal. Lawan dari *cloud* adalah *On-Premise* (infrastruktur lokal) dan *Off-Premise* (*co-location*).

**6 Karakteristik Utama:**
1. *On-demand self-service*: Penyediaan sumber daya secara otomatis tanpa interaksi manusia dengan penyedia.
2. *Broad network access*: Dapat diakses dari berbagai perangkat melalui jaringan standar.
3. *Resource pooling*: Penggabungan sumber daya fisik/virtual untuk melayani banyak pelanggan secara dinamis.
4. *Rapid elasticity*: Penyesuaian kapasitas naik/turun secara cepat dan otomatis sesuai beban permintaan.
5. *Measured service*: Pemantauan dan penagihan berbasis konsumsi aktual (*pay-as-you-go*).
6. *Multitenancy*: Satu *instance* melayani banyak penyewa (*tenant*) dengan isolasi data dan keamanan yang terjaga.

## 2. Model Layanan & Model Deployment
* **Model Layanan (XaaS - Everything as a Service)**:
  * **IaaS (Infrastructure as a Service)**: Penyedia mengelola perangkat keras, jaringan, penyimpanan, dan virtualisasi; pengguna mengelola OS, *runtime*, data, dan aplikasi.
  * **PaaS (Platform as a Service)**: Penyedia mengelola hingga lapisan OS, *middleware*, dan *runtime*; pengembang fokus mengelola aplikasi dan data.
  * **SaaS (Software as a Service)**: Seluruh lapisan dikelola oleh vendor; pengguna akhir langsung memakai aplikasi.
* **Model Deployment**: *Public Cloud*, *Private Cloud (On-Premise)*, *Hybrid Cloud*, dan *Community Cloud*.

## 3. Arsitektur, Scalability, dan Redundancy
* **Arsitektur Cloud**: Terdiri atas lapisan *Front End* (antarmuka pengguna) yang terhubung melalui internet ke *Back End* (Aplikasi, Layanan, *Cloud Runtime*, *Storage*, dan *Infrastructure* yang dilindungi oleh sistem *Management* dan *Security*).
* **Scalability vs Elasticity**: *Scalability* adalah kemampuan menangani kenaikan/penurunan kapasitas sumber daya secara terencana/statis (mencakup *Vertical Scaling* / menambah spesifikasi server, *Horizontal Scaling* / menambah jumlah server, dan *Diagonal Scaling*), sedangkan *Elasticity* adalah penyesuaian kapasitas secara dinamis dan otomatis.
* **Redundancy**: Duplikasi data, server, dan konfigurasi lintas node/zona untuk mencegah hilangnya data akibat kegagalan satu titik (contoh: AWS Backup, Azure Backup, serta *distributed database* seperti PostgreSQL, Cassandra, YugabyteDB).

## 4. Virtualisasi, Container, dan Serverless
* **Virtualisasi**: Abstraksi perangkat keras fisik menggunakan *Hypervisor* (VMM) untuk menjalankan beberapa sistem operasi tamu (VM), baik melalui arsitektur *Hosted* maupun *Bare-Metal*.
* **Teknologi Container**: Mengemas aplikasi beserta dependensinya dalam unit terisolasi yang berbagi *kernel* OS host secara ringan (mengikuti standar *Open Container Initiative* / OCI dengan runtime seperti `runc`, dikelola via Docker atau Podman).
* **Serverless (Komputasi Nirserver)**: Model eksekusi di mana penyedia cloud mengalokasikan, menjalankan, dan menjeda server secara otomatis berdasarkan beban kerja dan menagih per detik penggunaan komputasi (contoh: AWS Lambda, Azure SQL Serverless).

## 5. Cloud Native App (CNA)
**Cloud Native App** adalah pendekatan membangun aplikasi yang memanfaatkan penuh elastisitas dan sifat terdistribusi lingkungan cloud agar sistem bersifat *loosely-coupled*, tangguh (*resilient*), mudah dikelola, dan *observable*.
* **Elemen Utama CNA**: Arsitektur *Microservices*, *Containers* & orkestrasi (Kubernetes), kultur *DevOps*, otomatisasi *CI/CD* (*Continuous Integration / Continuous Delivery*), API deklaratif, serta *Service Mesh*.

## 6. Ringkasan Praktik: Membangun Containerized App (Docker & Podman)
Studi kasus mengemas aplikasi blog **Flask** (`flaskr`) menggunakan Python 3.14 menjadi *Containerized App*:
1. **Persiapan Aplikasi**: Mengkloning tutorial Flask (`examples/tutorial`), membuat *virtual environment* Python 3.14.4 dengan `uv`, menginstal paket (`uv pip install -e .`), dan menginisialisasi database SQLite (`flask --app flaskr init-db`).
2. **Pembuatan `Dockerfile`**:
   ```dockerfile
   FROM python:3.14
   RUN mkdir /app
   WORKDIR /app
   ADD . /app/
   RUN pip install -e .
   RUN flask --app flaskr init-db
   EXPOSE 5000
   CMD ["flask", "--app", "flaskr", "run", "--host=0.0.0.0", "--port=5000"]
