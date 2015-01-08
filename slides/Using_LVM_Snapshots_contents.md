## ¿Qué son Snapshots?

<div class="row">
    <div class="col-xs-9 col-sm-9 col-md-9 col-lg-9 col-centered">
<a class="fancybox" href="img/lvm-snapshot.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/lvm-snapshot.png" alt="Volumenes físicos">
</a>
      </a>
    </div>
</div>

note:
* LVM provides a useful feature known as a snapshot. 
* A snapshot is a logical volume that preserves the state of another logical volume, enabling you to make changes to the original while retaining the original state of the volume. 
* Snapshots are created very quickly, so you can use them to back up a disk at one moment in time, or you can use a snapshot as a quick “out” in case a major system change doesn’t work out.



## Creación de Snapshot (I)

* El comando usado para crearlos es `lvcreate` con la opción `-s (--snapshot)`:
<a class="fancybox" href="img/lvcreate-s.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="130px" src="img/lvcreate-s.png" alt="Volumenes físicos">
</a>
*  El tamaño de la instantánea puede ser menor que la del volumen fuente

note:
* To create a snapshot, use the lvcreate command with its -s (--snapshot) option:
# lvcreate -L 10G -s -n snappy /dev/speaker/PCLOS
* This example creates a new logical volume, snappy, that duplicates the current contents of /dev/speaker/PCLOS.
* The snapshot’s size (10 GiB in this example) can be substantially smaller than the source volume’s size. 



## Creación de Snapshot (II)

* Creamos una captura para `/dev/ejemplo/ejemplo2` usando `lvcreate`
``` bash
[root@localhost /]# lvcreate -L 32M -s -n captura /dev/ejemplo/ejemplo2 
  Logical volume "captura" created
```
* Usamos el comando `lvs` para comprobar el estado
```bash
[root@localhost /]# lvs
  LV       VG       Attr      LSize  Pool Origin   Data%  Move Log Cpy%Sync Convert
  captura  ejemplo  swi-a-s-- 32,00m      ejemplo2   0,00                          
  ejemplo2 ejemplo  owi-a-s-- 60,00m 
```

note:



## Creación de Snapshot (III)

* Con `lvdisplay` podemos ver la fecha de la creación de la captura

```bash
# lvdisplay /dev/ejemplo/ejemplo2 /dev/ejemplo/captura
  --- Logical volume ---
  LV Name                /dev/ejemplo/ejemplo2
  VG Name                ejemplo
  LV UUID                Xg3sDc-GnAc-EInJ-iY62-EL1C-m9r0-mQ7CpC
  LV Write Access        read/write
  LV snapshot status     source of
                         captura [active]
  LV Status              available
  # open                 0
  LV Size                68,00 MiB
  Current LE             15
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:2  
  
   --- Logical volume ---
  LV Path                /dev/ejemplo/captura
  LV Name                captura
  VG Name                ejemplo
  LV UUID                MkUcBm-BpHw-eGmU-1YiY-ilz3-uyld-fSKPnj
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2013-11-25 12:59:49 +0100
  LV snapshot status     active destination for ejemplo2
  LV Status              available
  # open                 0
  LV Size                60,00 MiB
  Current LE             15
  COW-table size         32,00 MiB
  COW-table LE           8
  Allocated to snapshot  0,00%
  Snapshot chunk size    4,00 KiB
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:3
```



## Copia de seguridad (I)

* Los snapshot se pueden usar para hacer copias

<div class="row">
    <div class="col-xs-9 col-sm-9 col-md-9 col-lg-9 col-centered">
<a class="fancybox" href="img/lvm_ejemplo_backup1.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/lvm_ejemplo_backup1.png" alt="Volumenes físicos">
</a>
    </div>
</div>



## Copia de seguridad (II)

* Montamos el volumen origen `/dev/ejemplo/ejemplo2`
``` bash
[root@localhost mnt]# mount /dev/ejemplo/ejemplo2
``` 
* Creamos varios ficheros
```
[root@localhost mnt]# echo "Antes de $(date)" > /mnt/salida1
[root@localhost mnt]# echo "Antes de $(date)" > /mnt/salida2
[root@localhost mnt]# echo "Antes de $(date)" > /mnt salida3
[root@localhost mnt]# ls -l /mnt/
total 18
drwx------. 2 root root 12288 nov 25 13:06 lost+found
-rw-r--r--. 1 root root    38 nov 25 13:37 salida1
-rw-r--r--. 1 root root    38 nov 25 13:37 salida2
-rw-r--r--. 1 root root    38 nov 25 13:37 salida3
[root@localhost mnt]# cat *
cat: lost+found: Es un directorio
Antes de lun nov 25 13:37:27 CET 2013
Antes de lun nov 25 13:37:30 CET 2013
Antes de lun nov 25 13:37:32 CET 2013
```



## Copia de seguridad (III)

* Realizamos la instantánea
```bash
lvcreate -L 32M -s -n captura /dev/ejemplo/ejemplo2 
  Logical volume "captura" created
```
* Comprobamos la fecha de creación
```bash
[root@localhost mnt]# lvdisplay /dev/ejemplo/captura 
  --- Logical volume ---
  LV Path                /dev/ejemplo/captura
  LV Name                captura
  VG Name                ejemplo
  LV UUID                SCEsMv-X616-zaZm-MqNX-yJ1r-UPJ0-15pbXl
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2013-11-25 13:47:13 +0100
  LV snapshot status     active destination for ejemplo2
  LV Status              available
  # open                 0
  LV Size                60,00 MiB
  Current LE             15
  COW-table size         32,00 MiB
  COW-table LE           8
  Allocated to snapshot  0,04%
  Snapshot chunk size    4,00 KiB
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:3
```



## Copia de seguridad (IV)

* Montamos la captura
```bash
[root@localhost mnt]#  mount /dev/ejemplo/captura /media/
```
* Comprobamos sus contenidos
```
[root@localhost mnt]# ls -l /media/
total 18
drwx------. 2 root root 12288 nov 25 13:06 lost+found
-rw-r--r--. 1 root root    38 nov 25 13:37 salida1
-rw-r--r--. 1 root root    38 nov 25 13:37 salida2
-rw-r--r--. 1 root root    38 nov 25 13:37 salida3
[root@localhost mnt]# cat /media/salida*
Antes de lun nov 25 13:37:27 CET 2013
Antes de lun nov 25 13:37:30 CET 2013
Antes de lun nov 25 13:37:32 CET 2013
```



## Copia de seguridad (V)

<div class="row">
    <div class="col-xs-9 col-sm-9 col-md-9 col-lg-9 col-centered">
<a class="fancybox" href="img/lvm_ejemplo_backup2.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/lvm_ejemplo_backup2.png" alt="Volumenes físicos">
</a>
    </div>
</div>



## Copia de seguridad (VI)

* Añadimos nuevos ficheros en `/dev/ejemplo/ejemplo2`
```bash 
[root@localhost mnt]# echo "Despues de $(date)" > /mnt/salida4
[root@localhost mnt]# echo "Despues de $(date)" > /mnt/salida5
``` 
* Comprobamos  `/dev/ejemplo/ejemplo2`
``` 
[root@localhost mnt]# ls -l /mnt/*
-rw-r--r--. 1 root root    38 nov 25 13:37 /mnt/salida1
-rw-r--r--. 1 root root    38 nov 25 13:37 /mnt/salida2
-rw-r--r--. 1 root root    38 nov 25 13:37 /mnt/salida3
-rw-r--r--. 1 root root    40 nov 25 13:51 /mnt/salida4
-rw-r--r--. 1 root root    40 nov 25 13:51 /mnt/salida5
```
* Comprobamos que no está en la copia
```
[root@localhost mnt]# ls -l /media/
total 18
drwx------. 2 root root 12288 nov 25 13:06 lost+found
-rw-r--r--. 1 root root    38 nov 25 13:37 salida1
-rw-r--r--. 1 root root    38 nov 25 13:37 salida2
-rw-r--r--. 1 root root    38 nov 25 13:37 salida3
```



## Copia de seguridad (VII)

* Modificamos ficheros contenidos `/dev/ejemplo/ejemplo2`
```bash
[root@localhost mnt]# echo "Despues de $(date)" >> /mnt/salida1 
[root@localhost mnt]# cat /mnt/salida
```
* Comprobamos su contenido en la copia
```bash
[root@localhost mnt]# cat /media/salida1
Antes de lun nov 25 13:37:27 CET 2013
```



## Copia de seguridad (VIII)

* Podemos hacer un backup partiendo de `captura`

<a class="fancybox" href="img/lvm_ejemplo_backup3.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="300px" src="img/lvm_ejemplo_backup3.png" alt="Volumenes físicos">
</a>



## Restauración (I)

<div class="row">
    <div class="col-xs-9 col-sm-9 col-md-9 col-lg-9 col-centered">
<a class="fancybox" href="img/lvm_restauracion_ejemplo.png" data-fancybox-group="gallery" title="Volumenes físicos">
<img height="550px" src="img/lvm_restauracion_ejemplo.png" alt="Volumenes físicos">
</a>
    </div>
</div>

note:
* Another use of snapshots is to provide a way to revert changes made to the original filesystem.
* If the changes you’ve made don’t work out, you will then use the snapshot to restore the original filesystem. 



## Restauración (II)

* Para llevar un volumen a un estado anterior usamos una captura
* Para llevar nuestro ejemplo a la situación anterior ejecutamos
```bash
[root@localhost ~]# lvconvert --merge /dev/ejemplo/captura 
  Merging of volume captura started.
  ejemplo2: Merged: 99,9%
  ejemplo2: Merged: 100,0%
  Merge of snapshot into logical volume ejemplo2 has finished.
  Logical volume "captura" successfully removed
```
* Los dos volúmenes deberían estar desmontamos

note:
* If the original filesystem isn’t critical to normal system functioning, you can do this from a normal system boot; however, this type of operation often involves the main system, which
can’t be unmounted. You can still perform the merge operation, but it will be deferred until you unmount the
mounted filesystem, which normally means until you reboot the computer. The merge uses the --merge option to
lvconvert:
# lvconvert --merge /dev/speaker/snappy
Once this operation completes, the original state of the original logical volume will be restored. The merge will
also automatically delete the snapshot volume, so if you want to attempt again whatever operation prompted
these actions, you’ll have to re-create the snapshot volume.



## Restauración (III)

``` bash
[root@localhost ~]# mount /dev/ejemplo/ejemplo2 /mnt/
[root@localhost ~]# ls /mnt/
lost+found  salida1  salida2  salida3
[root@localhost ~]# ls -l /mnt/
total 18
drwx------. 2 root root 12288 nov 25 13:06 lost+found
-rw-r--r--. 1 root root    38 nov 25 13:37 salida1
-rw-r--r--. 1 root root    38 nov 25 13:37 salida2
-rw-r--r--. 1 root root    38 nov 25 13:37 salida3
```
