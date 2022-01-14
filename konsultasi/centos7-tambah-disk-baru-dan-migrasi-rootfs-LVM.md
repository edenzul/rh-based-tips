# Centos7 tambah disk baru dan migrasi rootfs ke LVM

Dari: Arcturus Archie - Linux Indonesia
[https://www.facebook.com/zulfikar.denny/posts/607393847190580:13](https://www.facebook.com/zulfikar.denny/posts/607393847190580:13)

## Pertanyaan

server CentOS 7. 
Punya 2 Buah SSD dengan kapasitas 480 GB. 
/dev/sda/ >>> 480 GB
/dev/sdb/ >>> 480 GB

Storage tidak di RAID
CentOS terinstall di /dev/sda (tanpa LVM)

	/dev/sda1 >> BOOT
	/dev/sda2 >> SWAP
	/dev/sda3 >> /

/dev/sdb/ tidak ada partisi. --> disk baru

Bagaimana menggabungkan ke 2 disk tersebut tanpa perlu install ulang supaya bisa dijadikan 1 (960 GB).


## Jawab

1. Buat LVM di sdb
	**lv_rootfs
	lv_home**
2. Format LV yg baru dibuat
3. mount lv_rootfs dan lv_home ke /mnt/
4. copy seluruh direktory / ke lv_rootfs dan lv_home
5. buat entry baru grub untuk filesystem yang baru
(yang lama tetap dibiarkan tanpa perubahan)
6. edit fstab supaya mengarah ke filesystem yang baru (yang lama tetap dibiarkan tanpa perubahan)
7. tes booting
8. jika booting tidak ada kendala dan semua servis berjalan normal di filesystem LVM, maka sda3 bisa dihapus dan partisinya dijadikan LVM lalu di merge/gabung dengan LVM yg baru.

## Details step-by-step
### Tulisan dibawah ini akan diupdate lagi
**sebelum:**
/dev/sda1	-> /swap
/dev/sda2	-> swap
/dev/sda3 	-> /

**setelah diubah:**
/dev/sda1	-> /boot
/dev/sda2	-> swap
/dev/sda3 	-> /

fdisk /dev/sdb
-> create partisi baru sdb1

pvcreate /dev/sdb1

vgcreate vg_centos /dev/sdb1 -Z y

lvcreate -L 4000m vg_centos -n lv_rootfs -a ay

lvcreate -L 4000m vg_centos -n lv_home -a ay

format fs:

---
**AWAS!!! setelah proses ini selesai, anda tidak bisa kembali ke filesystem yang lama. harap cek dulu apakah aplikasi sudah berjalan normal di filesystem LVM apa belum?
kalau belum, anda masih bisa kembali ke filesystem yang lama!!!!**

hapus partisi sda2 dan sda3
buat partisi baru sda2 -> lvm

reboot

---
fdisk /dev/sda
#hapus partisi sda2 dan sda3, lalu buat partisi baru sda2 dengan tipe linux lvm

pvcreate /dev/sda2

vgextend vg_centos /dev/sda2

lvextend -L 8000m /dev/vg_centos/lv_rootfs

xfs_growfs /dev/vg_centos/lv_rootfs
