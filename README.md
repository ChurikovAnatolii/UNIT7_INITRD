### 1. Попасть в систему без пароля несколькими способами
___

***-- Вариант 1 (с rd.break). 
Запускаем виртуальную машину через [Vagrantfile](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/Vagrantfile), жмем 'e', чтобы отредактировать параметры загрузки, добавляем параметр rd.break в строчку с linux16, тем самым прерываем монтирование рутовой файловой системы, она остается смонтированой в /sysroot***

> Перемонтируем sysroot в RW, применяем chroot, изменяем пароль

```console
mount -o, remount rw /sysroot
chroot sysroot
cd root
passwd
touch /.autorelabel
```

***-- Вариант 2 (с rw init=/sysroot/bin/sh).  
Вариант аналогичен предыдущему так же редактируем параметры загрузки, вставляем  rw init=/sysroot/bin/sh в строчку с linux16,
загружаемся и меняем пароль root, отличие от предыдущего способа в том, что система сразу примонтирована в rw режиме.***

***-- Вариант 3 (с systemd.unit=emergency.target).  
в этом варианте меняем параметры загрузчика, прописываем systemd.unit=emergency.target в строку с linux16 и сразу псоле загрузки можем поменять пароль root.***




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
***- Создадим папку, поместим в нее два скрипта [module-setup.sh](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/module_setup.sh), [test.sh](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/test.sh)***

```console
[root@localhost vagrant]# mkdir /usr/lib/dracut/modules.d/01test
[root@localhost vagrant]# cd /usr/lib/dracut/modules.d/01test/
[root@localhost 01test]# nano module-setup.sh
[root@localhost 01test]# nano test.sh

```
***- Пересоберем initrd, посмотрим какие модули загружены***

```console
[root@localhost 01test]# dracut -f -v

*** Creating image file ***
*** Creating image file done ***
*** Creating initramfs image file '/boot/initramfs-3.10.0-862.2.3.el7.x86_64.img' done ***

[root@localhost 01test]# lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
test
```

***- Уберем из [grub.cfg](https://github.com/ChurikovAnatolii/UNIT7_INITRD/blob/main/grub.cfg) опции rghb и quiet, чтобы увидеть вывод нашего модуля при загрузке***



