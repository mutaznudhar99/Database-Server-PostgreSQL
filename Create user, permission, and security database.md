pada sesi kali ini, saya akan membuat user dengan role operasional **user_read** dan **user_writer**. role merupakan sebuah group operasional yang dapat memberikan hak akses kepada satu atau lebih user, sedangkan user merupakan entitas individu yang menjalankan tugas operasional.



1. sebelum membuat user, memastikan database atau schema public dalam keadaan zero trust dengan menghapus semua izin kepada object tersebut. karena secara default, setiap database yang dibuat tersimpan didalam schema public dan user yang dibuat tersimpan didalam role publc yang bisa membuat object pada public schema.  
