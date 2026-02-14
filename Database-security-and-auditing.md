Pada sesi ini, saya akan mengimplementasikan database security hardening pada PostgreSQL dengan pendekatan berlapis (Defense in Depth). Meliputi penguatan autentikasi, manajemen role berbasis RBAC (Role-Based Access Control), isolasi data melalui custom schema, dan pengaktifan modul pgAudit sebagai instrumen compliance untuk track record activity users secara detail

Strategi security yang diterapkan:
- Zero trust access control : Menutup akses publik default. Mencegah login tanpa izin eksplisit
- Least privilige principal : Membatasi hak akses role hanya pada objek yang diperlukan
- Automated permission      : Set default privileges, tabel baru secara otomatis mewarisi izin keamanan
- Advanced monitoring       : Integrasi pgAudit untuk merekam trail logs aktivitas DML/DDL dari users
 

1. Melakukan login testing menggunakan user without permission. Membuktikan kerentanan akses PUBLIC default, memungkinkan user asing create object atau data reading tanpa restriksi
   
   <img width="1165" height="523" alt="Screenshot (399)" src="https://github.com/user-attachments/assets/eab0eee5-9df5-4749-9bd2-353c84d30198" />
   - tanpa izin apapun, user bisa masuk ke dalam database dengan mudah. dengan begitu user bisa membuat object atau mengakses data dengan mudah.


2. Melakukan hardening global permission dengan REVOKE semua izin dari role PUBLIC terhadap database. Memaksa setiap new role memiliki izin CONNECT secara eksplisit sebelum dapat mengakses server
   
   <img width="1513" height="186" alt="Screenshot (400)" src="https://github.com/user-attachments/assets/23278db6-5cb5-4094-8da4-c8d5ebded373" />
   <img width="1404" height="125" alt="Screenshot (401)" src="https://github.com/user-attachments/assets/4d8bfb02-59af-4075-9c78-bebb56cf1a19" />


3. Melakukan data isolation setup. Menerapkan three-tier lock dengan menghapus seluruh hak akses pada level database, skema, dan tabel sebelum role operasional dibuat

   <img width="1263" height="608" alt="Screenshot (409)" src="https://github.com/user-attachments/assets/c0f65897-eb08-4dae-b6fd-bae91b30ff19" />
   - revoke all on database : penguncian tahap pertama sebagai pintu utama
   - revoke all on schema : penguncian tahap kedua sebelum menuju ruangan data
   - revoke all on table : penguncian tahap ketiga sebelum mengakses data


4. Create role operasional dengan access control terbatas. Menggunakan ALTER DEFAULT PRIVILEGES, memastikan otomatisasi izin akses pada new table tanpa harus memberi izin akses secara berulang
   
   <img width="1327" height="325" alt="Screenshot (410)" src="https://github.com/user-attachments/assets/66887b67-e8ca-4df0-b9cf-2ce906c359ae" />
   - grant connect on database : mengizinkan role/user untuk bisa masuk menuju pintu utama
   - grant usage on schema : mengizinkan role/user untuk bisa masuk ke ruangan data
   - grant select on table : mengizinkan role/user untuk bisa mengakses data
   - alter default privileges : setiap table baru dibuat otomatis masuk ke dalam role operasional tanpa perlu grant select secara berulang


5. Create real user dan set ke dalam role operasional, sehingga user mewarisi kebijakan role least privilege yang telah dikonfigurasi

   <img width="1328" height="357" alt="Screenshot (411)" src="https://github.com/user-attachments/assets/e711bfc2-a2ff-4bd9-9aeb-3338c172673d" />


6. Mendaftarkan schema custome ke dalam search_path user. Bertujuan agar user operasional dapat mengakses tabel secara langsung tanpa perlu menuliskan nama skema (schema qualification)

     <img width="846" height="270" alt="Screenshot (407)" src="https://github.com/user-attachments/assets/1487c8d0-a307-428c-bdd5-366a930155df" />

     <img width="850" height="211" alt="Screenshot (408)" src="https://github.com/user-attachments/assets/b11e9906-43ef-4793-8afb-6b47fd389ed2" />


7. Melakukan access testing. Memastikan user operasional dapat menjalankan fungsi data reading (SELECT) sesuai otorisasi, sekaligus memitigasi ketergantungan pada superuser (root) untuk daily tasks

   <img width="1033" height="302" alt="Screenshot (412)" src="https://github.com/user-attachments/assets/239c419e-f361-4225-95b0-eb1262a68c74" />


8. Verifikasi roles & privileges dengan melakukan audit internal melalui system table dan memastikan matriks akses sudah sesuai dengan kebijakan database security hardening

   <img width="1124" height="234" alt="Screenshot (413)" src="https://github.com/user-attachments/assets/be2c9c41-9a3e-4381-adbd-27d43895ecd1" />

   <img width="1031" height="683" alt="Screenshot (414)" src="https://github.com/user-attachments/assets/1e41ba07-9ae5-40d3-8820-098f46d82174" />


9. Instalasi dan aktivasi ekstensi pgAudit pada target database untuk menangkap track record setiap aktivitas SQL yang dilakukan user ke dalam log sistem

      <img width="1357" height="677" alt="Screenshot (504)" src="https://github.com/user-attachments/assets/0d3a8cb5-00f6-4e09-8f67-e8565b2393be" />


10. Konfigurasi postgresql.conf dengan mengaktifkan pgaudit.log. Set log_line_prefix untuk records connection metadata (user, IP, app) sebagai bukti forensik audit

    <img width="1137" height="201" alt="Screenshot (518)" src="https://github.com/user-attachments/assets/7c99da5c-e6a7-459e-9dc5-a34bcb14f52f" />
    - all   : mencatat semua tindakan (execution) yang dilakukan user pada database server: proses audit paling memakan konsumsi resource


11. Inisialisasi pgaudit extension dengan melakukan CREATE EXTENSION untuk mengaktifkan mesin audit pada database tertentu

    <img width="564" height="230" alt="Screenshot (511)" src="https://github.com/user-attachments/assets/5547cd2e-f24f-4a76-bdc8-9b600b5a7dd2" />

12. Konfigurasi pg_hba.conf untuk remote connection testing. Metode trust hanya dilakukan untuk memvalidasi connectivity

    <img width="1166" height="276" alt="Screenshot (514)" src="https://github.com/user-attachments/assets/7236d5b9-315f-4f2d-98f0-1193d12b4b82" />
   - trust : memberikan kepercayaan penuh pada user di luar server tanpa verifikasi apapun. dilakukan untuk testing akses database.


13. Simulasi user activity pada server berbeda menggunakan postgresql-client. Bertujuan trigger log record pada primary server, memastikan audit trail bekerja lintas network

    <img width="1229" height="414" alt="Screenshot (515)" src="https://github.com/user-attachments/assets/262b6114-bfcf-4d7c-8100-b4f28a73728f" />
    <img width="748" height="245" alt="Screenshot (516)" src="https://github.com/user-attachments/assets/ab4b54a0-e7de-441d-836c-c5df82f197f8" />


14. Audit log analysis dengan melakukan inspeksi pada file log primary. Menunjukkan deteksi terhadap aktivitas DML/DQL yang dilakukan oleh user eksternal beserta identitas IP asalnya

    <img width="1694" height="322" alt="Screenshot (517)" src="https://github.com/user-attachments/assets/1d9f1553-fb97-4d96-8bd9-bd50cd16d65a" />
    - postgres@aaa dari 192.168.1.12 : melakukan create table
    - postgres@aaa dari 192.168.1.12 : melakukan insert table
    - postgres@aaa dari 192.168.1.12 : melakukan reading table


   




   
   
   

   
