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

fdisk /dev/sdb
-> create partisi baru sdb1

buat physical volume, volume group dan logical volume:
```
pvcreate /dev/sdb1
vgcreate vg_centos /dev/sdb1 -Z y
lvcreate -L 4000m vg_centos -n lv_rootfs -a ay
lvcreate -L 4000m vg_centos -n lv_home -a ay
```

format logical volume:
```
mkfs.xfs /dev/vg_centos/lv_rootfs -L rootfs
mkfs.xfs /dev/vg_centos/lv_home -L homedir
```

mount filesystem baru ke /mnt dan copy isi sda ke sdb:
```
ls /mnt
mount /dev/vg_centos/lv_rootfs /mnt
mkdir /mnt/home
mkdir /mnt/{proc,sys,mnt}
mount /dev/vg_centos/lv_home /mnt/home
rsync -a / --exclude=/mnt  --exclude=/proc  --exclude=/sys /mnt
```

update fstab rootfs yg baru:
```vi /mnt/etc/fstab
```
berikan comment baris untuk **/** dan **/home**, lalu tambahkan baris baru untuk filesystem yang baru:
```
LABEL=rootfs / xfs defaults 0 0 
LABEL=homedir /home xfs defaults 0 0
```
simpan **:wq!**

modifikasi dracut supaya memasukkan support lvm saat mengenerate initramdisk:
```
vi /etc/dracut.conf
```
update baris berikut:
```
add_dracutmodules+="lvm"
lvmconf="yes"
```

ulangi langkah yg sama untuk /mnt/etc/dracut.conf

generate initramfs:
```
dracut -f
```

update config grub:
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
disini terlihat filesystem yang baru sudah terdeteksi.

unmount filesystem yang baru, lalu reboot.
```
umount /mnt/home
umount /mnt
reboot
```

di grub menu, filesystem baru sudah terdeteksi.
arahkan kursor ke baris filesystem baru, tekan tombol **e** untuk edit.
tambahkan sebelum **insmod part_msdos**:
```
insmod lvm
```
**ctrl+x** untuk melanjutkan booting

setelah masuk ke dalam fs yg baru, reinstal bootloader dan generate config boot yg baru:
```
grub2-install /dev/sda
grub2-mkconfig -o /boot/grub2/grub.cfg
dracut -f
```
reboot dan lihat perubahan. saat ini tidak perlu ada manual seting di grub menu saat awal booting.

setelah system selesai reboot, cek dulu semua file, direktori dan semua servis apa bisa berjalan normal. jika sudah OK, silahkan lanjut step berikutnya untuk menghapus partisi fs lama dan menjadikannya bagian dari lvm yang baru.

---
**AWAS!!! setelah proses ini selesai, anda tidak bisa kembali ke filesystem yang lama. harap cek dulu apakah aplikasi sudah berjalan normal di filesystem LVM apa belum?
kalau belum, anda masih bisa kembali ke filesystem yang lama!!!!**

hapus partisi sda2 dan sda3
buat partisi baru sda2 dengan menggunakan semua space dari sda2 dan sda3 -> lvm

---
fdisk /dev/sda
di langkah ini, hapus partisi sda2 dan sda3, lalu buat partisi baru sda2 dengan tipe linux lvm

berikutnya definisikan sda2 sbg physical volume, tambahkan sda2 ke dalam volumegroup vg_centos, lalu perbesar ukuran logicalvolume lv_rootfs:
```
pvcreate /dev/sda2
vgextend vg_centos /dev/sda2
lvextend -L 8000m /dev/vg_centos/lv_rootfs
```
terakhir, perbesar ukuran filesystem agar semua space yang tersedia di lv_rootfs bisa digunakan:
```
xfs_growfs /dev/vg_centos/lv_rootfs
```

Jika **homedir** memerlukan space tambahan, **lv_home** bisa mengambil alokasi dari **vg_centos** untuk ditambahkan sebagai space homedir.
```
lvextend -L 50m /dev/vg_centos/lv_home
xfs_growfs /dev/vg_centos/lv_home
```
