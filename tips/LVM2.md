LVM2
---

```
sudo apt-get install lvm2
dpkg -l | grep lvm
```

иногда нужно сделать так:
```
(dd if=/dev/zero of-/dev/md0 bs=1M count=32) если physical volume делается из raid
```

# создаём physical volume

```
sudo pvcreate /dev/sdc
sudo pvcreate /dev/sdd
sudo pvcreate /dev/sde
sudo pvcreate /dev/sdf
sudo pvcreate /dev/sdg
sudo pvcreate /dev/md0
```

# создаём группу томов fs (Volume Group)
```
sudo vgcreate fs /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg /dev/md0

# Смотрим что получилось
```
sudo  vgdisplay
```

# Запоминаем цифру в Total PE - будем её использовать чтобы создать логический том на всю группу.
```
sudo lvcreate -n vl1 -l 2861559 fs
```

# Смотрим, что натворили
```
sudo lvscan
```

# Если не нравится, то удаляем
```
sudo lvremove fs
```
# И повторяем  sudo lvcreate …

# Создаём файловую систему:
```
sudo mkfs.ext4 /dev/fs/vl1
```

# Папка куда монтировать
```
sudo mkdir -p /media/vl1
```

# Монтируем
```
sudo mount /dev/fs/vl1 /media/vl1
```

# здесь проверяем работоспособность и если все пучком, 
# то закрепляем полученный результат  системе не постоянной основе.
# Вычисляем UUID нашего раздела (d84895b8-93f6-4b40-ab9e-b6f179a13ef5)
```
blkid  /dev/fs/vl1
```

# Добавим новый том в fstab
```
echo "UUID=d84895b8-93f6-4b40-ab9e-b6f179a13ef5 /media/vl1      ext4    auto,rw,noexec,noatime        0       0" >> /etc/fstab
```

# отмонтируем
```
sudo umount /media/vl1
```

# меняем размер логических томов
```
lvreduce --size 120G /dev/fs/tmp
lvextend --size 120G /dev/fs/tmp
```

# можно использовать lvresize


# монтируем всё на основе fstab
```
mount -a
```
