Pada sesi ini, saya akan mendemonstrasikan proses deployment database PostgreSQL dengan fokus pada otentikasi berbasis host (HBA) dan security hardening untuk lingkungan testing



1. Integrasi repositori dan signing key. Memastikan paket yang di-install adalah autentik dan terverifikasi

   <img width="1575" height="304" alt="Screenshot (380)" src="https://github.com/user-attachments/assets/753c57b4-40b6-497c-9292-f19d8fc43a41" />


2. Mendaftarkan repositori resmi PostgreSQL ke dalam sistem Ubuntu. Memungkinkan instalasi versi PostgreSQL yang tidak tersedia di repositori default Ubuntu
     
   <img width="1691" height="206" alt="Screenshot (382)" src="https://github.com/user-attachments/assets/d27de684-303f-4340-a82e-8e927a0607dc" />


3. Melakukan sinkronisasi indeks paket dan memilih versi spesifik mesin database PostgreSQL untuk diinstal
   
   <img width="1130" height="730" alt="Screenshot (383)" src="https://github.com/user-attachments/assets/ba8755bb-4a57-459d-8492-a21ff093d59f" />   
   <img width="1690" height="584" alt="Screenshot (384)" src="https://github.com/user-attachments/assets/ee4d33a2-cd65-4bd0-b78c-e5bcaa5932b2" />


4. Melakukan instalasi mesin database PostgreSQL berdasarkan versi spesifik untuk diinstal
   
   <img width="1629" height="330" alt="Screenshot (385)" src="https://github.com/user-attachments/assets/ee83b5a1-f7cd-4643-9392-d9d53e559806" />


5. Verifikasi service database aktif melalui systemctl status postgresql dan mengecek versi binary yang terpasang
   
   <img width="1284" height="185" alt="Screenshot (386)" src="https://github.com/user-attachments/assets/879ac832-5bc8-4a15-b555-725ddac5b00b" />   
   <img width="704" height="65" alt="Screenshot (387)" src="https://github.com/user-attachments/assets/87664585-e643-4cd9-9e88-1c5ce5977fa7" />


6. Set kebijakan akses database pada level jaringan dan protokol. Memastikan superuser memiliki password policy untuk bisa masuk ke dalam database server
      
      <img width="1329" height="322" alt="Screenshot (389)" src="https://github.com/user-attachments/assets/c2b3a2dd-d7b2-4e91-86f8-6222437f9960" />
      - scram-sha-256 : membuat/meminta password yang terenksipsi
      - trust         : mengizinkan user login ke dalam database server tanpa izin password/security
      - reject        : menolak koneksi secara mentah-mentah
      - peer          : mengandalkan user login di system
    memastikan semua user yang terdapat di dalam host based authetification terverifikasi keamanan.


7. Validasi aksesbilitas superuser gagal melakukan login ke dalam database server karena protocol host-based authentification
    
    <img width="1301" height="112" alt="Screenshot (390)" src="https://github.com/user-attachments/assets/67b241a0-57b1-4dfc-a105-08ee06a75481" />
   

8. Melakukan manajemen security pada superuser (Postgres)
    
    <img width="1321" height="346" alt="Screenshot (391)" src="https://github.com/user-attachments/assets/9d54caa7-807b-4956-b31b-65b2617991f8" />
    <img width="1372" height="245" alt="Screenshot (392)" src="https://github.com/user-attachments/assets/f63a6eb9-ecd4-4afc-bf86-382b39344852" />
    <img width="1227" height="343" alt="Screenshot (393)" src="https://github.com/user-attachments/assets/cee09b7a-92d7-4711-a822-158dacfa640e" />
    <img width="846" height="151" alt="Screenshot (394)" src="https://github.com/user-attachments/assets/f84344f2-a36d-4764-a17c-434cc9045362" />


9. Optimasi konfigurasi utama database postgresql (postgresql.conf). Menyesuaikan variabel operasional database agar sesuai dengan lingkungan non-produksi

      <img width="1561" height="833" alt="Screenshot (395)" src="https://github.com/user-attachments/assets/4606381b-adc9-4f6e-b07d-9c3480657600" />




    
 

    








