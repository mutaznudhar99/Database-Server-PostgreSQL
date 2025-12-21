pada sesi kali ini, saya akan melakukan instalasi dan konfigurasi, serta melakukan backup & restore data file menggunakan pgbackrest sebagai tool backup dan restore untuk database server postgresql. 

kelebihan pgbackrest sebagai tool physical backup open source:
- support full, incremental, dan differential backups
- sangat cepat untuk backup database produksi dengan memori besar dan memastikan backup yang konsisten
- parallel backup dan restore
- kemudahan untuk point in time recovery(pitr)
- support retention policy, untuk automasi hapus backup usang
- support compression dan encrypted file backup
- support remote backup dan restore melalui ssh




1. memastikan bahwa tool pgbackrest sudah terinstall di server lokal database server postgresql
   <img width="759" height="101" alt="Screenshot (427)" src="https://github.com/user-attachments/assets/148db495-c27a-445e-b3fb-bb96d0a35680" />


2. konfigurasi pgbackrest.conf untuk menentukan variable dan perilaku tool backup
   - sudo vim /etc/pgbackrest.conf

     <img width="896" height="254" alt="Screenshot (431)" src="https://github.com/user-attachments/assets/2b4d2701-d539-46d9-9061-62e76fdc0bdd" />
     - [global] : variable utama untuk menentukan perilaku tool backup
     - [pg16] : target untuk melakukan backup
     - repo-retention-full : menyimpan file penyimpanan data terakhir, lebih dari misal 2 backup lama dihapus
     - log-level-file : menentukan perilaku monitoring tool backup


3.  konfigurasi postgresql.conf untuk mengatifkan variable archive wal files saat melakukan backsudoup pgbackrest
   - sudo vim /etc/postgresql/16/main/postgresql.conf
     archive_mode = on
     archive_command = 'pgbackrest --stanza=pg16 archive-push %p'
     wal_replica = replica
     wal_compression = on
     archive_timeout = 60s
   - restart database postgresql


4. mmebuat stanza, untuk memberitahu pgbackrest file fisik mana yang harus dibackup pada database postgresql

   <img width="1689" height="319" alt="Screenshot (433)" src="https://github.com/user-attachments/assets/c3ff228f-c768-408e-82f2-bf73d7eb0f0b" />
   - memastikan user postgres tidak dalam keamanan tinggi(scram)
   - merubah autentifikasi user postgres ke autentifikasi(peer)


5. cek status stanza
   - memastikan bahwa konfigurasi dan archive wal files sudah aktif 

   <img width="1684" height="160" alt="Screenshot (434)" src="https://github.com/user-attachments/assets/279eea23-9d3b-479a-949b-755657b3dc54" />


6. mmebuat database, table, dan input beberapa data sebagai bahan testing full backup

   <img width="1197" height="377" alt="Screenshot (435)" src="https://github.com/user-attachments/assets/f0e3046e-e995-4b7c-b7d0-74ae83438f84" />
   <img width="1689" height="447" alt="Screenshot (436)" src="https://github.com/user-attachments/assets/0e739be4-33ed-4e82-85f3-1bf019704f2b" />


7. cek status backup

   <img width="1297" height="263" alt="Screenshot (437)" src="https://github.com/user-attachments/assets/4e1ed1d4-4e19-4feb-8fb0-4a65143d3023" />


8. hapus beberapa data pada table backup di database testingbackup dan restore fullbackup

   <img width="1692" height="723" alt="Screenshot (441)" src="https://github.com/user-attachments/assets/50153fcc-08f0-481d-8ce7-5a0843f85caf" />
   - saat menghapus data setelah melakukan backup database masih menyalin perubahan data pada database server dan menyimpannya ke salinan archive wal file
   - alasan kenapa saat di restore data tidak kembali ke saat sebelum melakukan backup
   - lakukan point in time recovery untuk mengembalikan data yang hilang saat restore


9. melakukan point in time recovery untuk mengembalikan data yang hilang saat restore
   - cek status waktu saat melakukan backup

     <img width="1233" height="262" alt="Screenshot (442)" src="https://github.com/user-attachments/assets/d36b8544-6249-4cde-afe1-a252032139db" />
     - target timer : "2025-12-21 02:24:00"


10. restore backup file data menggunakan waktu terakhir saat backup sebelum kehilangan data

    <img width="1687" height="369" alt="Screenshot (443)" src="https://github.com/user-attachments/assets/f234e483-a558-407f-89e3-b0a6f8b291da" />
    - type : untuk menentukan variable target point in time recovery
    - target-action(opsional) : untuk membuat database berhenti sejenak, supaya mempermudah proses verifikasi data apakah sudah restore ke titik waktu tertentu atau belum


11. melakukan SELECT pg_wal_replay_resume(); pada database server untuk keluar dari mode pause setelah melakukan --target-action=pause saat restore pitr


12. monitoring file logging pgbackrest
    - cd /var/log/pgbackrest
    - sudo tail -f /var/log/pgbackrest/....
      
      <img width="1698" height="202" alt="Screenshot (444)" src="https://github.com/user-attachments/assets/3d3d944c-05c0-4f3d-ac47-635cbf240e81" />

      




   
   

   



   
   



     
   
    
     

   
   
    

   
   
   

