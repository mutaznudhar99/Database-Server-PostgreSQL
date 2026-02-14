Pada sesi ini, saya akan mendemonstrasikan prosedur switchover pada arsitektur physical streaming replication. Bertujuan melakukan transisi role (role reversal) antara server primary dan standby secara terencana untuk keperluan maintenance server dengan target zero data loss. Proses ini mencakup sinkronisasi final log transaksi (WAL), stop primary server service, dan promote server standby menjadi new primary untuk menjamin high availability


hal yang dipersipkan:
- Infrastructure : 1 server primary & 1 server standby (OS & spek identik)
- Software  : PostgreSQL v16 (versi mayor identik)
- Tools     : pg_basebackup & pg_promote
- WAL archiving dan replication slots





1. Verifikasi kedua server meng-install PostgreSQL dengan versi mayor dan minor yang identik untuk menghindari ketidaksesuaian format data biner selama proses streaming

   <img width="576" height="62" alt="Screenshot (483)" src="https://github.com/user-attachments/assets/037a5a32-6019-4c18-8e38-f0c929b2d214" />
   <img width="540" height="56" alt="Screenshot (499)" src="https://github.com/user-attachments/assets/36f524fd-ce2d-4e78-924a-c9c173347ebe" />



2. Konfigurasi postgresql.conf pada server primary dengan set parameter listen_addresses. Memastikan server dapat menerima request replikasi dari node standby melalui network

     <img width="862" height="148" alt="Screenshot (484)" src="https://github.com/user-attachments/assets/5d9d890e-b5dd-45cf-a8c0-a60008207ab9" />


3. Membuat data sample dengan inisialisasi database dan struktur tabel pada server primary sebagai objek validasi physical replication

   <img width="1395" height="632" alt="Screenshot (487)" src="https://github.com/user-attachments/assets/2dfb4acd-c84c-4be1-8751-901742f669e7" />


4. Crete user dengan atribut REPLICATION. Bertindak sebagai entitas legal untuk melakukan penarikan data biner (WAL streaming) dari server primary ke standby

     <img width="923" height="121" alt="Screenshot (485)" src="https://github.com/user-attachments/assets/ddd75831-382c-4cb2-bb06-5761a551a2ff" />


5. Konfigurasi pg_hba.conf. Memberikan izin akses untuk IP standby. Mengamankan jalur replikasi agar hanya bisa diakses oleh IP yang terdaftar

     <img width="1362" height="117" alt="Screenshot (486)" src="https://github.com/user-attachments/assets/9d876802-3982-4694-8e84-1004d025d534" />


6. Melakukan stop service dan backup direktori data (PGDATA) pada server standby untuk menerima salinan data biner dari server primary

   <img width="1043" height="122" alt="Screenshot (489)" src="https://github.com/user-attachments/assets/cf04c35f-fe67-4705-b999-f230806e088c" />


7. Menjalankan physical base backup pada server standby untuk menarik data biner database primary menggunakan pg_basebackup utility

   <img width="1427" height="267" alt="Screenshot (490)" src="https://github.com/user-attachments/assets/44aae29c-171e-45f6-84ae-53f960bbab1d" />
   - h : menentukan sumber aliran data(stream)
   - D : menentukan lokasi file data di server standby
   - U : user sebagai perantara untuk membuka akses aliran data dari server primary ke server standby
   - P : progess bar untuk mengetahui berapa persen data terkirim
   - v : verbose memberikan rincian detail saat melakukan stream data
   - R : membuat file standby.signal dan primary.conninfo secara otomatis untuk menentukan bahwa server tersebut merupakan server standby
   - X stream : memastikan log transaksi(WAL) dikirim secara real-time saat stream data berlangsung. untuk menjaga konsistensi data antara server primary dan standby
   - C : membuat slot sebagai anchor yang menahan file WAL untuk tidak dihapus oleh prosess checkpointer di server primary. menjaga konsistensi data pada server standby apabila mengalami downtime atau error.
   - S : nama slot


8. Melakukan permission adjustment pada direktori PGDATA (data biner) melalui perintah chown ke user postgres agar proses recovery data dapat berjalan tanpa kendala hak akses sistem operasi

    <img width="879" height="98" alt="Screenshot (491)" src="https://github.com/user-attachments/assets/9cfd1e10-d059-46e2-b5f8-b402cf299c36" />


9. Menjalankan service PostgreSQL server standby untuk memproses salinan data biner (PGDATA) database primary berjalan pada database server standby


10. Verifikasi status replikasi pada kedua server. Memastikan status primary server streaming (via pg_stat_replication), dan server standby mode recovery
    
    server primary
    <img width="596" height="866" alt="Screenshot (492)" src="https://github.com/user-attachments/assets/c2704e80-864c-4c03-b49c-47f3b20f124c" />

    server standby
    <img width="1684" height="581" alt="Screenshot (494)" src="https://github.com/user-attachments/assets/8851f28a-49c7-4226-9308-34a1592bc676" />


11. Validasi objek database dan struktur table. Memastikan data standby selaras dengan primary untuk menjamin data integrity

      <img width="1717" height="654" alt="Screenshot (497)" src="https://github.com/user-attachments/assets/1bfca8ac-b065-470a-8752-4c6b082a2b0f" />

      <img width="1415" height="490" alt="Screenshot (498)" src="https://github.com/user-attachments/assets/c51acb18-a2d4-4c6f-9105-3b65493ab033" />


12. Validasi status database menggunakan fungsi pg_is_in_recovery(). Memastikan standby dalam mode read-only

    <img width="802" height="225" alt="Screenshot (500)" src="https://github.com/user-attachments/assets/14118ff2-1b62-4c50-a01f-547d87d18f45" />
    - pg_is_in_recovery (t) : Database dalam mode recovery atau read-only


13. Mensimulasikan switchover operasional database dengan stop service primary. Kemudian menjalankan perintah pg_promote() pada db standby untuk mempromosikan standby menjadi new primary database (read-write)
    - stop system postgresql primary, hal ini akan berpengaruh pada waktu downtime

      <img width="741" height="181" alt="Screenshot (501)" src="https://github.com/user-attachments/assets/5a71503e-cf96-41d0-95cf-3b4354dfe2d7" />

      <img width="769" height="281" alt="Screenshot (502)" src="https://github.com/user-attachments/assets/4fb3080b-45ca-4431-ad9c-d47776b6a43a" />
      - pg_is_in_recovery (f) : Database server tidak lagi dalam keadaan recovery (new primary)

14. Validasi new primary database dapat melakukan operasional read/write

    <img width="1415" height="439" alt="Screenshot (503)" src="https://github.com/user-attachments/assets/bcc10aab-2317-4f72-bbbc-559b9c401982" />











   






