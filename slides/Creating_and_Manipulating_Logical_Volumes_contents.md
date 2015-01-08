## ¿Qué es un volumen lógico (lv)?

<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
        <a class="fancybox" href="img/overview_lvm.png" data-fancybox-group="gallery" title="Pasos para trabajar con volúmenes lógicos">
          <img  src="img/overview_lvm.png" alt="Pasos para trabajar con volúmenes lógicos">
        </a>
    </div>
</div>

note:
* Are the ultimate goal of LVM.
* They’re created and managed in volume groups much like you create and manage files in a filesystem.
* Unlike partitions on a disk, logical volumes are created without reference to device sector numbers, and the LVM subsystem can create logical volumes that span multiple disks or that are discontiguous.
* In LVM, a volume group is divided up into logical volumes. T



## Tipos de lv (I)

* Linear Volumes
* Striped Logical Volumes
* Mirrored Logical Volumes

note:

* There are three types of LVM logical volumes: linear volumes, striped volumes, and mirrored volumes.



## Tipos de lv (II)
### Linear Volumes

<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
        <a class="fancybox" href="img/lvm_linear_lv.png" data-fancybox-group="gallery" title="Linear Volumes">
          <img  src="img/lvm_linear_lv.png" alt="Linear Volumes">
        </a>
    </div>
</div>

note:

* A linear volume aggregates space from one or more physical volumes into one logical volume. For example, if you have two 60GB disks, you can create a 120GB logical volume. The physical storage is concatenated.



## Tipos de lv (III)
### Striped Logical Volumes

<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
        <a class="fancybox" href="img/lvm_striped_lv.png" data-fancybox-group="gallery" title="Striped Logical Volumes">
          <img  src="img/lvm_striped_lv.png" alt="Striped Logical Volumes">
        </a>
    </div>
</div>

note:
* Striping enhances performance by writing data to a predetermined number of physical volumes in round-robin fashion.
* With striping, I/O can be done in parallel.
* In some situations, this can result in near-linear performance gain for each additional physical volume in the stripe.
* The following illustration shows data being striped across three physical volumes.
* In this figure:
 * the first stripe of data is written to PV1
 * the second stripe of data is written to PV2
 * the third stripe of data is written to PV3
 * the fourth stripe of data is written to PV1



## Tipos de lv (IV)
### Mirrored Logical Volumes

<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
        <a class="fancybox" href="img/lvn_mirrored_lv.png" data-fancybox-group="gallery" title="Mirrored Logical Volumes">
          <img  src="img/lvn_mirrored_lv.png" alt="Mirrored Logical Volumes">
        </a>
    </div>
</div>

note:




## Creación un lv (I)

* Para crear un volumen lógico usamos el comando [`lvcreate`](http://linux.die.net/man/8/lvcreate)

<a class="fancybox" href="img/lvcreate.png" data-fancybox-group="gallery" title="Pasos para trabajar con volúmenes lógicos">
<img height="400px" src="img/lvcreate.png" alt="Pasos para trabajar con volúmenes lógicos">
</a>

note:



## Creación un lv (II)

* Basta indicar el tamaño, el nombre y el volumen
```bash
[root@localhost ~]# lvcreate -L 40M -n ejemplo1 ejemplo
  Logical volume "ejemplo1" created
[root@localhost ~]# lvcreate -L 40M -n ejemplo2 ejemplo
  Logical volume "ejemplo1" created
```
* El tamaño no puede ser superior a la suma de los tamaños de los volúmenes físicos que forman el volumen

note:
* Creates a new logical volume in a volume group by allocating logical extents from the free physical extent pool of that volume group.
* If there are not enough free physical extents then the volume group can be extended  with other physical volumes or by reducing existing logical volumes of this volume group in size.
* If you specify one or more PhysicalVolumes, allocation of physical extents will be restricted to these volumes.



## Consultar estado de los lv (I)

* El comando [`lvs`](http://linux.die.net/man/8/lvs) permite ver el estado de los volúmenes
 * `LV` y `VG`: nombre del volumen lógico y nombre del grupo de volúmenes
 * `Attr`: attributos
 * `LSize` : tamaño del volumen
 * `Pool`: conjunto de bloques libres (para LV más grandes que extensiones)

```bash
[root@localhost ~]# lvs
  LV       VG       Attr      LSize  Pool Origin Data%  Move Log Cpy%Sync Convert
  lv_root  VolGroup -wi-ao---  6,51g
  lv_swap  VolGroup -wi-ao---  1,00g
  ejemplo1 ejemplo  -wi-a---- 40,00m
```

note:
* lvs produces formatted output about logical volumes.



## Consultar estado de los lv (II)
### Atributos (I)

<div class="table-responsive">
  <table class="table table-hover table-bordered table-condensed">
    <thead>
      <tr>
        <th>Attributes</th>
        <th>Description</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>`m`</td>

        <td>(m)irrored</td>
      </tr>

      <tr>
        <td>`M`</td>

        <td>(M)irrored without intial sync</td>
      </tr>

      <tr>
        <td>`o`</td>

        <td>(o)rgin</td>
      </tr>

      <tr>
        <td>`p`</td>

        <td>(p)vmove</td>
      </tr>

      <tr>
        <td>`s`</td>

        <td>(s)napshot</td>
      </tr>

      <tr>
        <td>`S`</td>

        <td>invalid (S)napshot</td>
      </tr>
    </tbody>
  </table>
</div>



## Consultar estado de los lv (III)
### Atributos (II)

<div class="table-responsive">
  <table class="table table-hover table-bordered table-condensed">
    <thead>
      <tr>
        <th>Attributes</th>
        <th>Description</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>`v`</td>

        <td>(v)irtual</td>
      </tr>

      <tr>
        <td>`i`</td>

        <td>mirror (i)mage</td>
      </tr>

      <tr>
        <td>`l`</td>

        <td>mirror (I)mage without sync</td>
      </tr>

      <tr>
        <td>`c`</td>

        <td>under (c)onstruction</td>
      </tr>

      <tr>
        <td>`-`</td>

        <td>Simple Volume</td>
      </tr>
    </tbody>
  </table>
</div>



## Consultar estado de los lv (II)

* Podemos obtener una **información más detallada** gracias a [`lvdisplay`](http://linux.die.net/man/8/lvdisplay)

``` bash
[root@localhost ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/ejemplo/ejemplo1
  LV Name                ejemplo1
  VG Name                ejemplo
  LV UUID                Xg3sDc-GnAc-EInJ-iY62-EL1C-m9r0-mQ7CpC
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2013-11-22 20:23:33 +0100
  LV Status              available
  # open                 0
  LV Size                40,00 MiB
  Current LE             10
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:2

  --- Logical volume ---
  LV Path                /dev/VolGroup/lv_root
  LV Name                lv_root
  VG Name                VolGroup
  LV UUID                G309bn-lFVk-mjaq-rta7-0lgg-OKwu-lkfILi
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2013-11-22 17:58:31 +0100
  LV Status              available
  # open                 1
  LV Size                6,51 GiB
  Current LE             1666
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0

  --- Logical volume ---
  LV Path                /dev/VolGroup/lv_swap
  LV Name                lv_swap
  VG Name                VolGroup
  LV UUID                GBuN5D-DtCC-Z3iG-1naZ-x1JJ-u4Vp-uZ8nCZ
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2013-11-22 17:58:32 +0100
  LV Status              available
  # open                 1
  LV Size                1,00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```

note:
* lvdisplay allows you to see the attributes of a logical volume like size, read/write status, snapshot information etc.



## Creación del SF y montaje (I)

<div class="row">
    <div class="col-xs-8 col-sm-8 col-md-8 col-lg-8 col-centered">
        <a class="fancybox" href="img/devmapper.png" data-fancybox-group="gallery" title="/dev/mapper ">
<img height="550px" src="img/devmapper.png" alt="/dev/mapper ">
</a>
    </div>
</div>

note:

* Los volúmenes lógicos son accesibles mediante ficheros de dispositivo localizados en:
 * /dev/mapper
 * /dev



## Creación del SF y montaje (II)

``` bash
[root@localhost]# ls /dev/mapper
ejemplo-ejemplo1  ejemplo-ejemplo2

[root@localhost dev]# ls /dev/ejemplo/
ejemplo1  ejemplo2
```

note:



## Creación del SF y montaje (III)

<a class="fancybox" href="img/mkfs.png" data-fancybox-group="gallery" title="/dev/mapper ">
<img height="450px" src="img/mkfs.png" alt="/dev/mapper ">
</a>

note:



## Creación del SF y montaje (IV)

* Creamos el sistema de ficheros en cada volumen

``` bash
[root@localhost dev]# mkfs.ext3 /dev/ejemplo/ejemplo1
mke2fs 1.41.12 (17-May-2010)
Etiqueta del sistema de ficheros=
Tipo de SO: Linux
Tamaño del bloque=1024 (bitácora=0)
Tamaño del fragmento=1024 (bitácora=0)
Stride=0 blocks, Stripe width=0 blocks
10240 nodos-i, 40960 bloques
2048 bloques (5.00%) reservados para el superusuario
Primer bloque de datos=1
Número máximo de bloques del sistema de ficheros=41943040
5 bloque de grupos
8192 bloques por grupo, 8192 fragmentos por grupo
2048 nodos-i por grupo
Respaldo del superbloque guardado en los bloques:
  8193, 24577

Escribiendo las tablas de nodos-i: hecho
Creating journal (4096 blocks): hecho
Escribiendo superbloques y la información contable del sistema de ficheros: hecho

Este sistema de ficheros se revisará automáticamente cada 38 montajes o
180 días, lo que suceda primero.  Utilice tune2fs -c o -i para cambiarlo.
[root@localhost dev]# mkfs.ext3 /dev/ejemplo/ejemplo2
mke2fs 1.41.12 (17-May-2010)
Etiqueta del sistema de ficheros=
Tipo de SO: Linux
Tamaño del bloque=1024 (bitácora=0)
Tamaño del fragmento=1024 (bitácora=0)
Stride=0 blocks, Stripe width=0 blocks
10240 nodos-i, 40960 bloques
2048 bloques (5.00%) reservados para el superusuario
Primer bloque de datos=1
Número máximo de bloques del sistema de ficheros=41943040
5 bloque de grupos
8192 bloques por grupo, 8192 fragmentos por grupo
2048 nodos-i por grupo
Respaldo del superbloque guardado en los bloques:
  8193, 24577

Escribiendo las tablas de nodos-i: hecho
Creating journal (4096 blocks): hecho
Escribiendo superbloques y la información contable del sistema de ficheros: hecho

Este sistema de ficheros se revisará automáticamente cada 29 montajes o
180 días, lo que suceda primero.  Utilice tune2fs -c o -i para cambiarlo.
```
note:



## Creación del SF y montaje (V)

```bash
[root@localhost dev]# mount  /dev/ejemplo/ejemplo2 /mnt/
[root@localhost dev]# mount
/dev/mapper/VolGroup-lv_root on / type ext4 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
/dev/sda1 on /boot type ext4 (rw)
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
/dev/mapper/ejemplo-ejemplo2 on /mnt type ext3 (rw)
```
note:
* Once a SF were installed in a  Logical Volumen, we can mount it as usual



## Aumentar el tamaño de un lv (I)

* Usamos [`lvextend`](http://linux.die.net/man/8/lvextend) para aumentar el tamaño del volumen lógico.

<a class="fancybox" href="img/lvextend.png" data-fancybox-group="gallery" title="lvextend">
<img height="200px" src="img/lvextend.png" alt="vgextend">
</a>

note:



## Aumentar el tamaño de un lv (II)

* Comprobamos el tamaño de los SF montados usando [`df`](http://linux.die.net/man/1/df)
```bash
~$ df
S.ficheros         Bloques de 1K   Usado    Dispon Uso% Montado en
/dev/mapper/VolGroup-lv_root
                       6716804    668868   5706740  11% /
tmpfs                   255492         0    255492   0% /dev/shm
/dev/sda1               495844     30193    440051   7% /boot
/dev/mapper/ejemplo-ejemplo2
                         39663      4604     33011  13% /mnt
```
* Incremento en 20 Mb el tamaño del volumen usando [`lvextend`](http://linux.die.net/man/8/lvextend)
```bash
~$ lvextend -L +20M /dev/ejemplo/ejemplo2
  Extending logical volume ejemplo2 to 60,00 MiB
  Logical volume ejemplo2 successfully resized
```
note:



## Aumentar el tamaño de un lv (III)

* Uso [`lvs`](http://linux.die.net/man/8/lvs)  para comprobar el aumento en el volumen
``` bash
~$ lvs
LV       VG       Attr      LSize  Pool Origin Data%  Move Log Cpy%Sync Convert
ejemplo1 ejemplo  -wi-a---- 40,00m
ejemplo2 ejemplo  -wi-ao--- 60,00m
```
* Confirmo aumento en el SF usando [`df`](http://linux.die.net/man/1/df)
```bash
~$ df
S.ficheros         Bloques de 1K   Usado    Dispon Uso% Montado en
/dev/mapper/VolGroup-lv_root
                       6716804    668868   5706740  11% /
tmpfs                   255492         0    255492   0% /dev/shm
/dev/sda1               495844     30193    440051   7% /boot
/dev/mapper/ejemplo-ejemplo2
                         39663      4604     33011  13% /mnt
```
* **¿Ha aumentado el tamaño?**

note:
* vextend allows you to extend the size of a logical volume.
* Extension of snapshot logical volumes  is supported as well.



## Aumentar el tamaño de un lv (IV)

<div class="alert alert-danger">
  <strong>¡Atención!</strong> `lvextend` aumenta el tamaño del volumen pero no del SF
</div>

<a class="fancybox" href="img/problema_lvextend.png" data-fancybox-group="gallery" title="vgextend">
<img height="350px" src="img/problema_lvextend.png" alt="vgextend">
</a>



## Aumentar el tamaño de un lv (V)

* Para aumentar el tamaño del sistema de ficheros al tamaño usamos [`resizefs`](http://linux.die.net/man/8/resize2fs)

``` bash
[root@localhost dev]# resize2fs /dev/ejemplo/ejemplo2
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/ejemplo/ejemplo2 is mounted on /mnt; on-line resizing required
old desc_blocks = 1, new_desc_blocks = 1
Performing an on-line resize of /dev/ejemplo/ejemplo2 to 61440 (1k) blocks.
El sistema de ficheros en /dev/ejemplo/ejemplo2 tiene ahora 61440 bloques.

[root@localhost dev]# df
S.ficheros         Bloques de 1K   Usado    Dispon Uso% Montado en
/dev/mapper/VolGroup-lv_root
                       6716804    668864   5706744  11% /
tmpfs                   255492         0    255492   0% /dev/shm
/dev/sda1               495844     30193    440051   7% /boot
/dev/mapper/ejemplo-ejemplo2
```

note:



## Renombrando un lv (I)

* El comando [`lvrename`](http://linux.die.net/man/8/lvrename) renombra un volumen lógico

<a class="fancybox" href="img/lvrename.png" data-fancybox-group="gallery" title="lvrename">
<img height="200px" src="img/lvrename.png" alt="lvrename">
</a>

note:



## Renombrando un lv (II)

``` bash
[root@localhost dev]# lvrename ejemplo ejemplo1 Nuevoejemplo
  Renamed "ejemplo1" to "Nuevoejemplo" in volume group "ejemplo"
[root@localhost dev]# lvs
  LV           VG       Attr      LSize  Pool Origin Data%  Move Log Cpy%Sync Convert
  lv_root      VolGroup -wi-ao---  6,51g
  lv_swap      VolGroup -wi-ao---  1,00g
  Nuevoejemplo ejemplo  -wi-a---- 40,00m
  ejemplo2     ejemplo  -wi-ao--- 60,00m
[root@localhost dev]#
```

note:
* lvrename renames an existing logical volume from OldLogicalVolume{Name|Path} to NewLogicalVolume{Name|Path}.



## Borrar un lv (I)

* Borramos un volumen lógico usando [`lvremove`](http://linux.die.net/man/8/lvremove)

<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
      <a class="fancybox" href="img/lvremove.png" data-fancybox-group="gallery" title="lvrename">
      <img height="350px" src="img/lvremove.png" alt="lvrename">
      </a>
    </div>
</div>

<div class="alert alert-info">
  <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
  <strong>¡Atención!</strong> No puede estar en uso
</div>

note:
* Removes one or more logical volumes.
* Confirmation will be requested before deactivating any active logical volume prior to removal.
* Logical volumes cannot be deactivated or removed while they are open (e.g. if they contain a mounted filesystem).
* Removing an origin logical volume will also remove all dependent snapshots.
* If the logical volume is clustered then it must be deactivated on all nodes in the cluster before it can be removed.
* A single lvchange command issued from one node can do this.



## Borrar un lv (II)

* Borro `/dev/ejemplo/Nuevoejemplo` usando [`lvremove`](http://linux.die.net/man/8/lvremove)
``` bash
[root@localhost dev]# lvremove /dev/ejemplo/Nuevoejemplo
Do you really want to remove active logical volume Nuevoejemplo? [y/n]: y
  Logical volume "Nuevoejemplo" successfully removed
```
* Compruebo que se ha borrado usando [`lvs`](http://linux.die.net/man/8/lvs)
```bash
[root@localhost dev]# lvs
  LV       VG       Attr      LSize  Pool Origin Data%  Move Log Cpy%Sync Convert
  lv_root  VolGroup -wi-ao---  6,51g
  lv_swap  VolGroup -wi-ao---  1,00g
  ejemplo2 ejemplo  -wi-ao--- 60,00m
```
note:



## Resumen comandos (I)

<div class="table-responsive">
  <table class="table table-hover table-condensed table-bordered">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Descripción</th>
      </tr>
    </thead>
    <tbody>
<tr>
<td><a href="http://linux.die.net/man/8/lvchange">lvchange</a></td>
<td>Cambia atributos de un volumen lógico</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvconvert">lvconvert</a></td>
<td>Convierte el estado entre lineal, espejo y snapshot</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvcreate">lvcreate</a></td>
<td>Crea un volumen</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvdisplay">lvdisplay</a></td>
<td>Muestra información detallada</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvextend">lvextend</a></td>
<td>Aumenta el tamaño de un LV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvreduce">lvreduce</a></td>
<td>Reduce el tamaño de un LV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvremove">lvremove</a></td>
<td>Borra un LV</td>
</tr>
</tbody>
  </table>
</div>

note:
| Comando   | Descripción |
|-----------|-------------|
| lvchange  | Changes attributes of a logical volume, such as whether it must be allocated to contiguous sectors and whether it can be written. |
| lvconvert | Converts between linear, mirror, and snapshot status.
| lvcreate  | Creates a logical volume.
| lvdisplay | Displays verbose information about logical volumes.
| lvextend  | Expands the size of a logical volume. (This does not expand the contained filesystem, though.)
| lvreduce  | Shrinks a logical volume. (This does not shrink the contained filesystem, though.)
| lvremove  | Deletes a logical volume.
| lvrename  | Renames a logical volume.
| lvresize  | Resizes a logical volume; does the jobs of both lvextend and lvreduce . (This does not shrink the contained filesystem,though.)
| lvs       | Displays a terse summary of logical volume information
| lvscan    | Scans all disks for logical volumes.



## Resumen comandos (I)

<div class="table-responsive">
  <table class="table table-hover table-condensed table-bordered">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Descripción</th>
      </tr>
    </thead>
    <tbody>
<tr>
<td><a href="http://linux.die.net/man/8/lvrename">lvrename</a></td>
<td>Cambia el nombre de un LV </td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvresize">lvresize</a></td>
<td>Cambia el tamaño de un LV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvs">lvs</a></td>
<td>Muestra información resumida de LV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/lvscan">lvscan</a></td>
<td>Busca LV</td>
</tr>
</tbody>
  </table>
</div>

note:
| Comando   | Descripción |
|-----------|-------------|
| lvchange  | Changes attributes of a logical volume, such as whether it must be allocated to contiguous sectors and whether it can be written. |
| lvconvert | Converts between linear, mirror, and snapshot status.
| lvcreate  | Creates a logical volume.
| lvdisplay | Displays verbose information about logical volumes.
| lvextend  | Expands the size of a logical volume. (This does not expand the contained filesystem, though.)
| lvreduce  | Shrinks a logical volume. (This does not shrink the contained filesystem, though.)
| lvremove  | Deletes a logical volume.
| lvrename  | Renames a logical volume.
| lvresize  | Resizes a logical volume; does the jobs of both lvextend and lvreduce . (This does not shrink the contained filesystem,though.)
| lvs       | Displays a terse summary of logical volume information
| lvscan    | Scans all disks for logical volumes.
