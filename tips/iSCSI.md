# TARGET (server)

Установка:
```
# apt-get install iscsitarget
```

##### /etc/default/iscsitarget:
```
….
ISCSITARGET_ENABLE=true
….
```

##### /etc/iet/ietd.conf:
```
 Target iqn.2017-01.com.attava:target00
    Lun 0 Path=/home/serg/iscsi_disks/lun1.img,Type=fileio
    initiator-address 10.1.2.180
    incominguser serg jgf23
```

Lun’ы нумеруются с нуля. Нулевой лун должен присутствовать.
```
# systemctl restart iscsitarget.service
```
---

# INITIATOR (client)

Установка:
```
# apt-get install open-iscsi
```

##### /etc/iscsi/iscsid.conf:
```
...
node.startup = automatic
...
node.session.auth.username = serg
node.session.auth.password = jgf23
...
discovery.sendtargets.auth.username = serg
discovery.sendtargets.auth.password = jgf23
```

перезапуск сервиса:
```
# systemctl restart open-iscsi
```

поиск доступных ресурсов:
```
# iscsiadm -m discovery -t st -p 10.1.2.69
```

login (подключение к ресурсу, создание блочных устройств)
```
# iscsiadm -m node -l -T iqn.2017-01.com.attava:target00
```

logout (отключение и удаление устройств из /dev/)
```
# iscsiadm -m node -u -T iqn.2017-01.com.attava:target00
```
