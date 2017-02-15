LVM2
---

Действия далее приведены для системы `uname -a`:
```
Linux oyccomp 3.16.0-4-amd64 #1 SMP Debian 3.16.36-1+deb8u2 (2016-10-19) x86_64 GNU/Linux
```

Установка:
```
# apt-get install lvm2
$ dpkg -l | grep lvm
```

иногда (если physical volume делается из raid) нужно сделать так:
```
$ dd if=/dev/zero of-/dev/md0 bs=1M count=32 
```

создаём physical volume

```
# pvcreate /dev/sdc
# pvcreate /dev/sdd
# pvcreate /dev/sde
# pvcreate /dev/sdf
# pvcreate /dev/sdg
# pvcreate /dev/md0
```

создаём группу томов fs (Volume Group)
```
# vgcreate fs /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg /dev/md0
```

Смотрим что получилось
```
# vgdisplay
```

Запоминаем цифру в Total PE - будем её использовать чтобы создать логический том на всю группу.
```
# lvcreate -n vl1 -l 2861559 fs
```

Смотрим, что натворили
```
# lvscan
```

Если не нравится, то удаляем
```
# lvremove fs
```

И повторяем  `# lvcreate …`

Создаём файловую систему:
```
# mkfs.ext4 /dev/fs/vl1
```

Папка куда монтировать
```
# mkdir -p /media/vl1
```

Монтируем
```
# mount /dev/fs/vl1 /media/vl1
```

здесь проверяем работоспособность и если все пучком, 
то закрепляем полученный результат  системе не постоянной основе.
Вычисляем UUID нашего раздела (d84895b8-93f6-4b40-ab9e-b6f179a13ef5)
```
$ blkid  /dev/fs/vl1
```

Добавим новый том в */etc/fstab*
```
# echo "UUID=d84895b8-93f6-4b40-ab9e-b6f179a13ef5 /media/vl1      ext4    auto,rw,noexec,noatime        0       0" >> /etc/fstab
```

отмонтируем
```
# umount /media/vl1
```

меняем размер логических томов
```
# lvreduce --size 120G /dev/fs/tmp
# lvextend --size 120G /dev/fs/tmp
```

можно использовать **lvresize**

монтируем всё на основе */etc/fstab*
```
# mount -a
```
