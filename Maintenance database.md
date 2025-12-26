Pada sesi kali ini, saya akan melakukan pemeliharaan (maintenance) database menggunakan pg_repack dan ANALYZE. Tujuannya adalah untuk mereorganisasi ukuran fisik tabel yang mengalami pembengkakan (bloat) 
serta memperbarui statistik tabel agar query planner dapat bekerja secara akurat, sehingga performa database tetap optimal.

kelebihan pg_repack:
- online processing (tanpa lock table dan zero downtime)
- mengembalikan disk space ke OS
- reorganisasi index (reindex)
- meningkatkan performa I/O


kekurangan pg_repack
- beban resources tinggi CPU dan I/O
- dilakukan pada table yang memiliki primary key atau unique key not null
- mengkonsumsi banyak ruang disk. membutuhkan setidaknya 2x lipat disk dari ukuran table





1. pg_repack sudah terinstalasi di server linux

   <img width="527" height="68" alt="Screenshot (519)" src="https://github.com/user-attachments/assets/4ec04e7b-8e91-4ab4-aacc-81ccdb463f97" />


2. konfigurasi postgresql.conf dan menambahkan parameter pg_repack pada shared_preload_libraries

   <img width="750" height="131" alt="Screenshot (520)" src="https://github.com/user-attachments/assets/b0a0f17a-6678-4e6c-9815-3013d99d6657" />


3. membuat extension pg_repack pada target database

   <img width="1018" height="356" alt="Screenshot (521)" src="https://github.com/user-attachments/assets/5bc9c370-0510-4151-bf8c-e88aa974637b" />


4. membuat table dan input 1 juta data dummy sebagai bahan testing maintenance database dari bloat size disk table

   <img width="1689" height="340" alt="Screenshot (522)" src="https://github.com/user-attachments/assets/0d0d2dee-0615-4313-bf90-945da530dd9d" />


5. membuat index baru sebagai bahan testing maintenance database dari bloat size disk index pada table

   <img width="643" height="59" alt="Screenshot (523)" src="https://github.com/user-attachments/assets/2784184b-9f37-4a27-a91f-f64dc4f1f819" />


6. cek status autovacuum, dead tuples, size disk index, dan melakukan rencana eksekusi sebelum terjadi bloat pada table

   <img width="1692" height="243" alt="Screenshot (525)" src="https://github.com/user-attachments/assets/5d1608d6-c125-433e-a981-6b15f7950da3" />
   <img width="1082" height="147" alt="Screenshot (533)" src="https://github.com/user-attachments/assets/9c153672-bbe3-4647-ac33-eca339060d99" />
   <img width="1178" height="274" alt="Screenshot (526)" src="https://github.com/user-attachments/assets/e6b90f3d-2e34-4874-9353-4d788468de36" />
   <img width="388" height="113" alt="Screenshot (534)" src="https://github.com/user-attachments/assets/79fd9ff1-99e3-4864-a637-d1e110f65474" />
   - walaupun autovacuum sudah aktif secara default, tapi tugaas autovacuum tidak membantu banyak dalam mengelola size disk pada table apabila database mengalami operasi DML yang cukup besar.
     oleh karena itu, size disk table dan index akan terjadi bloat size disk yang signifikan tergantung seberapa besar operasi DML nya.


7. melakukan update pada kolom table

   <img width="728" height="651" alt="Screenshot (529)" src="https://github.com/user-attachments/assets/7ab40ebc-bf76-49d4-a7c0-21a4d951a3ee" />


8. cek size dead tuples, size disk index, dan rencana eksekusi pada table setelah melakukan operasi DML (update)
   - bloat pada size disk dan size disk index bisa menyebabkan menurunnya performa database seperti query, performa index, dan melakukan backup data lebih lama.

   <img width="1700" height="674" alt="Screenshot (530)" src="https://github.com/user-attachments/assets/dcaba600-d12a-4910-96c0-fc341f745fe0" />
   - idx_orders 6848kb > 12mb
   - idx_orders_pkey 21mb > 34mb
   - n_dead_tup 0 > 269367
   - execution_time 1.541 > 4.413


10. melakukan pg_repack menggunakan user postgres dan melihat proses repack table secara realtime menggunakan -e
   - untuk merapihkan size disk table, size disk index, dan statistik rencana eksekusi ke performa terbaiknya

   <img width="1690" height="315" alt="Screenshot (531)" src="https://github.com/user-attachments/assets/fce937b3-be3c-4429-9320-3a5a52854330" />



11. cek size dead tuples, size disk index, dan rencana eksekusi setelah melakukan pg_Repack
    
    <img width="1680" height="686" alt="Screenshot (532)" src="https://github.com/user-attachments/assets/7b109f50-f191-41b9-9f51-077f13842431" />
    - idx_orders 6848kb > 12mb > 6856kb
    - idx_orders_pkey 21mb > 34mb > 21mb
    - n_dead_tup 0 > 269367 > 0
    - execution_time 1.541 > 4.413 > 0.713


12. melakukan analyze verbose pada table untuk memperbarui data statistik untuk menentukan jalur rencana eksekusi yang optimal setelah me-repack struktur fisik table
    - analyze verbose lebih informatif dari analyze biasa karena menampilkan detail teknis mengenai informasi sampel data

      <img width="1443" height="124" alt="Screenshot (535)" src="https://github.com/user-attachments/assets/a8e410b7-e4a2-4925-b73c-2345d2536c47" />


13. cek rencana eksekusi query setelah melakukan analyze verbose

    <img width="1162" height="273" alt="Screenshot (536)" src="https://github.com/user-attachments/assets/d0fb2a23-daf0-4196-9215-4c1065ac0d3a" />
    execution_time 1.541 > 4.413 > 0.713 > 0.597





    








   


