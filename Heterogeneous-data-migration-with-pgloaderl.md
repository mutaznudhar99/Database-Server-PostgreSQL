pada sesi ini, saya akan mendemonstrasikan proses migrasi data lintas platform (heterogen) dari MySQL ke PostgreSQL menggunakan utilitas pgloader. Fokus utama dari demo ini melakukan transformasi skema secara otomatis, migrasi data secara real-time, serta penyesuaian tipe data (type casting) dari dialek MySQL ke standar PostgreSQL guna menjaga integritas data selama masa transisi

hal yang dipersiapkan:
- Source  : MySQL server v8.0
- Target  : PostgreSQL server v16.11
- Migration tool: Pgloader (Latest build dari GitHub)


1. Verifikasi instalasi database MySQL pada source server untuk memastikan layanan berjalan optimal sebelum proses migrasi dilakukan

   <img width="1146" height="383" alt="Screenshot (571)" src="https://github.com/user-attachments/assets/8291eb43-9ab2-4dd1-bed5-957a90410ca3" />


2. Konfigurasi mysqld.cnf pada parameter bind-address. Memungkinkan server PostgreSQL melakukan koneksi jarak jauh (remote connection) ke server MySQL

     <img width="1011" height="170" alt="Screenshot (565)" src="https://github.com/user-attachments/assets/239711d2-5536-4c71-9399-e74e70668681" />
     - * : Menerima koneksi network dari network luar


3. Membuat user khusus pada MySQL. Berfungsi sebagai monitor dan data access provider selama proses migrasi berlangsung

   <img width="934" height="451" alt="Screenshot (587)" src="https://github.com/user-attachments/assets/d0ae8df2-ab94-4f1e-98e5-f141fde1afe9" />


4. Verifikasi konfigurasi character set pada server MySQL. Penggunaan utf8mb4 untuk mendukung data modern (seperti emoji/multibyte characters) agar kompatibel dengan standar UTF-8 di PostgreSQL

   <img width="711" height="462" alt="Screenshot (569)" src="https://github.com/user-attachments/assets/b7795496-9db6-4545-8ea6-d4a1fcc430b1" />


5. Persiapan skema data testing. Membuat database dan tabel dengan variasi tipe data modern untuk test ability type casting tool migrasi

   <img width="1349" height="856" alt="Screenshot (567)" src="https://github.com/user-attachments/assets/dc7dc96d-c1ab-4334-9bb7-337b3da37c79" />


6. Input sampel data (Baseline) ke dalam tabel sebagai sampel validasi. Memastikan data dan type casting yang dipindahkan akurat dan konsisten

   <img width="1687" height="506" alt="Screenshot (568)" src="https://github.com/user-attachments/assets/b44a8efe-b1d2-4ca8-a29b-f3a5c2683561" />


7. Verifikasi target server dengan menginstal PostgreSQL versi 16 pada environment yang berbeda sebagai destinasi migrasi

   <img width="977" height="254" alt="Screenshot (570)" src="https://github.com/user-attachments/assets/029f3055-bddc-4799-ac99-6e3bb04c5353" />


8. Instalasi pgloader versi terbaru. Menjamin full support terhadap tipe data modern dan menghindari isu charset mismatch
   - Menghindari ketidakcocokan charset mysql dengan postgresql
   - Compatible dengan type casting modern

     <img width="676" height="90" alt="Screenshot (581)" src="https://github.com/user-attachments/assets/81a0ac9f-81c9-4700-bf7c-9ce5510f20c4" />


9. Konfigurasi user target PostgreSQL sebagai data receiver dan schema own hasil migrasi

   <img width="826" height="155" alt="Screenshot (572)" src="https://github.com/user-attachments/assets/e65da59d-40b6-4976-a94d-29887fd406d3" />


10. Verifikasi charset target PostgreSQL menggunakan encoding UTF-8, yang secara arsitektur setara dengan utf8mb4 pada MySQL. Menjamin data integrity selama transisi
    - Memastikan charset kompatibel dengan type casting modern MySQL

      <img width="583" height="314" alt="Screenshot (580)" src="https://github.com/user-attachments/assets/07151654-95ea-4c1f-be7a-29c980da491f" />


11. Inisialisasi destinasi database sebagai storage container hasil transformasi data dan skema dari MySQL

    <img width="1338" height="401" alt="Screenshot (573)" src="https://github.com/user-attachments/assets/452fd9ae-fca7-40ce-88b9-4e705bd79001" />


12. Menjalankan perintah pgloader dari server target untuk fetch data dari MySQL. Secara otomatis tool melakukan transformasi skema dan data transfer secara real-time

    <img width="1643" height="585" alt="Screenshot (583)" src="https://github.com/user-attachments/assets/0835a2a9-c5bf-4334-a973-05160427e7c7" />


13. Validasi transformasi tipe data pada tabel PostgreSQL. Memastikan pgloader berhasil menyesuaikan dialek MySQL ke arsitektur PostgreSQL

    <img width="1098" height="551" alt="Screenshot (584)" src="https://github.com/user-attachments/assets/2db6f991-0425-4c2e-878e-4ad2d2d143a7" />


14. Validasi sample data. Memastikan semua baris data di MySQL berpindah secara utuh di sisi PostgreSQL

    <img width="1698" height="239" alt="Screenshot (585)" src="https://github.com/user-attachments/assets/16f65f10-a9fd-4333-9815-bc669afdce80" />



## Migrasi data heterogen sangat dimungkinkan dengan downtime minimal menggunakan tool yang tepat. Namun, untuk database level produksi, perlu ketelitian tinggi dalam pemetaan tipe data dan validasi skema guna menghindari anomali pasca-migrasi



    




     

   


   


   


