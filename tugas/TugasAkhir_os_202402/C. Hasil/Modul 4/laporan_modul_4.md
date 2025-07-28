# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `Fakhri Fahmi Ramadan`
**NIM**: `240202897`
**Modul yang Dikerjakan**:
Modul 4 – XV6: Subsistem Kernel Alternatif
---

## 💠 Deskripsi Implementasi

Saya mengimplementasikan dua fitur penting dalam kernel XV6:

1. **System Call `chmod(path, mode)`**  
   Untuk mengatur perizinan sederhana file (read-only atau read-write) melalui bit `mode` di inode.

2. **Driver Device `/dev/random`**  
   Sebuah character device yang menghasilkan byte acak saat dibaca oleh program user.

---

## 📂 File yang Dimodifikasi / Ditambahkan

| File           | Perubahan                                                                 |
|----------------|---------------------------------------------------------------------------|
| `inode.h`      | Tambah field `short mode` di struct `inode`                               |
| `file.c`       | Tambah pengecekan `mode` di `filewrite()`, dan daftarkan driver random    |
| `fs.h`         | Jika perlu, deklarasi tambahan                                             |
| `sysfile.c`    | Implementasi syscall `sys_chmod()`                                        |
| `syscall.c`    | Registrasi syscall `chmod()`                                              |
| `syscall.h`    | Tambahkan `#define SYS_chmod`                                             |
| `user.h`       | Deklarasi user-level syscall `chmod()`                                    |
| `usys.S`       | Entry syscall `chmod()`                                                   |
| `random.c`     | Implementasi handler driver `/dev/random`                                 |
| `init.c`       | Tambahkan `mknod("/random", 3, 0)` untuk device node                      |
| `chmodtest.c`  | Program uji untuk `chmod()`                                               |
| `randomtest.c` | Program uji untuk `/random`                                               |
| `Makefile`     | Tambahkan `_chmodtest` dan `_randomtest` ke variabel `UPROGS`            |

---

## Bagian A – System Call chmod()

### Penambahan ke `inode`:

```c
short mode; // 0 = read-write, 1 = read-only
```

### Implementasi syscall:

```c
int sys_chmod(void) {
  char *path;
  int mode;
  struct inode *ip;

  if(argstr(0, &path) < 0 || argint(1, &mode) < 0)
    return -1;

  begin_op();
  if((ip = namei(path)) == 0){
    end_op();
    return -1;
  }

  ilock(ip);
  ip->mode = mode;
  iupdate(ip);  // opsional
  iunlock(ip);
  end_op();

  return 0;
}
```

### Validasi penulisan file:

```c
if(f->ip && f->ip->mode == 1)  // mode == 1 berarti read-only
  return -1;
```

---

## 📄 Program Uji `chmodtest.c`

```c
int main() {
  int fd = open("myfile.txt", O_CREATE | O_RDWR);
  write(fd, "hello", 5);
  close(fd);

  chmod("myfile.txt", 1);  // ubah jadi read-only

  fd = open("myfile.txt", O_RDWR);
  if(write(fd, "world", 5) < 0)
    printf(1, "Write blocked as expected\n");
  else
    printf(1, "Write allowed unexpectedly\n");

  close(fd);
  exit();
}
```
## ✅ Output Diharapkan

```bash
$ chmodtest
Write blocked as expected

---

## Bagian B – Device Pseudo /dev/random

### Struktur `randomread`:

```c
static uint seed = 123456;

int randomread(struct inode *ip, char *dst, int n) {
  for(int i = 0; i < n; i++) {
    seed = seed * 1664525 + 1013904223;
    dst[i] = seed & 0xFF;
  }
  return n;
}
```

### Registrasi di `file.c`:

```c
[3] = { 0, randomread }, // device major = 3
```

### Tambahkan node device:

```c
mknod("/random", 3, 0);
```

---

## 📄 Program Uji `randomtest.c`

```c
int main() {
  char buf[8];
  int fd = open("/random", 0);
  if(fd < 0){
    printf(1, "cannot open /dev/random\n");
    exit();
  }

  read(fd, buf, 8);
  for(int i = 0; i < 8; i++)
    printf(1, "%d ", (unsigned char)buf[i]);
  printf(1, "\n");

  close(fd);
  exit();
}
```

---

## ✅ Output Diharapkan

```
$ randomtest
159 114 41 116 67 198 109 232
```
### 📸 Screenshot:
<img width="1919" height="1079" alt="modul 4" src="https://github.com/user-attachments/assets/665fa7f7-e281-45d4-acc1-16519992167b" />


---

## ⚠️ Kendala yang Dihadapi

Selama implementasi, beberapa kendala teknis yang cukup menantang berhasil diatasi, di antaranya:

---

## ⚠️ Kendala dan Solusi

| No | Kendala                                                                                 | Solusi                                                                                   |
|----|------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| 1  | Field `short mode;` awalnya ditambahkan ke file `fs.h`, padahal tidak berpengaruh di inode memori | Dipindahkan ke `inode.h`, karena `struct inode` digunakan aktif di manajemen memori     |
| 2  | Duplikasi atau konflik antara `struct sleeplock` dan `spinlock` saat include header      | Gunakan `#ifndef/#define` (include guard) dan atur urutan `#include` secara tepat       |
| 3  | Fungsi `randomread()` tidak dikenali di `file.c` saat kompilasi                         | Tambahkan `extern int randomread(...);` sebelum deklarasi `devsw[]` di `file.c`         |
| 4  | Device `/dev/random` gagal dibuat karena `mknod()` tidak dipanggil                      | Tambahkan `mknod("/random", 3, 0);` di akhir fungsi `init()` di `init.c`                |
| 5  | Program user `chmodtest` dan `randomtest` tidak terdeteksi saat `make`                  | Tambahkan `_chmodtest` dan `_randomtest` ke variabel `UPROGS` di `Makefile`             |

---

---

## 📎 Referensi

- [xv6-public](https://github.com/mit-pdos/xv6-public)
- xv6 book by MIT (rev11)
- LCG random generator: Numerical Recipes

