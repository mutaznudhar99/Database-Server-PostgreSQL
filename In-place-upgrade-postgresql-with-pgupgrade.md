Pada sesi kali ini, saya akan mendemonstrasikan proses in-place upgrade server database PostgreSQL dari versi 16 ke versi 17 menggunakan utilitas pg_upgrade. Metode ini dipilih karena efisien melakukan transisi versi mayor pada server yang sama tanpa perlu melakukan proses dump dan restore yang memakan waktu lama

kelebihan pg_upgrade:
- Efisiensi Metadata: Memindahkan metadata dan sistem katalog dari cluster lama ke cluster baru secara instan
- Binary integrity  : Memastikan kompatibilitas file binary versi lama dan baru sebelum melakukan upgrade versi cluster
- Cepat untuk database dengan memori besar

kekurangan pg_upgrade:
- Downtime selama proses transisi metadata
- File konfigurasi custome seperti postgresql.conf dan pg_hba.conf tidak di salin secara otomatis. Perlu penyesuaian secara manual dengan cluster versi terbaru



1. Instalasi PostgreSQL versi 16 sebagai source cluster yang akan di-upgrade versi mayor

     <img width="685" height="67" alt="Screenshot (465)" src="https://github.com/user-attachments/assets/f60f85cf-cbf4-49fd-acb3-c21a7e0de77f" />


2. Inisialisasi database dan tabel sebagai data sample testing. Memastikan data tetap utuh pasca proses upgrade

   <img width="1328" height="641" alt="Screenshot (466)" src="https://github.com/user-attachments/assets/1c4641b0-b69b-41e0-ba3b-7380bd7f8a03" />


3. Instalasi paket PostgreSQL 17. Secara otomatis, menyiapkan struktur direktori dan binary untuk versi upgrade

     <img width="675" height="68" alt="Screenshot (468)" src="https://github.com/user-attachments/assets/c3c1ada4-d139-4ee3-9c62-032021c18684" />


4. Menjalankan pg_lsclusters untuk melakukan verifikasi terhadap status cluster yang berjalan di sistem operasi

     <img width="1042" height="91" alt="Screenshot (469)" src="https://github.com/user-attachments/assets/74bab442-49b6-44bf-ad5b-d9192ced42e4" />


5. Mendaftarkan cluster versi terbaru menggunakan pg_createcluster agar berjalan side-by-side di sistem operasi. Memastikan cluster PostgreSQL 17 dalam kondisi bersih (clean state) agar dapat menampung metadata dari versi 16.

     <img width="1270" height="690" alt="Screenshot (470)" src="https://github.com/user-attachments/assets/1adb9e6c-941f-4100-906f-2e4018748fce" />


7. Pre-upgrade compatibility check dengan melakukan service stop pada kedua versi, kemudian menjalankan pg_upgrade dengan flag --check. 
   - Verifikasi compatibility file binary dan lokasi data file (PGDATA) agar proses upgrade berjalan sempurna

   <img width="1080" height="146" alt="Screenshot (473)" src="https://github.com/user-attachments/assets/690e422d-d903-4a2f-81f2-d1cce268c61a" />
   <img width="907" height="474" alt="Screenshot (474)" src="https://github.com/user-attachments/assets/4d219731-5074-4d24-ad34-e56a91e85de9" />
   - Main : data file database server
   - Bin  : binary database server


8. Menjalankan pg_upgrade menggunakan opsi --link. Metode tercepat karena utilitas hanya menghubungkan blok data fisik ke sistem katalog baru tanpa melakukan copy data

   <img width="896" height="869" alt="Screenshot (475)" src="https://github.com/user-attachments/assets/0792c3d5-d2e7-49e8-a559-484a84f998f1" />


9. Mengaktifkan service PostgreSQL 17 dan verifikasi inisialisasi database dan table data testing

    <img width="1415" height="531" alt="Screenshot (477)" src="https://github.com/user-attachments/assets/68e55cdd-d2d3-4c5f-a4ea-af514ee0fa94" />
    - database sudah diperbarui ke versi postgresql 17


10. Menjalankan perintah vacuumdb atau script analyze_new_cluster.sh. Membangun kembali statistik optimizer agar performa query tetap optimal pada cluster new version
    
    <img width="1094" height="238" alt="Screenshot (478)" src="https://github.com/user-attachments/assets/a652738b-8598-40da-88ba-2cf7bfa01cda" />


11. Melakukan pembersihan total (purge) paket instalasi & cluster PostgreSQL 16. Menjaga kebersihan dependensi pada sistem operasi
    - apt auto-remove postgresql-16
    - apt purge poestgresql-16

      <img width="892" height="161" alt="Screenshot (479)" src="https://github.com/user-attachments/assets/579e706d-e94a-40b8-8ac7-23c270d4fb5c" />


12. Verifikasi akhir melalui pg_lsclusters. Memastikan server hanya menjalankan cluster versi 17 dan database tetap utuh pasca-proses upgrade

    <img width="1370" height="372" alt="Screenshot (482)" src="https://github.com/user-attachments/assets/dd833be7-9429-4d8e-a617-f5e715e8a0d5" />


    

    


    


    






   


   



   
