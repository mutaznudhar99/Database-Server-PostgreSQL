Pada sesi ini, saya akan mendemonstrasikan instalasi, konfigurasi, serta prosedur physical backup & restore menggunakan pgBackRest. Simulasi ini mencakup pengelolaan Stanza, full backup, hingga teknik Point-In-Time Recovery (PITR) untuk menjamin ketersediaan data pada PostgreSQL Server

kelebihan pgbackrest sebagai tool physical backup:
- Support full, incremental, dan differential backups
- Support parallel processing: Mempercepat proses backup dan restore dengan memanfaatkan multi-core CPU
- Integrasi dengan archive-push untuk memastikan RPO minimum (Zero Data Loss)
- Support retention policy, untuk automasi menghapus backup usang
- Support compression dan encrypted pada direktori backup
- Support remote backup dan restore melalui ssh
- Mempercepat recovery dengan hanya melakukan restore pada file yang berubah (delta restore)




1. Verifikasi intalasi binary. Memastikan utilitas pgbackrest telah terinstal di server dan siap digunakan melalui pengecekan versi
   <img width="759" height="101" alt="Screenshot (427)" src="https://github.com/user-attachments/assets/148db495-c27a-445e-b3fb-bb96d0a35680" />


2. konfigurasi repositori pgbackrest.conf untuk mengatur variabel global dan spesifik Stanza
   - sudo vim /etc/pgbackrest.conf

     <img width="896" height="254" alt="Screenshot (431)" src="https://github.com/user-attachments/assets/2b4d2701-d539-46d9-9061-62e76fdc0bdd" />
     - [global]   : variable utama untuk menentukan perilaku tool backup
     - [pg16]     : target untuk melakukan backup
     - repo-retention-full : Menentukan kebijakan retensi untuk efisiensi disk capacity
     - log-level-file : Mengatur detail log yang disimpan untuk kebutuhan troubleshooting


3.  Konfigurasi postgresql.conf agar PostgreSQL mengirimkan file WAL secara otomatis ke repositori pgBackRest menggunakan perintah archive-push
   - sudo vim /etc/postgresql/16/main/postgresql.conf
     archive_mode = on
     archive_command = 'pgbackrest --stanza=pg16 archive-push %p'
     wal_replica = replica
     wal_compression = on
     archive_timeout = 60s
   - restart database postgresql


4. Inisialisasi stanza (identitas database di pgbackrest) untuk memetakan lokasi file fisik database serta menginisialisasi folder repositori

   <img width="1689" height="319" alt="Screenshot (433)" src="https://github.com/user-attachments/assets/c3ff228f-c768-408e-82f2-bf73d7eb0f0b" />
   - memastikan user postgres tidak dalam keamanan tinggi(scram)
   - merubah autentifikasi user postgres ke autentifikasi(peer)


5. Verifikasi kesehatan konfigurasi pgbackrest (Check). Memastikan database dan repositori sudah terhubung dan mekanisme archiving berjalan lancar

   <img width="1684" height="160" alt="Screenshot (434)" src="https://github.com/user-attachments/assets/279eea23-9d3b-479a-949b-755657b3dc54" />


6. Membuat sampel data sebagai baseline sebelum melakukan full backup

   <img width="1197" height="377" alt="Screenshot (435)" src="https://github.com/user-attachments/assets/f0e3046e-e995-4b7c-b7d0-74ae83438f84" />
   <img width="1689" height="447" alt="Screenshot (436)" src="https://github.com/user-attachments/assets/0e739be4-33ed-4e82-85f3-1bf019704f2b" />


7. Menjalankan perintah backup dan verifikasi melalui info untuk memastikan status backup (OK) dan tercatat di repositori

   <img width="1297" height="263" alt="Screenshot (437)" src="https://github.com/user-attachments/assets/4e1ed1d4-4e19-4feb-8fb0-4a65143d3023" />


8. Simulasi logical loss & analisis restore untuk menguji skenario recovery

   <img width="1692" height="723" alt="Screenshot (441)" src="https://github.com/user-attachments/assets/50153fcc-08f0-481d-8ce7-5a0843f85caf" />
   - saat menghapus data setelah melakukan backup database masih menyalin perubahan data pada database server dan menyimpannya ke salinan archive wal file
   - alasan kenapa saat di restore data tidak kembali ke saat sebelum melakukan backup
   - lakukan point in time recovery untuk mengembalikan data yang hilang saat restore


9. Identifikasi recovery target. Menentukan titik waktu (sebelum loss data) melalui log transaksi atau timestamp sebagai parameter target PITR
   
     <img width="1233" height="262" alt="Screenshot (442)" src="https://github.com/user-attachments/assets/d36b8544-6249-4cde-afe1-a252032139db" />
     - target waktu : "2025-12-21 02:24:00"


10. Eksekusi PITR dengan melakukan restore ke titik waktu aman (sebelum loss data) dengan parameter --type=time dan --target

    <img width="1687" height="369" alt="Screenshot (443)" src="https://github.com/user-attachments/assets/f234e483-a558-407f-89e3-b0a6f8b291da" />
    - type : untuk menentukan variable target point in time recovery
    - target-action(opsional) : untuk membuat database berhenti sejenak (pause) untuk proses verifikasi data

11. Menjalankan fungsi SELECT pg_wal_replay_resume(); untuk mengakhiri database dalam mode recovery dan mengembalikan database ke status operasional (Read/Write)


12. Memantau file log pada /var/log/pgbackrest untuk memastikan tidak ada error selama proses transfer data dan WAL replay
      
      <img width="1698" height="202" alt="Screenshot (444)" src="https://github.com/user-attachments/assets/3d3d944c-05c0-4f3d-ac47-635cbf240e81" />

      




   
   

   



   
   



     
   
    
     

   
   
    

   
   
   

