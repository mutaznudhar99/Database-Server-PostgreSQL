Pada sesi kali ini, saya akan melakukan migrasi data lintas versi mayor (PostgreSQL 14 ke 16) menggunakan Logical Replication. Metode ini dipilih untuk mencapai aplikasi near-zero downtime dan zero data loss. Logical replication hanya mengirim data pada table seperti proses Insert, Update, dan Delete. Mengimplementasikan model Publication dan Subscription, dapat memastikan kelangsungan operasional bisnis tetap berjalan selama proses transisi data berlangsung.

hal yang dipersiapkan:
- 1 server publication versi 14
- 1 server subscription versi 16
- Table yang memiliki primary key atau unique key



1. install postgresql versi 14 sebagai source db

   <img width="596" height="79" alt="Screenshot (590)" src="https://github.com/user-attachments/assets/c62f32dd-effd-45f6-ac17-a51a1bca9032" />


2. konfigurasi postgresql.conf untuk membuat variable logical replication
   - listen_address : '*'
   - port : 5432
   - wal_replica : logical

     <img width="893" height="119" alt="Screenshot (595)" src="https://github.com/user-attachments/assets/a24c3ead-375c-4e82-91c1-a43d5bf33199" />
     <img width="915" height="122" alt="Screenshot (594)" src="https://github.com/user-attachments/assets/cd1b73c6-164b-4634-abec-01480248362f" />


3. membuat database, table, dan input data ke dalam table sebagai sample migrasi data

   <img width="1056" height="406" alt="Screenshot (596)" src="https://github.com/user-attachments/assets/038d7a8c-3d37-4338-980b-06c14e931adc" />


4. membuat publication sebagai konfigurasi logical replication di source server untuk menentukan data mana (table) yang akan dikirim menuju target server.

   <img width="953" height="303" alt="Screenshot (599)" src="https://github.com/user-attachments/assets/b4625bf7-e889-45c6-bac3-38943fcb6677" />
   - bisa mengirim 1 table, multiple table, atau semua table.


5. Membuat User replikasi sebagai otoritas untuk mengakses, membaca, dan mengirimkan data dari source erver ke server target selama proses migrasi berlangsung.

   <img width="850" height="277" alt="Screenshot (600)" src="https://github.com/user-attachments/assets/eae370ae-4998-4ac6-901a-20e02cf698dc" />


6. Konfigurasi pg_hba.conf sebagai Host-Based Authentication untuk memberikan izin akses koneksi dari server Target ke server Source secara aman.
   - input user replikasi ke dalam pg_hba.conf yang bertugas sebagai otoritas untuk menarik data dari source server ke server target

     <img width="908" height="162" alt="Screenshot (601)" src="https://github.com/user-attachments/assets/dc249817-fed4-40f5-a5d1-1c1b4594cc91" />


7. restart system postgresql versi 14


8. install postgresql versi 16 sebagai target server

   <img width="667" height="91" alt="Screenshot (602)" src="https://github.com/user-attachments/assets/d0af4783-d2e2-4766-be02-f0a8d0f4246c" />


9. Melakukan inisialisasi database pada server Target, serta membuat struktur tabel (DDL) yang identik dengan skema pada source server

   <img width="1591" height="211" alt="Screenshot (603)" src="https://github.com/user-attachments/assets/d540a207-d8bd-40af-82a1-7884811b5071" />


10. Membuat Subscription untuk membangun koneksi ke source server dan memulai sinkronisasi data dari Publication secara real-time

    <img width="1629" height="227" alt="Screenshot (608)" src="https://github.com/user-attachments/assets/e6ed36ff-b5b7-4364-acca-8852364ca0aa" />


11. verifikasi status replikasi

    <img width="737" height="612" alt="Screenshot (604)" src="https://github.com/user-attachments/assets/2992824c-f547-4a4a-a0d6-cf7d78884a81" />
    <img width="803" height="416" alt="Screenshot (605)" src="https://github.com/user-attachments/assets/0fc2c80c-f9e0-437d-9523-a38cc312bc70" />
    - Memastikan koordinat LSN tersinkronisasi sepenuhnya antara server Source dan Target untuk menjamin integritas data (Zero Data Loss)


12. verifikasi data pada table di target server

    <img width="690" height="279" alt="Screenshot (606)" src="https://github.com/user-attachments/assets/471a9359-19a4-441d-95cf-852e4920da25" />


    
##Meskipun implementasi Logical Replication telah berhasil divalidasi pada lingkungan testing, transisi ke lingkungan produksi memerlukan analisis mendalam terkait manajemen workload, monitoring replication lag, serta strategi sinkronisasi sequence.










