pada sesi kali ini, saya akan melakukan Optimasi Query dengan mengkomparasi performa melalui Execution Plan sebelum dan sesudah penerapan indeks. Tujuannya adalah untuk memvalidasi efektivitas indeks dalam meminimalkan biaya query (query cost) dan waktu eksekusi. 


1. membuat database dan table dengan data dummy sebagai bahan testing query optimizer

   <img width="1178" height="646" alt="Screenshot (456)" src="https://github.com/user-attachments/assets/3acf2175-af66-402b-8025-ab11458379e0" />


2. identifikasi query menggunakan **explain analyze** untuk mengetahui seberapa cepat performa database mengeksekusi query
   - menghindari pemanggilan seluruh kolom (*) pada table lebih optimal untuk menghindari cost yang tinggi dan efektifitas penggunaan index

     <img width="1418" height="196" alt="Screenshot (463)" src="https://github.com/user-attachments/assets/bf3b96b9-29f5-437a-a003-79483a32bbcd" />
     - seq scan (full table scan) : database memindai seluruh table kemudian mencari data spesifik (sangat lambat)
     - execution_time 2.463 : sangat tinggi, performa lambat.


3. membuat index pada kolom logika where clause include dengan kolom pemanggilan data (covering index)
   - lebih cepat ke performa query untuk memanggil data menggunakan index only scan

     <img width="1367" height="119" alt="Screenshot (537)" src="https://github.com/user-attachments/assets/33e86bba-cf6c-4357-8e17-f18e09882578" />
     - index concurrently : membuat index secara online tanpa perlu lock table
 
     
4. identifikasi kembali query untuk mengetahui update performa database mengeksekusi query.

   <img width="1433" height="203" alt="Screenshot (462)" src="https://github.com/user-attachments/assets/25a8acc9-df98-4e68-bc16-b4e75db70936" />
   - seq scan(full table scan) > index scan : database langsung mencari baris data spesifik tanpa perlu full table scan terlebih dahulu
   - cost 248 > 62 : penurunan cost yang signifikan setelah membuat index include covering index
   - execution time 2.463 > 0.433 : penurunan waktu query yang signifikan menandakan performa query setelah index lebih optimal.


5. identifikasi index exist pada database

   <img width="1210" height="165" alt="Screenshot (538)" src="https://github.com/user-attachments/assets/22aca017-1c47-4d1e-88a9-479e886c1d2e" />


6. cek rasio penggunaan index pada ram (index cache hit ration)

   <img width="969" height="291" alt="Screenshot (539)" src="https://github.com/user-attachments/assets/e7b69603-6c15-4436-9cb4-469d95839b2f" />
   - read_from_ram : membaca dari RAM lebih cepat daripada dari disk
   - cache_hit_percentage : 99% masih aman digunakan. apabila <95% pertimbangkan **reindex index concurrently nameindex** karena bisa jadi bloat index


7. cek index duplikat dan redundan index pada table database, kemudian hapus salah satu index duplikat.
   - menghapus salah satu atau menyisakan 1 index yang membuat memori lebih efisien, mengurangi beban operasi DML, dan mempercepat prosess maintenance.

     <img width="1459" height="752" alt="Screenshot (541)" src="https://github.com/user-attachments/assets/c1bc6a80-416b-4d7e-82db-66316baa73d8" />

   - cek status index terbaru setelah menghapus index duplikat

     <img width="1236" height="368" alt="Screenshot (542)" src="https://github.com/user-attachments/assets/77cbd93b-6b11-43e7-97ef-c5a5221f9303" />
     - 0 rows : tidak ada index duplikat atau redundan







   
   
   


   

