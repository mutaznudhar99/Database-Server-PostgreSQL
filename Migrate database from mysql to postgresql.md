pada sesi kali ini, saya akan mendemonstrasikan proses Migrasi Data Heterogen (lintas platform) dari MySQL ke PostgreSQL menggunakan tool pgloader. Fokus utama dari demonstrasi ini adalah melakukan transformasi 
skema secara otomatis, migrasi data secara real-time, serta penyesuaian tipe data (type casting) dari dialek MySQL ke standar PostgreSQL untuk menjaga integritas data selama proses transisi.


hal yang perlu dipersiapkan:
1. 1 server mysql versi 8.0
2. 1 server postgresql versi 16.11
3. tool pgloader, 
4. memastikan kedua database server menggunakan encoding utf-8 untuk menghindari corrupt character





1. install mysql dan postgresql database di masing-masing server

   <img width="683" height="64" alt="Screenshot (543)" src="https://github.com/user-attachments/assets/d5175b99-a4ad-4836-beea-5ec6482ba500" />
   <img width="744" height="79" alt="Screenshot (544)" src="https://github.com/user-attachments/assets/ae769416-17a2-435a-9b8b-47a05b4fde37" />


2. konfigurasi bind_address mysqld.cnf mysql sebagai listening parameter untuk menerima koneksi dari server luar

   


2. membuat user di mysql server menggunakan ip server postgresql sebagai monitor migrasi data

   <img width="1029" height="348" alt="Screenshot (545)" src="https://github.com/user-attachments/assets/ff1a8b72-c9e2-48c6-8f8a-b35db3cf987a" />


3. membuat sample data di database mysql sebagai bahan demonstrasi migrasi data

   <img width="897" height="715" alt="Screenshot (546)" src="https://github.com/user-attachments/assets/45140513-0db4-492a-bc13-b207f958ca7b" />


4. cek encoding database mysql, memastikan menggunakan character utf8mb4

   <img width="703" height="294" alt="Screenshot (551)" src="https://github.com/user-attachments/assets/98bda6fd-8747-4645-a6f9-70f2076b3101" />



5. install tool pgloader di server database postgresql

   <img width="791" height="94" alt="Screenshot (547)" src="https://github.com/user-attachments/assets/43419ee7-a6cf-4f58-b183-6e44a3cdfbb7" />


6. membuat user postgres dengan password untuk menerima data dari server database mysql

   <img width="867" height="210" alt="Screenshot (548)" src="https://github.com/user-attachments/assets/0f2abcbf-9972-49f7-a902-54e4d6b8fc1e" />


7. membuat database di server postgresql untuk menerima data dari server mysql

   <img width="1343" height="491" alt="Screenshot (550)" src="https://github.com/user-attachments/assets/a7653367-414f-4b43-82d6-25c7a29660dd" />


8. cek encoding database postgresql, memastikan menggunakan character yang sama dengan database mysql

   <img width="589" height="235" alt="Screenshot (552)" src="https://github.com/user-attachments/assets/0a5941a9-90be-44c6-bdcb-18811c7fa8fb" />
   - utf8mb4 > utf8 : character set database yang sama menghindari kegagalan saat migrasi data terutama untuk type data yang berbeda antara platform mysql dan postgresql


9. proses migrasi data menggunakan pgloader di server postgresql

   
   







   

   

   



   
