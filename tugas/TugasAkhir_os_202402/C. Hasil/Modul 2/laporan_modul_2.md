# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi  
**Semester**: Genap / Tahun Ajaran 2024–2025  
**Nama**: Fakhri Fahmi Ramadan  
**NIM**: 240202897  
**Modul**: Modul 2 – Penjadwalan CPU Non-Preemptive Berbasis Prioritas

---

## 📌 Deskripsi Singkat Tugas

Tugas pada modul ini adalah memodifikasi sistem penjadwalan proses di kernel xv6. Algoritma bawaan Round-Robin diganti dengan metode 
**penjadwalan berdasarkan prioritas (non-preemptive)**. Dengan sistem ini, proses yang memiliki nilai prioritas lebih rendah (berarti lebih penting) akan dieksekusi lebih dahulu. Untuk mendukungnya, ditambahkan syscall baru `set_priority()` agar prioritas bisa diatur dari user space.

---

---

## 🛠️ Rincian Implementasi

* Menambahkan field `int priority` pada struct `proc` di `proc.h`
* Menyesuaikan `allocproc()` dan `scheduler()` di `proc.c`
* Menambahkan syscall `set_priority(int)`:
  - Tambahan di `syscall.h`, `user.h`, `usys.S`, `syscall.c`, dan `sysproc.c`
* Program uji `ptest.c` dimodifikasi untuk menguji perilaku scheduler

---

## ✅ Uji Fungsionalitas

* `ptest modul2`: menguji penjadwalan berdasarkan prioritas
  - `Child 2` diberi prioritas tinggi (`10`)
  - `Child 1` diberi prioritas rendah (`90`)
  - Diharapkan output: `Child 2 selesai` muncul sebelum `Child 1 selesai`

---

## 📷 Hasil Uji

### 📍 `ptest`:

```
Child 2 selesai
Child 1 selesai
Parent selesai
```

### 📸 Screenshot:
<img width="1913" height="1076" alt="Modul2" src="https://github.com/user-attachments/assets/49efe531-fe75-4fb1-856c-61aae9fc7d89" />


---

## ⚠️ Kendala yang Dihadapi

## ⚠️ Kendala dan Solusi

Selama proses pengerjaan modul ini, terdapat beberapa hambatan teknis yang dihadapi. Berikut adalah kendala utama beserta langkah penyelesaiannya:

| Kendala                                                                 | Solusi                                                                           |
|-------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| Struktur `proc` dan `cpu` tidak dikenali dalam fungsi `scheduler()`    | Menambahkan deklarasi `extern struct cpu *cpu;` serta menggunakan `mycpu()`     |
| Proses prioritas tinggi belum sempat dijadwalkan saat proses dimulai   | Memberi jeda dengan `sleep(1)` setelah `fork()` agar semua proses bisa siap dijadwalkan |
```

## 📚 Referensi

* Buku xv6 MIT: [https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)  
* Repositori xv6-public: [https://github.com/mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public)  
* Diskusi praktikum, GitHub Issues, Stack Overflow
  
---
