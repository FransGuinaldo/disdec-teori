Nama : Francisco G I dc Corbafo
---
NIM : 255410003
---
Materi Minggu 6: Distributed File System (DFS), Cloud Storage, dan Praktik Apache Hadoop HDFS
AI yang digunakan: Gemini

Prompt: Buatkan rangkuman materi Distributed File System dan Cloud Storage (Modul 06) beserta langkah-langkah instalasi dan konfigurasi Apache Hadoop HDFS 3.5.0 di Linux dalam format Markdown.
Bagian yang disesuaikan dari keluaran prompt ada pada: Penambahan tabel perbandingan tipe penyimpanan (Block, File, Object Storage), integrasi teori modul 06--distributed-filesystem.pdf dengan langkah konfigurasi XML Hadoop dari dokumen menggunakan-hadoop-hdfs--linux.pdf.

# Distributed File System (DFS) dan Cloud Storage

## 1. Pengertian File System & Distributed File System (DFS)
* **File System (FS)**: Metode sistem operasi dalam mengelola dan mengatur berkas pada media penyimpanan melalui *driver* kernel dan *Virtual File System* (contoh: `ext4`, `XFS`, `btrfs` di Linux; `NTFS` di Windows; `APFS` di macOS). Hirarki direktori di UNIX/Linux mengikuti standar *Filesystem Hierarchy Standard* (FHS).
* **Distributed File System (DFS)**: Abstraksi terkoordinasi dari sekumpulan media penyimpanan di banyak mesin/node yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga tampak bagi pengguna sebagai satu ruang penyimpanan tunggal (*Global Namespace*).
* **Perbedaan Arsitektur Penyimpanan**:
  * **DAS (Direct-Attached Storage)**: Penyimpanan terpasang langsung ke satu komputer lokal.
  * **NAS (Network-Attached Storage)**: Penyimpanan berbasis *file-level* yang diakses melalui jaringan standar (NFS/SMB).
  * **SAN (Storage Area Network)**: Penyimpanan berbasis *block-level* berkecepatan tinggi melalui jaringan khusus (FC/iSCSI).
  * **DFS**: Abstraksi perangkat lunak yang mendistribusikan file dan direktori ke banyak node (umumnya memanfaatkan DAS pada tiap node).

## 2. Jenis DFS & Studi Kasus HDFS
* **Contoh DFS Populer**: Google File System (GFS) & Colossus, CephFS, GlusterFS, Lustre, dan **HDFS** (*Hadoop Distributed File System*).
* **Arsitektur HDFS**: Dirancang untuk menyimpan dataset besar pada *commodity hardware* dengan memecah berkas menjadi blok-blok yang direplikasi ke banyak node:
  * **NameNode (Master)**: Mengelola metadata sistem berkas (nama file, direktori, lokasi blok, dan jumlah replika).
  * **DataNode (Slave)**: Menyimpan blok data aktual dan melayani operasi baca/tulis dari klien serta replikasi antar-rak.

## 3. Cloud Storage dan Tipenya
**Cloud Storage** adalah model penyimpanan data jarak jauh yang dikelola dalam *logical pool* dan diakses melalui internet. DFS sering bertindak sebagai lapisan pengorganisasi di atas Cloud Storage (misalnya HDFS mengakses Amazon S3 melalui konektor `s3a`).

| Tipe Storage | Transport | Antarmuka / Protokol | Karakteristik & Kegunaan Utama |
| :--- | :--- | :--- | :--- |
| **Block Storage** | FC atau iSCSI | Direct Attached / SAN | Latensi sangat rendah, cocok untuk database transaksional & VM. |
| **File Storage** | TCP/IP | NFS, SMB | Performa baik, berbagi berkas terstruktur dengan *global file locking*. |
| **Object Storage** | TCP/IP | HTTP, REST API (S3) | Skalabilitas tanpa batas, ideal untuk data tidak terstruktur di cloud (contoh: Amazon S3, Ceph, CubeFS, RustFS, PGSTY Silo). |

## 4. Ringkasan Praktik: Instalasi & Konfigurasi Apache Hadoop HDFS 3.5.0
1. **Prasyarat Sistem**: JDK 17 (untuk server) / JDK 21 (untuk klien), `pdsh`, serta layanan `ssh`/`sshd` aktif dengan konfigurasi *passwordless SSH* ke `localhost` (`ssh-keygen` dan menyalin `id_rsa.pub` ke `~/.ssh/authorized_keys`).
2. **Instalasi & Environment**: Mengekstrak binary `hadoop-3.5.0.tar.gz`, mengatur variabel `PATH` (`$HADOOP_HOME/bin` dan `sbin`), serta menetapkan `JAVA_HOME` di `$HADOOP_HOME/etc/hadoop/hadoop-env.sh`.
3. **Konfigurasi XML Utama** (di `$HADOOP_HOME/etc/hadoop/`):
   * `core-site.xml`: Menetapkan alamat default HDFS (`fs.defaultFS` = `hdfs://localhost:9000`).
   * `hdfs-site.xml`: Mengatur faktor replikasi (`dfs.replication` = `1` untuk mode *pseudo-distributed*).
   * `mapred-site.xml` & `yarn-site.xml`: Mengatur *framework* YARN dan alokasi memori *NodeManager*.
4. **Inisialisasi & Eksekusi Daemon**:
   * Format NameNode (hanya dilakukan sekali saat instalasi awal): `hdfs namenode -format`
   * Menjalankan layanan HDFS: `start-dfs.sh` (dapat dipantau melalui antarmuka web `http://localhost:9870`).
   * Operasi dasar HDFS: `hdfs dfs -mkdir -p /user/<username>` dan `hdfs dfs -ls /`, lalu menghentikan layanan dengan `stop-dfs.sh`.
