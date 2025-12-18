pada sesi kali ini, saya akan membuat role, permission, user, dan security database pada level keamanan yang bisa diterapkan di level enterprise, diantaranya:
- menutup akses database secara public 
- membuat role operasional 
- akses role ke dalam object baru secara otomatis
- membuat user dengan hak akses role operasional
- mempertimbangkan membuat schema selain public
- mengaktifkan security level ssl
- mengaktifkan audit menggunakan pgaudit.log


1. membuat user sebagai bahan testing untuk login ke dalam database server dengan hak akses public
   
   <img width="1165" height="523" alt="Screenshot (399)" src="https://github.com/user-attachments/assets/eab0eee5-9df5-4749-9bd2-353c84d30198" />
   - tanpa izin apapun, user bisa masuk ke dalam database dengan mudah. dengan begitu user bisa membuat object atau mengakses data dengan mudah.


2. menghapus semua izin ke dalam database dari role public
   
   <img width="1513" height="186" alt="Screenshot (400)" src="https://github.com/user-attachments/assets/23278db6-5cb5-4094-8da4-c8d5ebded373" />
   <img width="1404" height="125" alt="Screenshot (401)" src="https://github.com/user-attachments/assets/4d8bfb02-59af-4075-9c78-bebb56cf1a19" />
   - dengan cara ini, setiap user atau role yang baru saja dibuat perlu izin connect ke dalam database, sehingga user atau role yang baru saja dibuat tidak dengan mudah bisa login ke dalam database server tanpa penguncian database.


3. membuat database, schema, dan tabel sebagai bahan testing untuk role operasional

   <img width="1458" height="747" alt="Screenshot (403)" src="https://github.com/user-attachments/assets/7e3e355c-dbd1-438e-bf29-4ce7abb3f56b" />
   - revoke all on database : penguncian tahap ke-1 sebagai pintu utama
   - revoke all on schema : penguncian tahap ke-2 sebelum menuju ruangan data
   - revoke all on table : penguncian tahap ke-3 sebelum mengakses data


4. membuat role operasional **user_reader** secara ketat
   
   <img width="1147" height="323" alt="Screenshot (404)" src="https://github.com/user-attachments/assets/0f90adce-327e-47cf-be2b-8ff482a58a0e" />
   - grant connect on database : mengizinkan role/user untuk bisa masuk menuju pintu utama
   - grant usage on schema : mengizinkan role/user untuk bisa masuk ke ruangan data
   - grant select on table : mengizinkan role/user untuk bisa mengakses data
   - alter default privileges : setiap table baru dibuat otomatis masuk ke dalam role operasional tanpa perlu grant select secara berulang.


5. membuat user dan memberikan hak akses role **user_reader**

   <img width="1209" height="315" alt="Screenshot (406)" src="https://github.com/user-attachments/assets/32d2aabd-ed1c-408e-8855-007aca1190c2" />


6. mendaftarkan schema yang baru ke dalam source_path, bertujuan untuk:
   - ketika user ingin mengakses data pada table di dalam schema selain schema public(default), user root perlu mendaftarkan schema baru ke dalam source_path agar user operasional bisa mengakses data pada table selain schema public.

     <img width="846" height="270" alt="Screenshot (407)" src="https://github.com/user-attachments/assets/1487c8d0-a307-428c-bdd5-366a930155df" />

     ![Uploading Screenshot (408).png…]()




6. testing user untuk bisa login ke dalam database dan mengakses schema hingga mengakses data.
   

   

   
   

   


8.
9.
10. sebelum membuat user, memastikan setiap database dalam keadaan zero trust dengan menghapus semua izin kepada object tersebut. karena secara default, setiap database yang dibuat atau tersedia bisa diakses oleh user baru yang bisa membuat object lain pada database.
   <img width="1160" height="209" alt="Screenshot (396)" src="https://github.com/user-attachments/assets/563a6527-e08c-4388-8632-2e74da9b64d6" />
   dengan cara ini, setiap role yang mengakses database atau schema membutuhkan izin connect (grant connect)
   memperketat keamanan database server dengan memberikan hak akses kepada role seminimal mungkin (least privilege)


11. membuat database dan user sebagai bahan testing
   
   
   

   
