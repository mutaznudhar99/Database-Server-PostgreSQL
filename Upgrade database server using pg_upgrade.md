pada sesi kali ini, saya akan melakukan upgrade database server dari versi 16 ke versi 17 menggunakan pg_upgrade utility di server yang sama.

kelebihan pg_upgrade sebagai utility upgrade database server versi mayor:
- migrasi metadata pada database server
- memindahkan data fisik database server
- memindahkan system katalog
- memindahkan file binary database server
- cepat untuk database dengan memori besar

kekurangan pg_upgrade sebagai utility upgrade database server:
- downtime selama beberapa menit
- tidak menyalin konfigurasi database server seperi postgresql.conf dan lainnya



1. install postgresql 16
   - sudo apt-get install postgresql-16 postgresql-contrib -y

     <img width="685" height="67" alt="Screenshot (465)" src="https://github.com/user-attachments/assets/f60f85cf-cbf4-49fd-acb3-c21a7e0de77f" />


2. membuat database, table, dan input beberapa data sebagai bahan testing upgrade versi database

   <img width="1328" height="641" alt="Screenshot (466)" src="https://github.com/user-attachments/assets/1c4641b0-b69b-41e0-ba3b-7380bd7f8a03" />


3. install postgresql 17
   - sudo apt-get install postgresql-17 postgresql-contrib -y

     <img width="675" height="68" alt="Screenshot (468)" src="https://github.com/user-attachments/assets/c3c1ada4-d139-4ee3-9c62-032021c18684" />


4. cek database server yang tersedia di os linux
   - pg_lsclusters
     <img width="1042" height="91" alt="Screenshot (469)" src="https://github.com/user-attachments/assets/74bab442-49b6-44bf-ad5b-d9192ced42e4" />


6. membuat cluster baru versi postgresql 17
   - pg_createcluster 17 main

     <img width="1270" height="690" alt="Screenshot (470)" src="https://github.com/user-attachments/assets/1adb9e6c-941f-4100-906f-2e4018748fce" />


7. cek database di postgresql versi 17, memastikan tidak ada database lain selain database bawaan

   <img width="1262" height="289" alt="Screenshot (472)" src="https://github.com/user-attachments/assets/c59e7876-defb-4fcf-a780-3c29db6ce52e" />


8. stop kedua system postgresql kemudian melakukan kecocokan file binary, data, dan konfigurasi database server sebelum eksekusi upgrade

   <img width="1080" height="146" alt="Screenshot (473)" src="https://github.com/user-attachments/assets/690e422d-d903-4a2f-81f2-d1cce268c61a" />
   <img width="907" height="474" alt="Screenshot (474)" src="https://github.com/user-attachments/assets/4d219731-5074-4d24-ad34-e56a91e85de9" />
   - main : file data database server
   - bin : binary database server
   - postgresql.conf : lokasi konfigurasi postgresql apabila terpisah dengan data fisik


9. setelah file kedua versi compatible, eksekusi pg_upgared menggunakan link, supaya lebih cepat.

   <img width="896" height="869" alt="Screenshot (475)" src="https://github.com/user-attachments/assets/0792c3d5-d2e7-49e8-a559-484a84f998f1" />


10. start system postgresql 17 dan cek database yang tersedia

    <img width="1415" height="531" alt="Screenshot (477)" src="https://github.com/user-attachments/assets/68e55cdd-d2d3-4c5f-a4ea-af514ee0fa94" />
    - database sudah diperbarui ke versi postgresql 17


11. melakukan vacuum database untuk membangun ulang statistik dan mengembalikan performa database supaya lebih optimal

    <img width="1094" height="238" alt="Screenshot (478)" src="https://github.com/user-attachments/assets/a652738b-8598-40da-88ba-2cf7bfa01cda" />


12. hapus direktori cluster postgresql versi 16

    <img width="892" height="161" alt="Screenshot (479)" src="https://github.com/user-attachments/assets/579e706d-e94a-40b8-8ac7-23c270d4fb5c" />


13. hapus paket instalasi dan membersihkan folder postgresql versi 16
    - sudo apt purge postgresql-16


14. cek status cluster database, memastikan cluster database versi 16 sudah terhapus

    <img width="1370" height="372" alt="Screenshot (482)" src="https://github.com/user-attachments/assets/dd833be7-9429-4d8e-a617-f5e715e8a0d5" />


    

    


    


    






   


   



   
