Pada sesi ini, saya akan mengimplementasi database security pada PostgreSQL. Fokus utama meliputi password otentifikasi (Hardening), manajemen peran (Role-based Access Control), penggunaan skema kustom isolasi data, dan mengaktifkan modul audit menggunakan pgAudit utility sebagai monitor setiap aktivitas users secara real-time

Strategi security yang diterapkan:
- Zero trust access control : Menutup akses publik default. Mencegah login tanpa izin eksplisit
- Least privilige principal : Membatasi hak akses role hanya pada objek yang diperlukan
- Automated permission      : Set default privileges, tabel baru secara otomatis mewarisi izin keamanan
- Advanced monitoring       : Integrasi pgAudit untuk merekam trail logs aktivitas DML/DDL dari users
 

1. membuat user sebagai bahan testing untuk login ke dalam database server dengan hak akses public
   
   <img width="1165" height="523" alt="Screenshot (399)" src="https://github.com/user-attachments/assets/eab0eee5-9df5-4749-9bd2-353c84d30198" />
   - tanpa izin apapun, user bisa masuk ke dalam database dengan mudah. dengan begitu user bisa membuat object atau mengakses data dengan mudah.


2. menghapus semua izin ke dalam database dari role public
   
   <img width="1513" height="186" alt="Screenshot (400)" src="https://github.com/user-attachments/assets/23278db6-5cb5-4094-8da4-c8d5ebded373" />
   <img width="1404" height="125" alt="Screenshot (401)" src="https://github.com/user-attachments/assets/4d8bfb02-59af-4075-9c78-bebb56cf1a19" />
   - dengan cara ini, setiap user atau role yang baru saja dibuat perlu izin connect ke dalam database, sehingga user atau role yang baru saja dibuat tidak dengan mudah bisa login ke dalam database server tanpa penguncian database.


3. membuat database, schema, dan tabel sebagai bahan testing untuk role operasional

   <img width="1263" height="608" alt="Screenshot (409)" src="https://github.com/user-attachments/assets/c0f65897-eb08-4dae-b6fd-bae91b30ff19" />
   - revoke all on database : penguncian tahap ke-1 sebagai pintu utama
   - revoke all on schema : penguncian tahap ke-2 sebelum menuju ruangan data
   - revoke all on table : penguncian tahap ke-3 sebelum mengakses data


4. membuat role operasional **user_reader** secara ketat
   
   <img width="1327" height="325" alt="Screenshot (410)" src="https://github.com/user-attachments/assets/66887b67-e8ca-4df0-b9cf-2ce906c359ae" />
   - grant connect on database : mengizinkan role/user untuk bisa masuk menuju pintu utama
   - grant usage on schema : mengizinkan role/user untuk bisa masuk ke ruangan data
   - grant select on table : mengizinkan role/user untuk bisa mengakses data
   - alter default privileges : setiap table baru dibuat otomatis masuk ke dalam role operasional tanpa perlu grant select secara berulang.


5. membuat user dan memberikan hak akses role **user_reader**

   <img width="1328" height="357" alt="Screenshot (411)" src="https://github.com/user-attachments/assets/e711bfc2-a2ff-4bd9-9aeb-3338c172673d" />


6. mendaftarkan schema yang baru ke dalam source_path, bertujuan untuk:
   - ketika user ingin mengakses data pada table di dalam schema selain schema public(default), user root perlu mendaftarkan schema baru ke dalam source_path agar user operasional bisa mengakses data pada table selain schema public.

     <img width="846" height="270" alt="Screenshot (407)" src="https://github.com/user-attachments/assets/1487c8d0-a307-428c-bdd5-366a930155df" />

     <img width="850" height="211" alt="Screenshot (408)" src="https://github.com/user-attachments/assets/b11e9906-43ef-4793-8afb-6b47fd389ed2" />


7. testing user untuk bisa login ke dalam database dan mengakses schema hingga mengakses data.

   <img width="1033" height="302" alt="Screenshot (412)" src="https://github.com/user-attachments/assets/239c419e-f361-4225-95b0-eb1262a68c74" />
   - dengan adanya user operasional, tidak perlu lagi menggunakan user root untuk operasional database server, terkecuali untuk kepentingan administrator.


8. di tahap level lanjutan, mengaktifkan enkripsi ssl dan pgaudit menjadi skema yang kokoh untuk keamanan level tinggi.


9. cek user beserta role operasional yang sedang digunakan

   <img width="1124" height="234" alt="Screenshot (413)" src="https://github.com/user-attachments/assets/be2c9c41-9a3e-4381-adbd-27d43895ecd1" />


10. cek grant atau akses yang diberikan kepada role operasional

    <img width="1031" height="683" alt="Screenshot (414)" src="https://github.com/user-attachments/assets/1e41ba07-9ae5-40d3-8820-098f46d82174" />


11. selanjutnya, mengaktifkan pg_audit untuk mencatat setiap user yang login dan mengakses data pada database dan disimpan di dalam file logging.
    - install postgresql-versi-pgaudit

      <img width="1357" height="677" alt="Screenshot (504)" src="https://github.com/user-attachments/assets/0d3a8cb5-00f6-4e09-8f67-e8565b2393be" />


12. konfigurasi postgresql.conf dengan menambahkan parameter pgaudit

    <img width="1137" height="201" alt="Screenshot (518)" src="https://github.com/user-attachments/assets/7c99da5c-e6a7-459e-9dc5-a34bcb14f52f" />
    - all : mencatat semua tindakan yang dilakukan oleh user pada database server, proses audit paling berat(tidak rekomen)
    - log_line_prefix : mencatat dari mana asal user


13. membuat extension pgaudit pada database yang dituju untuk mengaktifkan fitur audit

    <img width="564" height="230" alt="Screenshot (511)" src="https://github.com/user-attachments/assets/5547cd2e-f24f-4a76-bdc8-9b600b5a7dd2" />


14. konfigurasi pg_hba.conf untuk menerima akses ke database dari server luar

    <img width="1166" height="276" alt="Screenshot (514)" src="https://github.com/user-attachments/assets/7236d5b9-315f-4f2d-98f0-1193d12b4b82" />
   - trust : memberikan kepercayaan penuh pada user di luar server tanpa verifikasi apapun. dilakukan untuk testing akses database.


15. install postgresql-client-versi di server berbeda kemudian login ke dalam database server dan membuat database beserta table untuk bahan audit
    - sudo apt-get install postgresql-client-versi

      <img width="1229" height="414" alt="Screenshot (515)" src="https://github.com/user-attachments/assets/262b6114-bfcf-4d7c-8100-b4f28a73728f" />


16. login ke dalam database server dan melakukan read data pada server primary

    <img width="748" height="245" alt="Screenshot (516)" src="https://github.com/user-attachments/assets/ab4b54a0-e7de-441d-836c-c5df82f197f8" />


17. cek log file audit di server primary

    <img width="1694" height="322" alt="Screenshot (517)" src="https://github.com/user-attachments/assets/1d9f1553-fb97-4d96-8bd9-bd50cd16d65a" />
    - postgres@aaa dari 192.168.1.12 : melakukan create table
    - postgres@aaa dari 192.168.1.12 : melakukan insert table
    - postgres@aaa dari 192.168.1.12 : melakukan reading table


   




   
   
   

   
