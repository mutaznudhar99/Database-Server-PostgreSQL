Sesi ini akan mendemonstrasikan prosedur Switchover pada arsitektur Streaming Replication (Physical Replication). Tujuannya adalah melakukan transisi peran antara server Primary dan Standby secara terencana guna keperluan pemeliharaan server tanpa kehilangan data (zero data loss). Proses ini melibatkan sinkronisasi terakhir (checkpoint), penghentian layanan Primary secara bersih, dan mempromosikan Standby menjadi Primary baru. Skalabilitas baca akan dicapai dengan mengarahkan trafik read-only ke node yang sedang berperan sebagai Standby.


hal yang dipersipkan:
- 1 server primary
- 1 server standby
- database sebagai bahan testing switchover
- archive WAL files 
- pg_basebackup





1. memastikan kedua server sudah terinstall postgresql dengan versi yang sama

   <img width="576" height="62" alt="Screenshot (483)" src="https://github.com/user-attachments/assets/037a5a32-6019-4c18-8e38-f0c929b2d214" />
   
   <img width="540" height="56" alt="Screenshot (499)" src="https://github.com/user-attachments/assets/36f524fd-ce2d-4e78-924a-c9c173347ebe" />



2. konfigurasi postgresql.conf server primary
   - mengaktifkan listen_address, untuk mendengarkan(listening) koneksi masuk dari luar jaringan atau server tersebut.

     sudo vim /etc/postgresql/16/main/postgresql.conf
     <img width="862" height="148" alt="Screenshot (484)" src="https://github.com/user-attachments/assets/5d9d890e-b5dd-45cf-a8c0-a60008207ab9" />


3. membuat database, table, dan input beberapa data ke dalam table sebagai bahan testing migrasi data

   <img width="1395" height="632" alt="Screenshot (487)" src="https://github.com/user-attachments/assets/2dfb4acd-c84c-4be1-8751-901742f669e7" />



4. membuat user dengan hak akses replikasi untuk menjalankan protokol replikasi postgresql
   - user ini yang akan menarik file data dari server primary ke server standby

     <img width="923" height="121" alt="Screenshot (485)" src="https://github.com/user-attachments/assets/ddd75831-382c-4cb2-bb06-5761a551a2ff" />


5. konfigurasi pg_hba.conf sebagai based authentification postgresql untuk menerima akses dari jaringan luar.
   - input user dengan hak akses replikasi dan input ip address server standby

     <img width="1362" height="117" alt="Screenshot (486)" src="https://github.com/user-attachments/assets/9d876802-3982-4694-8e84-1004d025d534" />


6. restart system postgresql server primary untuk memberlakukan perubahan pada file konfigurasi
   - sudo systemctl restart postgresql


8. selanjutnya ke server standby, stop system postgresql server standby
   - sudo systemctl stop postgresql


9. backup file data di server standby ke directory lain dan hapus directory file data lama untuk diisi oleh file data dari server primary

   <img width="1043" height="122" alt="Screenshot (489)" src="https://github.com/user-attachments/assets/cf04c35f-fe67-4705-b999-f230806e088c" />


10. mengambil file data server primary ke server standby menggunakan pg_basebackup dan user replikasi

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


11. ubah kepemilikan file data postgresql di server standby dari root ke postgres, agar bisa akses masuk ke dalam database.

    <img width="879" height="98" alt="Screenshot (491)" src="https://github.com/user-attachments/assets/9cfd1e10-d059-46e2-b5f8-b402cf299c36" />


12. start system postgresql server standby


13. cek status replikasi server primary dan standby
    
    server primary
    <img width="596" height="866" alt="Screenshot (492)" src="https://github.com/user-attachments/assets/c2704e80-864c-4c03-b49c-47f3b20f124c" />

    server standby
    <img width="1684" height="581" alt="Screenshot (494)" src="https://github.com/user-attachments/assets/8851f28a-49c7-4226-9308-34a1592bc676" />


14. cek database dan data pada table server primary dan standby
    - memastikan konsistensi data

      <img width="1717" height="654" alt="Screenshot (497)" src="https://github.com/user-attachments/assets/1bfca8ac-b065-470a-8752-4c6b082a2b0f" />

      <img width="1415" height="490" alt="Screenshot (498)" src="https://github.com/user-attachments/assets/c51acb18-a2d4-4c6f-9105-3b65493ab033" />


15. memastikan server standby dalam keadaan read-only dan recovery

    <img width="802" height="225" alt="Screenshot (500)" src="https://github.com/user-attachments/assets/14118ff2-1b62-4c50-a01f-547d87d18f45" />
    - pg_is_in_recovery (t) : database dalam mode recovery atau read-only


16. mempromosikan server standby menjadi server primary
    - stop system postgresql server primary, hal ini akan berpengaruh pada waktu downtime
    - login ke dalam database server standby dan melakukan **pg_promote();** kemudian cek status replikasi

      <img width="741" height="181" alt="Screenshot (501)" src="https://github.com/user-attachments/assets/5a71503e-cf96-41d0-95cf-3b4354dfe2d7" />

      <img width="769" height="281" alt="Screenshot (502)" src="https://github.com/user-attachments/assets/4fb3080b-45ca-4431-ad9c-d47776b6a43a" />
      - pg_is_in_recovery (f) : database server bukan lagi sebagai server standby


17. memastikan database bisa melakukan mode read-write seletah dipromosi menjadi server primary

    <img width="1415" height="439" alt="Screenshot (503)" src="https://github.com/user-attachments/assets/bcc10aab-2317-4f72-bbbc-559b9c401982" />











   






