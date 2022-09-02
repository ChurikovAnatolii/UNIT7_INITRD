### 1. Попасть в систему без пароля несколькими способами
___

***-- Вариант (с rd.break). 
Запускаем виртуальную машину через [Vagrantfile](), жмем 'e', чтобы отредактировать параметры загрузки, добавляем параметр rd.break в строчку с linux16, тем самым прерываем монтирование рутовой файловой системы, она остается смонтированой в /sysroot***

> Перемонтируем sysroot в RW, применяем chroot, изменяем пароль

```console
mount -o, remount rw /sysroot
chroot sysroot
cd root
passwd
touch /.autorelabel
```

### 2. Установить систему с LVM, после чего переименовать VG
---
> перемонтируем систему в rw, переименуем Volume Group в centos
```console 
[root@localhost vagrant]# vgs
  VG         #PV #LV #SN Attr   VSize   VFree
  VolGroup00   1   2   0 wz--n- <38.97g    0 
[root@localhost vagrant]# vgrename VolGroup00 centos
  Cannot archive volume group metadata for VolGroup00 to read-only filesystem.
[root@localhost vagrant]# mount -o remount,rw /         
[root@localhost vagrant]# vgrename VolGroup00 centos
  Volume group "VolGroup00" successfully renamed to "centos"
  ```
  
  > Изменим название VG в  [/etc/fstab](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/fstab), [/etc/default/grub](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/grub), [/boot/grub2/grub.cfg](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/grub.cfg)  
  
  > Создадим initrd, ребутнемся и проверим имя VG  
  ```console
  [root@localhost vagrant]# mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
  
   *** Creating image file ***
   *** Creating image file done ***
   *** Creating initramfs image file '/boot/initramfs-3.10.0-862.2.3.el7.x86_64.img' done ***
   [root@localhost vagrant]# reboot
   
   [root@localhost vagrant]# vgs
   VG     #PV #LV #SN Attr   VSize   VFree
   centos   1   2   0 wz--n- <38.97g    0 
   
  ```
### 3. Добавить модуль в initrd
---
***- Создадим папку, поместим в нее два скрипта [module-setup.sh](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/module_setup.sh), [test.sh](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/test.sh)
```console

```
