### 1. Попасть в систему без пароля несколькими способами
___

***-- Запускаем виртуальную машину через [Vagrantfile](), жмем 'e', чтобы отредактировать параметры загрузки, добавляем параметр rd.break в строчку с linux16***
***Тем самым прерываем монтирование рутовой файловой системы, она остается смонтированой в /sysroot***
> 

### 2. Установить систему с LVM, после чего переименовать VG

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
  
  > Изменим название VG в  [/etc/fstab](), [/etc/default/grub](), [/boot/grub2/grub.cfg]() 
