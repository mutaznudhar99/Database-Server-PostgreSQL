Pada sesi kali ini, saya akan melakukan maintenance database menggunakan tools pg_repack dan perintah ANALYZE. Bertujuan reorganisasi struktural fisik tabel yang mengalami pembengkakan (bloat) serta meng-update statistik tabel. Memastikan query planner bekerja secara akurat agar performa I/O database tetap optimal tanpa mengganggu ketersediaan layanan (zero downtime)

kelebihan pg_repack:
- Online processing: (melakukan maintenance tanpa lock table dan zero downtime)
- Space Recovery: Mengembalikan free space akibat fragmentasi (bloat) langsung ke OS
- Performance Boost: Meningkatkan efisiensi I/O dan membangun ulang indeks secara optimal


kekurangan pg_repack
- beban resources tinggi CPU dan I/O
- dilakukan pada table yang memiliki primary key atau unique key not null
- mengkonsumsi banyak ruang disk. membutuhkan setidaknya 2x lipat disk dari ukuran table





1. Verifikasi utility pg_repack pada level OS Linux. Memastikan tool telah terinstal dan siap digunakan melalui pengecekan versi

   <img width="527" height="68" alt="Screenshot (519)" src="https://github.com/user-attachments/assets/4ec04e7b-8e91-4ab4-aacc-81ccdb463f97" />


2. Melakukan konfigurasi pada postgresql.conf dengan menambahkan pg_repack ke dalam parameter shared_preload_libraries. Ini diperlukan agar ektensi dapat dimuat oleh server postgresql

   <img width="750" height="131" alt="Screenshot (520)" src="https://github.com/user-attachments/assets/b0a0f17a-6678-4e6c-9815-3013d99d6657" />


3. Aktivasi ekstensi melalui perintah CREATE EXTENSION pg_repack pada database target untuk mengaktifkan fungsi-fungsi maintenance 

   <img width="1018" height="356" alt="Screenshot (521)" src="https://github.com/user-attachments/assets/5bc9c370-0510-4151-bf8c-e88aa974637b" />


4. Membuat data dummy sebagai simulasi real workload dan menyediakan basis data pengujian bloat

   <img width="1689" height="340" alt="Screenshot (522)" src="https://github.com/user-attachments/assets/0d0d2dee-0615-4313-bf90-945da530dd9d" />


5. Create new index pada tabel untuk monitoring perilaku dan dampak pembengkakan (bloat) pada struktur indeks saat operasi DML terjadi

   <img width="643" height="59" alt="Screenshot (523)" src="https://github.com/user-attachments/assets/2784184b-9f37-4a27-a91f-f64dc4f1f819" />


6. Verifikasi baseline performa & status awal database (autovacuum, dead tuples, dan ukuran disk). Membuktikan autovacuum standar tidak cukup untuk mengembalikan disk space ke OS setelah operasi besar

   <img width="1692" height="243" alt="Screenshot (525)" src="https://github.com/user-attachments/assets/5d1608d6-c125-433e-a981-6b15f7950da3" />
   <img width="1082" height="147" alt="Screenshot (533)" src="https://github.com/user-attachments/assets/9c153672-bbe3-4647-ac33-eca339060d99" />
   <img width="1178" height="274" alt="Screenshot (526)" src="https://github.com/user-attachments/assets/e6b90f3d-2e34-4874-9353-4d788468de36" />
   <img width="388" height="113" alt="Screenshot (534)" src="https://github.com/user-attachments/assets/79fd9ff1-99e3-4864-a637-d1e110f65474" />
   - Walaupun autovacuum aktif secara default, autovacuum standar tidak membantu banyak dalam mengelola size disk pada table apabila table mengalami operasi DML yang cukup besar.
     Karena itu, size disk table dan index akan terjadi bloat disk size yang signifikan


7. Simulasi manipulasi data melalui perintah UPDATE pada kolom tabel. Memicu terciptanya dead tuples

   <img width="728" height="651" alt="Screenshot (529)" src="https://github.com/user-attachments/assets/7ab40ebc-bf76-49d4-a7c0-21a4d951a3ee" />


8. Validasi dampak bloat pada table & index
   - bloat pada disk size dan disk size index bisa menyebabkan menurunnya performa database seperti query, performa index, dan melakukan backup data lebih lama.

   <img width="1700" height="674" alt="Screenshot (530)" src="https://github.com/user-attachments/assets/dcaba600-d12a-4910-96c0-fc341f745fe0" />
   - idx_orders      : 6848kb > 12mb
   - idx_orders_pkey : 21mb > 34mb
   - n_dead_tup      : 0 > 269367
   - execution_time  : 1.541 > 4.413


10. Eksekusi pg_repack dengan user postgres dan opsi -e untuk mereorganisasi tabel secara real-time tanpa lock table dan zero downtime
   - untuk merapihkan size disk table, size disk index, dan statistik rencana eksekusi ke performa terbaiknya

   <img width="1690" height="315" alt="Screenshot (531)" src="https://github.com/user-attachments/assets/fce937b3-be3c-4429-9320-3a5a52854330" />


11. Validasi efektivitas maintenance. Mengembalikan performa database dan performa index secara optimal
    
    <img width="1680" height="686" alt="Screenshot (532)" src="https://github.com/user-attachments/assets/7b109f50-f191-41b9-9f51-077f13842431" />
    - idx_orders      : 6848kb > 12mb > 6856kb
    - idx_orders_pkey : 21mb > 34mb > 21mb
    - n_dead_tup      : 0 > 269367 > 0
    - execution_time  : 1.541 > 4.413 > 0.713


12. Eksekusi ANALYZE VERBOSE untuk update data statistics
    - Analyze Verbose lebih informatif dari analyze standar karena menampilkan detail teknis mengenai informasi sampel data

      <img width="1443" height="124" alt="Screenshot (535)" src="https://github.com/user-attachments/assets/a8e410b7-e4a2-4925-b73c-2345d2536c47" />


13. Validasi akhir pada query planner setelah update data statistics. Query planner bekerja lebih akurat, menghasilkan execution time yang paling efisien (0.597ms).

    <img width="1162" height="273" alt="Screenshot (536)" src="https://github.com/user-attachments/assets/d0fb2a23-daf0-4196-9215-4c1065ac0d3a" />
    - execution_time  : 1.541 > 4.413 > 0.713 > 0.597





    








   


