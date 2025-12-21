pada sesi kali ini, saya akan melakukan instalasi dan konfigurasi server backup and recover manager (barman) dan server database postgresql, kemudian melakukan backup dan restore menggunakan tools barman sebagai alat pengelola penyimpanan data files dan wal files server postgresql.

kelebihan barman untuk penyimpanan data file postgresql:
- mengambil hot full backup dan archive wal files
- support incremental backup
- support remote backup menggunakan rsync/ssh, untuk bisa mengelola penyimpanan data files lebih dari satu server postgresql (best practice)
- cocok untuk point in time recovery yang lebih fleksibel
- support compression wal file dan backup
- support retention policy, untuk menghapus backup lama secara otomatis
- support restore ke server postgresql berbeda versi minor (16.2 > 16.5)

hal yang dipersiapkan melakukan backup dan restore menggunakan barman:
- 1 server barman
- 1 server postgresql



1. memastikan instalasi barman sudah dilakukan di server individu barman

   <img width="1124" height="112" alt="Screenshot (425)" src="https://github.com/user-attachments/assets/17210318-c424-4e82-8c79-18f53c29f26f" />


2. membuat file archive WAL direktori di penyimpanan data postgresql
   
   <img width="1217" height="568" alt="Screenshot (426)" src="https://github.com/user-attachments/assets/080274de-88d9-40aa-9a2d-d9760679c31b" />


4. konfigurasi database server postgresql sebagai target barman
   - sudo vim /etc/postgresql/16/main/postgresql.conf
     listen_adress = *
     wal_level = replica
     archive_mode = on
     archive_command = ‘test ! -f /var/lib/postgresql/16/main/wal_archive/%f && cp %p                                   var/lib/postgresql/16/main/wal_archive/%f’
     max_wal_senders=10
     wal_keep_size=1024


5. membuat user barman dan konfigurasi postgresql autentifikasi

   


6. menambahkan kunci autentifikasi postgresql dan mendaftarkannya ke repositori postgresql

   <img width="1679" height="463" alt="Screenshot (421)" src="https://github.com/user-attachments/assets/3b920671-b9a3-4bf2-93dd-9e113e25f888" />
   - bertujuan untuk mendaftarkan software resmi ke dalam os linux


7. update server dan install barman

   <img width="978" height="100" alt="Screenshot (422)" src="https://github.com/user-attachments/assets/3943e251-ce4c-47f7-8b09-4ca828bce5b6" />


8. membuat kunci keamanan untuk menggunakan barman terhubung ke database server tanpa akses password
   - membuat backup tanpa perlu input password
   - lebih aman dari membuat password biasa
   - mempermudah koneksi dengan database server

     <img width="1238" height="550" alt="Screenshot (423)" src="https://github.com/user-attachments/assets/6ce62374-045c-4d3b-b737-a590d5635e8d" />


9. 

   


   



     
