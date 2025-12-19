pada sesi kali ini, saya akan melakukan physical backup yang menyimpan file data direktori postgresql server menggunakan backup and recovery manager (barman).

barman merupakan tools open source administrator yang dikembangkan oleh enterpriseDB untuk disaster recovery postgresql server.

kelebihan barman dibandingkan pg_basebackup(native) postgresql:
- hot physical backup
- support incremental backup
- support remote backup menggunakan rsync/ssh
- cocok untuk point in time recovery
- satu server barman bisa mengelola lebih dari satu server postgresql, best practice
- mengambil data via(pg_basebackup) dan menerima log via(wal archiving) terus menerus.
- support compression wal file dan backup
- support retention policy, untuk menghapus backup lama secara otomatis
- support restore ke server postgresql berbeda versi minor (16.2 > 16.5)






1. mencari paket software barman di os linux

   <img width="1201" height="105" alt="Screenshot (415)" src="https://github.com/user-attachments/assets/9ad280b2-18ec-4c07-9b78-d61efff46227" />


2. cek informasi detail mengenai software barman untuk melihat semua informasi mengenai versi barman yang tersedia di os linux

   <img width="1265" height="539" alt="Screenshot (416)" src="https://github.com/user-attachments/assets/e1e40d43-4a58-4e4a-9b8b-8b522ae3ce5f" />


3. update server dan install prasyarat software barman

   <img width="1248" height="442" alt="Screenshot (417)" src="https://github.com/user-attachments/assets/51710966-d88d-4b34-80bd-76f14686bd1b" />
   - berfungsi menambahkan repositori untuk barman


4. menambahkan kunci autentifikasi postgresql dan mendaftarkannya ke repositori postgresql

   <img width="1679" height="463" alt="Screenshot (421)" src="https://github.com/user-attachments/assets/3b920671-b9a3-4bf2-93dd-9e113e25f888" />
   - bertujuan untuk mendaftarkan software resmi ke dalam os linux


5. update server dan install barman

   <img width="978" height="100" alt="Screenshot (422)" src="https://github.com/user-attachments/assets/3943e251-ce4c-47f7-8b09-4ca828bce5b6" />


6. membuat kunci keamanan untuk menggunakan barman terhubung ke database server tanpa akses password
   - membuat backup tanpa perlu input password
   - lebih aman dari membuat password biasa
   - mempermudah koneksi dengan database server

     <img width="1238" height="550" alt="Screenshot (423)" src="https://github.com/user-attachments/assets/6ce62374-045c-4d3b-b737-a590d5635e8d" />


7. 

   


   



     
