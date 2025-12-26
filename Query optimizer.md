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

   <img width="1383" height="121" alt="Screenshot (460)" src="https://github.com/user-attachments/assets/8fd957f1-ae82-4b4c-b5f1-09844e64cfcf" />


5. identifikasi kembali query untuk mengetahui update performa database mengeksekusi query.

   <img width="1433" height="203" alt="Screenshot (462)" src="https://github.com/user-attachments/assets/25a8acc9-df98-4e68-bc16-b4e75db70936" />
   - seq scan(full table scan) > index scan : database langsung mencari baris data spesifik tanpa perlu full table scan terlebih dahulu
   - cost 248 > 62 : penurunan cost yang signifikan setelah membuat index include covering index
   - execution time 2.463 > 0.433 : penurunan waktu query yang signifikan menandakan performa query setelah index lebih optimal.


6. 


   
   
   


   

