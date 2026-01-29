pada sesi kali ini, saya akan mendemonstrasikan proses Migrasi Data Heterogen (lintas platform) dari MySQL ke PostgreSQL menggunakan tool pgloader. Fokus utama dari demonstrasi ini adalah melakukan transformasi 
skema secara otomatis, migrasi data secara real-time, serta penyesuaian tipe data (type casting) dari dialek MySQL ke standar PostgreSQL untuk menjaga integritas data selama proses transisi.

hal yang perlu dipersiapkan:
- 1 server mysql versi 8.0
- 1 server postgresql versi 16.11
- tool pgloader


1. install database mysql server di server mysql

   <img width="1146" height="383" alt="Screenshot (571)" src="https://github.com/user-attachments/assets/8291eb43-9ab2-4dd1-bed5-957a90410ca3" />


2. konfigurasi mysqld.cnf. mengkoneksikan server postgresql dengan server mysql
   - sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

     <img width="1011" height="170" alt="Screenshot (565)" src="https://github.com/user-attachments/assets/239711d2-5536-4c71-9399-e74e70668681" />
     - * : menerima koneksi dari server luar


3. membuat user sebagai monitor migrasi data

   <img width="934" height="451" alt="Screenshot (587)" src="https://github.com/user-attachments/assets/d0ae8df2-ab94-4f1e-98e5-f141fde1afe9" />


4. cek character set mysql server. memastikan charset compatable dengan postgresql server.

   <img width="711" height="462" alt="Screenshot (569)" src="https://github.com/user-attachments/assets/b7795496-9db6-4545-8ea6-d4a1fcc430b1" />
   - utf8mb4 : charset modern dari mysql. menunjang type data seperti emoji dan sebagainya.


5. membuat database dan table dengan variasi type data modern sebagai testing migrasi data.

   <img width="1349" height="856" alt="Screenshot (567)" src="https://github.com/user-attachments/assets/dc7dc96d-c1ab-4334-9bb7-337b3da37c79" />


6. input data ke dalam table sebagai sample data 

   <img width="1687" height="506" alt="Screenshot (568)" src="https://github.com/user-attachments/assets/b44a8efe-b1d2-4ca8-a29b-f3a5c2683561" />


7. install database postgresql di server berbeda

   <img width="977" height="254" alt="Screenshot (570)" src="https://github.com/user-attachments/assets/029f3055-bddc-4799-ac99-6e3bb04c5353" />


8. install pgloader versi terbaru dari github
   - menghindari ketidakcocokan charset mysql dengan postgresql
   - menerima type data modern dari mysql

     <img width="676" height="90" alt="Screenshot (581)" src="https://github.com/user-attachments/assets/81a0ac9f-81c9-4700-bf7c-9ce5510f20c4" />


9. mmebuat user di postgresql untuk menerima data dari mysql

   <img width="826" height="155" alt="Screenshot (572)" src="https://github.com/user-attachments/assets/e65da59d-40b6-4976-a94d-29887fd406d3" />


10. cek status charset postgresql server
    - memastikan charset kompatibel dengan mysql

      <img width="583" height="314" alt="Screenshot (580)" src="https://github.com/user-attachments/assets/07151654-95ea-4c1f-be7a-29c980da491f" />
      - utf8 : charset default postgresql yang setara dengan charset mysql utf8mb4


11. membuat database sebagai lokasi penyimpanan data dari mysql

    <img width="1338" height="401" alt="Screenshot (573)" src="https://github.com/user-attachments/assets/452fd9ae-fca7-40ce-88b9-4e705bd79001" />


12. eksekusi migrasi data menggunakan tool pgloader di server postgresql

    <img width="1643" height="585" alt="Screenshot (583)" src="https://github.com/user-attachments/assets/0835a2a9-c5bf-4334-a973-05160427e7c7" />


13. verifikasi type data pada table di postgresql 

    <img width="1098" height="551" alt="Screenshot (584)" src="https://github.com/user-attachments/assets/2db6f991-0425-4c2e-878e-4ad2d2d143a7" />
    - type data mengalami perubahan karena pgloader menyesuaikan type data berdasarkan dengan arsitektur postgresql


15. verifikasi sample data

    <img width="1698" height="239" alt="Screenshot (585)" src="https://github.com/user-attachments/assets/16f65f10-a9fd-4333-9815-bc669afdce80" />



## secara teknis, migrasi data memungkinkan dilakukan. butuh ketelitian lebih tinggi untuk database level produksi.



    




     

   


   


   


