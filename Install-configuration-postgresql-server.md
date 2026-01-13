pada tahap awal, instalasi dan konfigurasi database server postgreSQL sangat penting untuk menentukan level database server. pada sesi ini saya akan instalasi postgreSQL versi yang tersedia di os linux ubuntu dan mengkonfigurasinya untuk kebutuhan database server level non-produksi/testing.



1. import repository signing key dari laman/website postgresql linux download, bertujuan untuk:
   - memverifikasi, package instalasi postgresql di os linux murni dari postgresql. menghindari peretasan system.

   <img width="1575" height="304" alt="Screenshot (380)" src="https://github.com/user-attachments/assets/753c57b4-40b6-497c-9292-f19d8fc43a41" />


2. mmebuat repositori file konfigurasi yang bisa di copy dan paste dari laman/website postgresql linux, bertujuan untuk:
   - mendapatkan akses untuk install postgresql versi terbaru
   - mendapatkan dukungan jangka panjang
   - memudahkan instalasi tambahan seperti postGIS, pgrouting, dan tools administratis lainnya yeng versinya sinkron dengan versi database server.
     
   <img width="1691" height="206" alt="Screenshot (382)" src="https://github.com/user-attachments/assets/d27de684-303f-4340-a82e-8e927a0607dc" />


3. update repositori os
   sudo apt-get update


4. cek versi postgresql yang tersedia di os linux
   
   <img width="1130" height="730" alt="Screenshot (383)" src="https://github.com/user-attachments/assets/ba8755bb-4a57-459d-8492-a21ff093d59f" />


6. cek secara detail versi postgresql yang tersedia di server linux
   
   <img width="1690" height="584" alt="Screenshot (384)" src="https://github.com/user-attachments/assets/ee4d33a2-cd65-4bd0-b78c-e5bcaa5932b2" />


7. install postgresql yang tersedia
   
   <img width="1629" height="330" alt="Screenshot (385)" src="https://github.com/user-attachments/assets/ee83b5a1-f7cd-4643-9392-d9d53e559806" />


8. cek status postgresql yang baru saja di install
   
   <img width="1284" height="185" alt="Screenshot (386)" src="https://github.com/user-attachments/assets/879ac832-5bc8-4a15-b555-725ddac5b00b" />


9. cek versi postgresql yang terinstall, memastikan versi postgresql sesuai dengan versi yang tersedia di server linux.
   
   <img width="704" height="65" alt="Screenshot (387)" src="https://github.com/user-attachments/assets/87664585-e643-4cd9-9e88-1c5ce5977fa7" />


10. login ke dalam database server menggunakan user root (postgres). secara deafult, user root postgresql belum memiliki password atau keamanan level tinggi.
    
   <img width="1245" height="116" alt="Screenshot (388)" src="https://github.com/user-attachments/assets/1f27f70e-c54d-43d4-b214-23418e9ecc51" />


11. membuat password/security untuk user root/postgres sebagai user local dengan mengubah konfigurasi pg_hba.conf sebagai host based authentification
    - sudo vim /etc/postgresql/16/main/pg_hba.conf
      
      <img width="1329" height="322" alt="Screenshot (389)" src="https://github.com/user-attachments/assets/c2b3a2dd-d7b2-4e91-86f8-6222437f9960" />
      - scram-sha-256 : mmebuat/meminta password yang terenksipsi
      - trust : mengizinkan user login ke dalam database server tanpa izin password/security
      - reject : menolak koneksi secara mentah-mentah
      - peer : mengandalkan user login di system
    memastikan semua user yang terdapat di dalam host based authetification terverifikasi keamanan.

12. restart system postgresql
    - sudo systemctl restart postgresql


13. cek dan memastikan sudah tidak bisa login ke dalam database server tanpa password
    
    <img width="1301" height="112" alt="Screenshot (390)" src="https://github.com/user-attachments/assets/67b241a0-57b1-4dfc-a105-08ee06a75481" />
    - karena sebelum mengkonfigurasi hba.conf user root/postgres belum membuat password, maka tidak bisa login ke dalam database server menggunakan password apapun

15. memberikan izin untuk user root masuk ke dalam database server tanpa password, kemudian membuat password dan menguncinya kembali.
    
    <img width="1321" height="346" alt="Screenshot (391)" src="https://github.com/user-attachments/assets/9d54caa7-807b-4956-b31b-65b2617991f8" />
    <img width="1372" height="245" alt="Screenshot (392)" src="https://github.com/user-attachments/assets/f63a6eb9-ecd4-4afc-bf86-382b39344852" />
    <img width="1227" height="343" alt="Screenshot (393)" src="https://github.com/user-attachments/assets/cee09b7a-92d7-4711-a822-158dacfa640e" />
    <img width="846" height="151" alt="Screenshot (394)" src="https://github.com/user-attachments/assets/f84344f2-a36d-4764-a17c-434cc9045362" />


16. mengaktifkan beberapa parameter di dalam konfigurasi postgresql utama untuk menentukan variable atau perilaku database server di level non-produksi/testing.
    - sudo vim /etc/postgresql/16/main/postgresql.conf

      <img width="1561" height="833" alt="Screenshot (395)" src="https://github.com/user-attachments/assets/4606381b-adc9-4f6e-b07d-9c3480657600" />




    
 

    








