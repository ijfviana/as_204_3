## ¿Qué son los volúmenes físicos (pv)?

<div class="row">
    <div class="col-xs-8 col-sm-8 col-md-8 col-lg-8 col-centered">
        <a class="fancybox" href="img/lvm_overview_1.png" data-fancybox-group="gallery" title="Volumenes físicos">
          <img src="img/lvm_overview_1.png" alt="Volumenes físicos">
      </a>
    </div>
</div>

note:
* In most cases, physical volumes are conventional partitions;
* however, LVM can be built atop entire disk devices if desired.
* Using partitions as physical volumes enables you to use partitions when they have advantages:
 * GRUB Legacy can’t read logical volumes, so you may want to put the Linux /boot directory on a partition and use another partition as an LVM physical volume.



## Creación de un pv (I)

<div class="row">
    <div class="col-xs-11 col-sm-11 col-md-11 col-lg-11 col-centered">
        <a class="fancybox" href="img/lvm-pv-steps.png" data-fancybox-group="gallery" title="Volumenes físicos">
          <img height="550px" src="img/lvm-pv-steps.png" alt="Volumenes físicos">
      </a>
    </div>
</div>

note:
If you want to use LVM, the first step is to prepare physical volumes. There are actually two substeps to this step.



## Creación de un pv (III)
### Disposición de un PV

* `LVM Label`: UUID + orden del dispositivo + número bloques + contiene metadata
* `Metadata`: detalles de configuración de los VG de nuestro sistema. Esta información es copia exacta en todos los PV.

<div class="row">
    <div class="col-xs-11 col-sm-11 col-md-11 col-lg-11 col-centered">
        <a class="fancybox" href="img/lvn_pv_layout.png" data-fancybox-group="gallery" title="PV layout">
          <img  src="img/lvn_pv_layout.png" alt="PV layout">
        </a>
    </div>
</div>

note:

* Initializing a block device as a physical volume places a label near the start of the device.
* An LVM label
 * Provides persistent and correct identification (UUID) and device ordering for a physical device.
 * Stores the size of the block device in bytes
 * Records where the LVM metadata will be stored on the device.
* The LVM metadata contains the configuration details of the LVM volume groups on your system (an identical copy of the metadata is maintained in every metadata area in every physical volume)



## Creación de un pv (IV)

<div class="row">
    <div class="col-xs-5 col-sm-5 col-md-5 col-lg-5 col-centered">
        <a class="fancybox" href="img/lvm_ejemplo_tipo_partciones.png" data-fancybox-group="gallery" title="Partición de tipo lvm">
          <img  src="img/lvm_ejemplo_tipo_partciones.png" alt="Partición de tipo lvm">
        </a>
    </div>
</div>

<div class="alert alert-info">
  <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
  <strong>¡Atención!</strong> Habitualmente se crea un único PV por disco
</div>

note:
* The first substep is to flag your physical volumes as being for LVM use.
* This is necessary if you use partitions as physical volumes, but not if you use whole unpartitioned disks or RAID devices.)
* The MBR type code for LVM partitions is 0x8E, so if you use fdisk, be sure to enter that as the type code for your physical volumes.
* If you use GPT disks and manipulate them with gdisk, use a type code of 0x8E00. When you use libparted-based tools with either MBR or GPT disks, set the lvm flag.
* It is generally recommended that you create a single partition that covers the whole disk to label as an LVM physical volume for the following reasons:
 * Administrative convenience: It is easier to keep track of the hardware in a system if each real disk only appears once.
 * Striping performance: LVM cannot tell that two physical volumes are on the same physical disk. If you create a striped logical volume when two physical volumes are on the same physical disk, the stripes could be on different partitions on the same disk.



## Creación de un pv (V)

* Usamos cualquier aplicación que permita particionado como [`fdisk`](http://linux.die.net/man/8/fdisk) para crear las particiones

```bash
[root@localhost ~]# fdisk /dev/sdb
El dispositivo no contiene una tabla de particiones DOS válida ni una etiqueta de disco Sun o SGI o OSF
Building a new DOS disklabel with disk identifier 0x5255635c.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Atención: el indicador 0x0000 inválido de la tabla de particiones 4 se corregirá mediante w(rite)

WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Orden (m para obtener ayuda): n
Acción de la orden
e   Partición extendida
   p   Partición primaria (1-4)
p
Número de partición (1-4): 1
Primer cilindro (1-1044, valor predeterminado 1): 1
Last cilindro, +cilindros or +size{K,M,G} (1-1044, valor predeterminado 1044): +256M

Orden (m para obtener ayuda): n
Acción de la orden
e   Partición extendida
   p   Partición primaria (1-4)
p
Número de partición (1-4): 2
Primer cilindro (35-1044, valor predeterminado 35):
Se está utilizando el valor predeterminado 35
Last cilindro, +cilindros or +size{K,M,G} (35-1044, valor predeterminado 1044): +256M

Orden (m para obtener ayuda): n
Acción de la orden
e   Partición extendida
   p   Partición primaria (1-4)
p
Número de partición (1-4): 3
Primer cilindro (69-1044, valor predeterminado 69):
Se está utilizando el valor predeterminado 69
Last cilindro, +cilindros or +size{K,M,G} (69-1044, valor predeterminado 1044): +256M

Orden (m para obtener ayuda):
```



## Creación de un pv (VI)

* Con [`fdisk`](http://linux.die.net/man/8/fdisk) marcamos las particiones como de tipo `Linux LVM (8e)`

``` bash
Orden (m para obtener ayuda): t
Número de partición (1-4): 1
Código hexadecimal (escriba L para ver los códigos): 8e
Se ha cambiado el tipo de sistema de la partición 1 por 8e (Linux LVM)

Orden (m para obtener ayuda): t
Número de partición (1-4): 2
Código hexadecimal (escriba L para ver los códigos): 8e
Se ha cambiado el tipo de sistema de la partición 2 por 8e (Linux LVM)

Orden (m para obtener ayuda): t
Número de partición (1-4): 3
Código hexadecimal (escriba L para ver los códigos): 8e
Se ha cambiado el tipo de sistema de la partición 3 por 8e (Linux LVM)

Orden (m para obtener ayuda): p

Disco /dev/sdb: 8589 MB, 8589934592 bytes
255 heads, 63 sectors/track, 1044 cylinders
Units = cilindros of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x5255635c

Disposit. Inicio    Comienzo      Fin      Bloques  Id  Sistema
/dev/sdb1               1          34      273073+  8e  Linux LVM
/dev/sdb2              35          68      273105   8e  Linux LVM
/dev/sdb3              69         102      273105   8e  Linux LVM

Orden (m para obtener ayuda): w
¡Se ha modificado la tabla de particiones!

Llamando a ioctl() para volver a leer la tabla de particiones.
Se están sicronizando los discos.
```



## Creación de un pv (VII)

<div class="container">
<div class="row">
    <div class="col-xs-7 col-sm-7 col-md-7 col-lg-7 col-centered">
        <a class="fancybox" href="img/lvm_ejemplo_inicialización_particiones.png" data-fancybox-group="gallery" title="Partición de tipo lvm">
          <img src="img/lvm_ejemplo_inicialización_particiones.png" alt="Partición de tipo lvm">
        </a>
    </div>
</div>
</div>



## Creación de un pv (VIII)

* Usamos el comando [`pvcreate`](http://linux.die.net/man/8/pvcreate) que inicializa una partición o disco para usar LVM.

``` bash
pvcreate [opciones] PhysicalVolume [PhysicalVolume...]
```

``` bash
[root@localhost ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created
[root@localhost ~]# pvcreate /dev/sdb2
  Physical volume "/dev/sdb2" successfully created
[root@localhost ~]# pvcreate /dev/sdb3
  Physical volume "/dev/sdb3" successfully created
```



## Consultar estado de un pv (I)

* Usando el comando [`pvs`](http://linux.die.net/man/8/pvs) obtenemos información de ocupación de cada volumen físico:
 * `PV` y `VG`: dispositivo y grupo de volúmenes al que pertenece
 * `Fmt`: formato del volumen físico
 * `Attr`: Atributos  del volumen
 * `PSize` y `PFree`: Tamaño total y libre (no asignado a lv).

``` bash
[root@localhost ~]# pvs
  PV         VG       Fmt  Attr PSize   PFree
  /dev/sdb1           lvm2 a--  266,67m 266,67m
  /dev/sdb2           lvm2 a--  266,70m 266,70m
  /dev/sdb3           lvm2 a--  266,70m 266,70m
```



## Consultar estado de un pv (II)
### Atributos
<div class="table-responsive">
  <table class="table table-hover table-bordered table-condensed">
    <thead>
      <tr>
        <th>Atributo</th>
         <th>Descripción</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>`a`</td>
        <td>Puede ser asignado a un grupo de volúmenes</td>
      </tr>

      <tr>
        <td>`x`</td>
        <td>El grupo de volúmenes al que pertenece fue exportado </td>
      </tr>
    </tbody>
  </table>
</div>

note:



## Consultar estado de un pv (III)

* El comando [`pvdisplay`](http://linux.die.net/man/8/pvdisplay) devuelve más información de cada volumen físico

``` bash
[root@localhost ~]# pvdisplay
  "/dev/sdb1" is a new physical volume of "266,67 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name
  PV Size               266,67 MiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               FL3Dmt-bKhg-Rw3h-rd2k-neuQ-Ci1z-743Tpd

  "/dev/sdb2" is a new physical volume of "266,70 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb2
  VG Name
  PV Size               266,70 MiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               nw2VmE-KskG-RSCy-P7a1-4Evv-z39Y-ni6B21

  "/dev/sdb3" is a new physical volume of "266,70 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb3
  VG Name
  PV Size               266,70 MiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               iQScRU-u3KN-aSKs-K0gJ-Hg2L-h4gd-QCXW2L
```

note:



## Eliminar un pv

* Podemos borrar un volumen físico usando [pvremove](http://linux.die.net/man/8/pvremove)

``` bash
[root@localhost ~] pvremove  /dev/sdb1
  Labels on physical volume " /dev/sdb1 " successfully wiped

[root@localhost ~]# pvs
  PV         VG       Fmt  Attr PSize   PFree
  /dev/sdb2           lvm2 a--  266,70m 266,70m
  /dev/sdb3           lvm2 a--  266,70m 266,70m
```

note:



## Redimensionar un pv
* Para redimensionar el tamaño de un volumen físico usamos el comando [pvresize](http://linux.die.net/man/8/pvresize)

``` bash
[root@localhost ~] pvresize --setphysicalvolumesize 46G /dev/sdb2
  Physical volume "/dev/sdb2" changed
  1 physical volume(s) resized / 0 physical volume(s) not resized

[root@localhost ~]# pvs
  PV         VG       Fmt  Attr PSize   PFree
  /dev/sdb1           lvm2 a--  266,70m 266,70m
  /dev/sdb2           lvm2 a--  46m  46m
  /dev/sdb3           lvm2 a--  266,70m 266,70m
```

<div class="alert alert-info">
  <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
  <strong>¡Atención!</strong> Un volumen físico no puede ser mayor que el tamaño de la partición asignada
</div>

note:



## Resumen Comandos (I)

<div class="table-responsive">
  <table class="table table-hover table-bordered table-condensed">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Explicación</th>
      </tr>
    </thead>
<tbody>
<tr>
<td><a href="http://linux.die.net/man/8/pvchange">pvchange</a></td>
<td>Cambia permisos de un PV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvck">pvck</a></td>
<td>Comprueba PV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvcreate">pvcreate</a></td>
<td>Inicializa partición o dispositivo</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvdisplay">pvdisplay</a></td>
<td>Muestra información detallada del PV</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvmove">pvmove</a></td>
<td>Mueve datos de un PV a otro</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvremove">pvremove</a></td>
<td>Borra estructura de PV de una partición</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/pvresize">pvresize</a></td>
<td>Redimensiona el PV </td>
</tr>
</tbody>

  </table>
</div>

note:
| pvchange | Changes allocation permissions on a physical volume. You might disallow new allocations if you’re making changes on other volumes and intend to delete one immediately thereafter. |
| pvck     | Checks the physical volume for errors; similar to fsck for filesystems. |
| pvcreate | Initializes a partition or other device for use by LVM; similar to mkfs for filesystems.|
| pvdisplay| Displays information on a physical volume, including the name of the volume group to which it belongs, its capacity, and how much of its capacity is not yet consumed by logical volumes. |
| pvmove   | Moves data from one physical volume to another one. You might use this prior to retiring a disk to move data off it. |
| pvremove | Removes physical volume data structures from a partition; essentially the opposite of pvcreate . Do not use this command until you’ve moved data off the physical volume and removed it from the volume group (using vgreduce ).
| pvresize | Resizes the physical volume data structures. This command does not resize the partition in which it resides; that must be done before (when enlarging) or after (when shrinking) using pvresize , typically with fdisk .
| pvs      | Summarizes information about physical volume. Similar to pvdisplay , but more succinct. |
| pvscan   | Scans disk partitions for LVM data structures. |



## Resumen Comandos (II)

<div class="table-responsive">
  <table class="table table-hover table-bordered table-condensed">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Explicación</th>
      </tr>
    </thead>
<tbody>
<tr>
<td><a href="http://linux.die.net/man/8/pvs">pvs</a></td>
<td>Muestra información resumida del PV</td>
</tr>
<tr>
<td><a href="(http://linux.die.net/man/8/pvscan">pvscan</a></td>
<td>Busca particiones con estructura lvm</td>
</tr>
</tbody>

  </table>
</div>

note:
| pvchange | Changes allocation permissions on a physical volume. You might disallow new allocations if you’re making changes on other volumes and intend to delete one immediately thereafter. |
| pvck     | Checks the physical volume for errors; similar to fsck for filesystems. |
| pvcreate | Initializes a partition or other device for use by LVM; similar to mkfs for filesystems.|
| pvdisplay| Displays information on a physical volume, including the name of the volume group to which it belongs, its capacity, and how much of its capacity is not yet consumed by logical volumes. |
| pvmove   | Moves data from one physical volume to another one. You might use this prior to retiring a disk to move data off it. |
| pvremove | Removes physical volume data structures from a partition; essentially the opposite of pvcreate . Do not use this command until you’ve moved data off the physical volume and removed it from the volume group (using vgreduce ).
| pvresize | Resizes the physical volume data structures. This command does not resize the partition in which it resides; that must be done before (when enlarging) or after (when shrinking) using pvresize , typically with fdisk .
| pvs      | Summarizes information about physical volume. Similar to pvdisplay , but more succinct. |
| pvscan   | Scans disk partitions for LVM data structures. |
