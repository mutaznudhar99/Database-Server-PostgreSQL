Pada sesi kali ini, saya akan mendemonstrasikan proses migrasi data lintas versi mayor dari PostgreSQL 14 ke PostgreSQL 16 menggunakan metode Logical Replication. Strategi ini dipilih untuk mitigasi risiko operasional dengan target near-zero downtime & zero data loss. Melalui model Publication dan Subscription, dapat mereplikasi perubahan data (INSERT, UPDATE, DELETE) secara real-time, memungkinkan transisi sistem yang smooth tanpa stop continuity transaksi

hal yang dipersiapkan:
- Source server  : PostgreSQL v14 (publication)
- Target server  : PostgreSQL v16 (subscription)
- Data integrity : Tabel yang memiliki primary key atau unique key sebagai row identity untuk proses replikasi


1. Instalasi PostgreSQL versi 14 pada source server sebagai database primary yang akan dimigrasikan

   <img width="596" height="79" alt="Screenshot (590)" src="https://github.com/user-attachments/assets/c62f32dd-effd-45f6-ac17-a51a1bca9032" />


2. Konfigurasi postgresql.conf dengan penyesuaian parameter wal_level menjadi logical agar database mencatat detail transaksi yang dibutuhkan untuk proses replikasi logis
   - listen_address : '*'
   - port    : 5432
   - wal_replica : logical

     <img width="893" height="119" alt="Screenshot (595)" src="https://github.com/user-attachments/assets/a24c3ead-375c-4e82-91c1-a43d5bf33199" />
     <img width="915" height="122" alt="Screenshot (594)" src="https://github.com/user-attachments/assets/cd1b73c6-164b-4634-abec-01480248362f" />


3. Membuat sampel data sebagai baseline logical replication data pada source server untuk divalidasi pada akhir proses migrasi

   <img width="1056" height="406" alt="Screenshot (596)" src="https://github.com/user-attachments/assets/038d7a8c-3d37-4338-980b-06c14e931adc" />


4. Create publication object pada source server. Menentukan data spesific (tabel spesifik atau seluruh tabel) yang akan direplikasi ke server target

   <img width="953" height="303" alt="Screenshot (599)" src="https://github.com/user-attachments/assets/b4625bf7-e889-45c6-bac3-38943fcb6677" />
   - Publication dapat mereplikasi satu table, multiple table, atau semua table


5. Create replication user sebagai otoritas untuk mengakses, membaca, dan mengirimkan data dari source server ke server target selama proses migrasi berlangsung

   <img width="850" height="277" alt="Screenshot (600)" src="https://github.com/user-attachments/assets/eae370ae-4998-4ac6-901a-20e02cf698dc" />


6. Konfigurasi pg_hba.conf (Host-Based Authentication). Mengizinkan koneksi dari IP target server menggunakan user replikasi sebagai otorisasi monitoring proses migrasi
   - input user replikasi ke dalam pg_hba.conf yang bertugas sebagai otoritas untuk menarik data dari source server ke server target

     <img width="908" height="162" alt="Screenshot (601)" src="https://github.com/user-attachments/assets/dc249817-fed4-40f5-a5d1-1c1b4594cc91" />


7. Instalasi PostgreSQL versi 16 pada environment server target sebagai destinasi migrasi data modern

   <img width="667" height="91" alt="Screenshot (602)" src="https://github.com/user-attachments/assets/d0af4783-d2e2-4766-be02-f0a8d0f4246c" />


8. Melakukan inisialisasi database pada target server sebagai lokasi storage data dan membuat struktur tabel (DDL) yang identik dengan skema pada source server

   <img width="1591" height="211" alt="Screenshot (603)" src="https://github.com/user-attachments/assets/d540a207-d8bd-40af-82a1-7884811b5071" />
   - Logical replication hanya mereplikasi data (DML), bukan struktur skema (DDL)

9. Create subscription object pada target server. Memicu koneksi ke source server dan memulai sinkronisasi data awal (initial snapshot) diikuti replikasi transaksi secara real-time

    <img width="1629" height="227" alt="Screenshot (608)" src="https://github.com/user-attachments/assets/e6ed36ff-b5b7-4364-acca-8852364ca0aa" />


10. Verifikasi status replikasi melalui view sistem table replikasi. Memastikan titik log sequence number (LSN) antara source dan target identik, menjamin data integrity (zero data loss)

    <img width="737" height="612" alt="Screenshot (604)" src="https://github.com/user-attachments/assets/2992824c-f547-4a4a-a0d6-cf7d78884a81" />
    <img width="803" height="416" alt="Screenshot (605)" src="https://github.com/user-attachments/assets/0fc2c80c-f9e0-437d-9523-a38cc312bc70" />
   


11. Validasi data integrity pada tabel di server target. Memastikan semua data rows telah berpindah sesuai dengan original source

    <img width="690" height="279" alt="Screenshot (606)" src="https://github.com/user-attachments/assets/471a9359-19a4-441d-95cf-852e4920da25" />











