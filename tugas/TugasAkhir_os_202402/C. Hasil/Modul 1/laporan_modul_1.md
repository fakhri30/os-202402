# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `Fakhri Fahmi Ramadan`
**NIM**: `240202897`
**Modul yang Dikerjakan**:
`(Contoh: Modul 1 – System Call dan Instrumentasi Kernel)`

---

## 📌 Deskripsi Singkat Tugas

Modul ini menambahkan dua system call baru pada sistem xv6-public:

- `getpinfo()`: Menampilkan informasi proses aktif seperti PID, ukuran memori, dan nama proses.
- `getreadcount()`: Menghitung jumlah pemanggilan system call `read()` sejak sistem booting.

---

## 🛠️ Rincian Implementasi

- Menambahkan dua system call `getpinfo()` dan `getreadcount()` di `sysproc.c` dan mendaftarkannya di `syscall.c`.
- Menambahkan entri syscall ke `syscall.h`, `user.h`, dan `usys.S`.
- Mendefinisikan struktur `struct pinfo` di `proc.h`.
- Menambahkan counter global `readcount` dan memperbaruinya dalam `sys_read()` di `sysfile.c`.
- Menggunakan `acquire()` dan `release()` untuk sinkronisasi akses `ptable`.
- Menambahkan program uji `ptest.c` dan `rtest.c`.
- Menambahkan `_ptest` dan `_rtest` ke daftar `UPROGS` dalam `Makefile`.

---

## ✅ Uji Fungsionalitas

- `ptest`: Menguji `getpinfo()` untuk menampilkan proses-proses aktif.
- `rtest`: Menguji `getreadcount()` dengan menampilkan nilai read count sebelum dan sesudah membaca input dari user.

---

## 📷 Hasil Uji

### 📍 Contoh Output `ptest`

```
== Info Proses Aktif ==
PID     MEM     NAME
1       4096    init
2       2048    sh
3       2048    ptest
```

### 📍 Contoh Output `rtest`:

```
Read Count Sebelum: 4
hello
Read Count Setelah: 5
```

### 📸 Screenshot:
<img width="479" height="233" alt="Modul1" src="https://github.com/user-attachments/assets/77dfe4a2-df04-46b2-8cd3-bb0fdf59603b" />


---

## ⚠️ Kendala yang Dihadapi

* Menangani pointer dari user space menggunakan `argptr()`
* Sinkronisasi akses ke `ptable` agar tidak terjadi race condition
* Kesalahan umum seperti:
  - Salah akses pointer (`.` vs `->`)
  - Lupa meng-include `spinlock.h`
  - Gagal membaca hasil syscall karena kesalahan definisi argumen

---

## 📚 Referensi

* Buku xv6 MIT: [https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* Repositori xv6-public: [https://github.com/mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public)
* Diskusi praktikum dan dokumentasi di Stack Overflow dan GitHub Issues

---

