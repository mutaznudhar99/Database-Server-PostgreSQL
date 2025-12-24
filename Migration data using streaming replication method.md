pada sesi kali ini, saya akan melakukan migrasi data dari server primary ke server standby menggunakan metode streaming replikasi (replikasi fisik)
untuk kebutuhan migrasi data near zero downtime. hal ini bisa dilakukan untuk maintenance server primary dan skalabilatas baca secara manual.


hal yang dipersipkan:
- 1 server primary
- 1 server standby
- database sebagai bahan testing switchover
- tool backup pgbackrest





1. 

