Pada sesi kali ini, saya akan melakukan optimasi query dengan mengkomparasi performa melalui execution plan sebelum dan sesudah penerapan indeks. Bertujuan memvalidasi efektifitas index dalam meminimalkan query cost dan execution time. Selain itu, Melakukan monitoring index cache hit ratio dan eliminasi duplikasi index untuk menjaga efisiensi storage dan performa operasi DML


1. Dataset preparation dengan create database dan table sebagai sampel testing untuk query optimizer

   <img width="1178" height="646" alt="Screenshot (456)" src="https://github.com/user-attachments/assets/3acf2175-af66-402b-8025-ab11458379e0" />


2. Identifikasi initial performance query menggunakan EXPLAIN ANALYZE. Mengetahui seberapa cepat performa database mengeksekusi query
   - Menghindari query seluruh kolom (*) membantu mengurangi cost yang tinggi dan efektifitas penggunaan index

     <img width="1418" height="196" alt="Screenshot (463)" src="https://github.com/user-attachments/assets/bf3b96b9-29f5-437a-a003-79483a32bbcd" />
     - seq scan (full table scan) : database memindai seluruh table kemudian mencari data spesifik (sangat lambat)
     - execution_time 2.463       : sangat tinggi, performa lambat.


3. Implementasi index pada kolom query filter. Menggunakan klausa INCLUDE untuk membuat covering index, sehingga database dapat mengambil data langsung dari index tanpa melakukan heap fetch (full table scan)

     <img width="1367" height="119" alt="Screenshot (537)" src="https://github.com/user-attachments/assets/33e86bba-cf6c-4357-8e17-f18e09882578" />
     - Index concurrently : Membuat index tanpa perlu lock table, sehingga database tetap berjalan
 
     
4. Identifikasi performa query pasca penerapan index menggunakan EXPLAIN ANALYZE. Memastikan workload database berkurang

   <img width="1433" height="203" alt="Screenshot (462)" src="https://github.com/user-attachments/assets/25a8acc9-df98-4e68-bc16-b4e75db70936" />
   - Status query scan  : seq scan(full table scan) > index scan
   - Cost reduction     : 248 > 62 
   - Time reduction     : 2.463 > 0.433
   

5. Melakukan audit terhadap list index exist. Memastikan index yang diinisiasi terdaftar dalam sistem katalog database

   <img width="1210" height="165" alt="Screenshot (538)" src="https://github.com/user-attachments/assets/22aca017-1c47-4d1e-88a9-479e886c1d2e" />


6. Mengevaluasi efektivitas penggunaan RAM melalui analisis cache hit ratio. Memastikan index dibaca dari memori buffer cache

   <img width="969" height="291" alt="Screenshot (539)" src="https://github.com/user-attachments/assets/e7b69603-6c15-4436-9cb4-469d95839b2f" />
   - Read_from_ram          : Membaca dari RAM lebih cepat daripada dari disk
   - Target cache_hit_ratio : Rasio > 99% menandakan efisiensi RAM yang optimal. Jika rasio turun drastis, memerlukan tindakan REINDEX untuk mengatasi bloat pada insex


7. Mengidentifikasi dan menghapus index duplikat. Memastikan efisiensi storage capacity dan mengurangi beban operasi DML
   
     <img width="1459" height="752" alt="Screenshot (541)" src="https://github.com/user-attachments/assets/c1bc6a80-416b-4d7e-82db-66316baa73d8" />

     <img width="1236" height="368" alt="Screenshot (542)" src="https://github.com/user-attachments/assets/77cbd93b-6b11-43e7-97ef-c5a5221f9303" />








   
   
   


   

