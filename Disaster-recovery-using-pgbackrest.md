Pada sesi ini, saya akan mengimplementasikan metode Point-In-Time Recovery (PITR) menggunakan pgBackRest. Simulasi ini memanfaatkan WAL (Write Ahead Log) files yang terarsip secara continuity untuk mencapai RPO (Recovery Point Objective) seminimal mungkin, memungkinkan pemulihan data hingga detik sebelum terjadinya insiden kehilangan data

yang perlu dilakukan:
- mengaktifkan archive wal files
- melakukan full backup untuk mengambil salinan wal files pada database server
- wal files untuk disaster recovery RPO ke waktu sebelum kehilangan data, misal 1 menit sebelum kehilangan data.






1. Memastikan utilitas pgBackRest telah terinstal dan file pgbackrest.conf telah dikonfigurasi dengan benar (menentukan lokasi repositori dan retention policy)
   
   <img width="759" height="101" alt="Screenshot (427)" src="https://github.com/user-attachments/assets/1973202e-aa74-4774-9360-534d3e43bddd" />
   <img width="896" height="254" alt="Screenshot (431)" src="https://github.com/user-attachments/assets/ea572ebf-019c-47c0-afae-92e69c2d871b" />



2. Membuat dan melakukan pengecekan status stanza. Memastikan jalur komunikasi antara database dan repositori backup valid, serta mekanisme archiving siap menerima file WAL
   
   <img width="1684" height="160" alt="Screenshot (434)" src="https://github.com/user-attachments/assets/079e59d9-5bae-4922-8c4b-ea6666eab743" />
   <img width="1920" height="189" alt="Screenshot (1226)" src="https://github.com/user-attachments/assets/926e7536-d848-452b-887f-de81b3879fdb" />


3. Membuat simulasi dataset pada tabel sebagai bukti konkret bahwa data yang ada sebelum backup dan sebelum insiden dapat dipulihkan

   <img width="1192" height="381" alt="Screenshot (447)" src="https://github.com/user-attachments/assets/b10be8ea-2b66-45b3-856e-ebfbd75d9687" />


4. Mengeksekusi full backup dan memastikan file WAL yang diperlukan untuk konsistensi backup tersebut juga ikut terarsip dalam repositori

   <img width="1694" height="365" alt="Screenshot (448)" src="https://github.com/user-attachments/assets/4912f50d-57a7-4f86-8e5d-7139b5dda7fd" />
   <img width="1007" height="270" alt="Screenshot (449)" src="https://github.com/user-attachments/assets/a6dbae07-bc1f-4b2b-a9bd-d54fecbf83d0" />
   

5. Simulasi insiden logical loss, kemudian melakukan investigasi pada PostgreSQL error log untuk menemukan timestamp saat perintah DELETE/DROP dieksekusi

   <img width="875" height="308" alt="Screenshot (450)" src="https://github.com/user-attachments/assets/2e1bb4d9-f70f-4070-a6ba-4562301a8a69" />
   <img width="1365" height="418" alt="Screenshot (451)" src="https://github.com/user-attachments/assets/39c9ea02-948d-49a3-8c3f-693378e6e22d" />
   - 2025-12-21 17:39:49 UTC : waktu database server kehilangan data


6. Menjalankan prosedur restore dengan parameter PITR. Menentukan target satu detik sebelum insiden (RPO minimal)
   - set target-time 2025-12-21 17:39:48

     <img width="1696" height="413" alt="Screenshot (452)" src="https://github.com/user-attachments/assets/86cafd9b-38fb-45be-b518-bb44d6d9c176" />


7. Menjalankan perintah SELECT pg_wal_replay_resume(); untuk mengakhiri status recovery dan mengubah mode database dari Read-Only (saat pause) menjadi Read-Write (operasional normal)
   - sudo -u postgres psql -c "SELECT pg_wal_replay_resume();" 


     
     








   

