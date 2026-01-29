pada sesi ini, saya akan melakukan metode backup dan point in time recovery (PITR) + wal files menggunakan pgbackrest untuk menentukan RPO seminimal mungkin.

apabila metode backup & restore pitr hanya akan mengembalikan data terakhir saat backup, restore pitr + wal files akan mengembalikan data ke titik waktu tertentu sebelum database kehilangan data.

yang perlu dilakukan:
- mengaktifkan archive wal files
- melakukan full backup untuk mengambil salinan wal files pada database server
- wal files untuk disaster recovery RPO ke waktu sebelum kehilangan data, misal 1 menit sebelum kehilangan data.






1. memastikan os server sudah install dan mengkonfigurasi pgbackrest
   
   <img width="759" height="101" alt="Screenshot (427)" src="https://github.com/user-attachments/assets/1973202e-aa74-4774-9360-534d3e43bddd" />
   <img width="896" height="254" alt="Screenshot (431)" src="https://github.com/user-attachments/assets/ea572ebf-019c-47c0-afae-92e69c2d871b" />



2. cek status konfigurasi pgbackrest.conf dan membuat stanza
   
   <img width="1684" height="160" alt="Screenshot (434)" src="https://github.com/user-attachments/assets/079e59d9-5bae-4922-8c4b-ea6666eab743" />
   <img width="1689" height="319" alt="Screenshot (433)" src="https://github.com/user-attachments/assets/33b685b9-e015-4ecc-add3-6a200dfc9e54" />



3. membuat database, table, dan input beberapa data ke dalam table untuk testing backup 

   <img width="1192" height="381" alt="Screenshot (447)" src="https://github.com/user-attachments/assets/b10be8ea-2b66-45b3-856e-ebfbd75d9687" />


4. melakukan full backup menggunakan pgbackrest

   <img width="1694" height="365" alt="Screenshot (448)" src="https://github.com/user-attachments/assets/4912f50d-57a7-4f86-8e5d-7139b5dda7fd" />
   <img width="1007" height="270" alt="Screenshot (449)" src="https://github.com/user-attachments/assets/a6dbae07-bc1f-4b2b-a9bd-d54fecbf83d0" />
   - memeastikan backup start archive wal dengan status backup sesuai


5. hapus beberapa data dan cek logging database server untuk mengetahui waktu saat database kehilangan data

   <img width="875" height="308" alt="Screenshot (450)" src="https://github.com/user-attachments/assets/2e1bb4d9-f70f-4070-a6ba-4562301a8a69" />
   <img width="1365" height="418" alt="Screenshot (451)" src="https://github.com/user-attachments/assets/39c9ea02-948d-49a3-8c3f-693378e6e22d" />
   - 2025-12-21 17:39:49 UTC : waktu database server kehilangan data


6. melakukan restore point in time recovery menggunakan file wal dan menentukan titik waktu sebelum waktu kehilangan data
   - set target-time 2025-12-21 17:39:48

     <img width="1696" height="413" alt="Screenshot (452)" src="https://github.com/user-attachments/assets/86cafd9b-38fb-45be-b518-bb44d6d9c176" />


7. menjalankan database server kembali setelah mode pause
   - sudo -u postgres psql -c "SELECT pg_wal_replay_resume();" 


     
     








   

