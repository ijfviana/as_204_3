## ¿Qué es un grupo de volúmenes (vg)?

<div class="row">
    <div class="col-xs-9 col-sm-9 col-md-9 col-lg-9 col-centered">
        <a class="fancybox" href="img/lvm_overview_2.png" data-fancybox-group="gallery" title="Grupo de volúmenes">
          <img height="550px" src="img/lvm_overview_2.png" alt="Grupo de volúmenes">
        </a>
    </div>
</div>

note:
* Is a collection of one or more physical volumes, which are managed as a single allocation space.
* The use of volume groups as an intermediate level of organization enables you to create larger filesystems than any individual device could handle by itself.



## Creación de un vg
* Usamos el comando [`vgcreate`](http://linux.die.net/man/8/vgcreate)  para definir grupos de volúmenes

``` bash
vgcreate [opciones] VolumeGroupName PhysicalDevicePath [PhysicalDevicePath...]
```

```bash
~ # vgcreate ejemplo /dev/sdb1 /dev/sdb2 /dev/sdb3
  Volume group "ejemplo" successfully created
```

note:



## Consultar estado del vg (I)

* El comando [`vgs`](http://linux.die.net/man/8/vgs) me devuelve *información general* sobre los grupos
 * `VG`: nombre del grupo de volúmenes
 * `#PV`: número de volúmenes físicos que lo forman
 * `#LV`: número de volúmenes lógicos que lo usan
 * `Attr`: Atributos
 * `VSize` y `VFree`: Tamaño total y libre

``` bash
[root@localhost ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  ejemplo    3   0   0 wz--n- 792,00m 792,00m
```

note:



## Consultar estado del vg (II)
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
        <td>`r`,`w`</td>
        <td>(r)ead &amp; (w)rite permissions</td>
      </tr>
      <tr><td>`z`</td><td>resi(z)eable</td>
      </tr>
      <tr><td>`x`</td><td>e(x)ported</td>
      </tr>
      <tr><td>`p`</td><td>(p)artial</td>
      </tr>
      <tr><td>`c`,`n`,`a`,`i`</td><td>allocation policy (c)ontiguous, c(l)ing, (n)ormal, (a)nywhere, (i)nherited</td>
      </tr>
      <tr>
        <td>`c`</td><td>(c)luster</td>
      </tr>
    </tbody>
  </table>
</div>

note:



## Consultar estado del vg (III)

* El comando [`vgdisplay`](http://linux.die.net/man/8/vgdisplay) me muestra *información detallada* sobre los grupos

```bash
[root@localhost ~]# vgdisplay
  --- Volume group ---
  VG Name               ejemplo
  System ID
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               792,00 MiB
  PE Size               4,00 MiB
  Total PE              198
  Alloc PE / Size       0 / 0
  Free  PE / Size       198 / 792,00 MiB
  VG UUID               1Dfbuq-IuX0-KRa4-KDq4-ychW-20qz-49upPc
```

note:



## Redudir un vg

* Podemos borrar un elemento de un grupo de volúmenes mediante el comando [`vgreduce`](http://linux.die.net/man/8/vgreduce)
``` bash
[root@localhost ~]# vgreduce ejemplo /dev/sdb2
  Removed "/dev/sdb2" from volume group "ejemplo"
[root@localhost ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  ejemplo    2   0   0 wz--n- 528,00m 528,00m
```
* Observe la reducción en el tamaño del grupo (792-258=264)

<div class="alert alert-warning">
    <strong>¡Atención!</strong> Si no indicamos volúmenes físicos borra todos
</div>

note:
* Allows you to remove one or more unused physical volumes from a volume group.



## Aumentar un vg

* Podemos añadir un nuevo volumen físico a un grupo mediante el comando [`vgextend`](http://linux.die.net/man/8/vgextend)
``` bash
[root@localhost ~]# vgextend ejemplo /dev/sdb2
  Volume group "ejemplo" successfully extended
[root@localhost ~]# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  ejemplo    3   0   0 wz--n- 792,00m 792,00m
```
* El tamaño total vuelve a ser de 792

note:
* Allows you to add one or more initialized physical volumes (see pvcreate(8)) to an existing volume group to extend it in size.
* Moreover, it allows you to re-add a physical volume that has gone missing previously, due to a transient device failure, without re-initialising it.



## Cambiar atributos de un grupo

* El comando [`vgchange`](http://linux.die.net/man/8/vgchange) permite cambiar el estado de un grupo de volúmenes.
* Sólo si el grupo está marcado como activo se pude usar
* Para activar todo los volúmenes

``` bash
[root@localhost ~]#  vgchange -ay
```

<div class="alert alert-info">
  <button type="button" class="close" data-dismiss="alert" aria-hidden="true">&times;</button>
  <strong>¡Atención!</strong> * Un grupo de volúmenes esta marcado como desactivo si arrancamos en modo recuperación
</div>

note:
* Allows  you  to  change  the attributes of one or more volume groups.
* Its main purpose is to activate  and  deactivate  VolumeGroup-Name,  or  all  volume groups if none is specified.
* Only active volume groups are subject to changes and allow access to  their  logical  volumes.



## Resumen comandos (I)

<div class="table-responsive">
  <table class="table table-hover table-condensed table-bordered">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Explicación</th>
      </tr>
    </thead>
    <tbody>
<tr>
<td><a href="http://linux.die.net/man/8/vgcfgbackup">vgcfgbackup</a></td>
<td>Guarda metadatos de un VG </td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgcfgrestore">vgcfgrestore</a></td>
<td>Restaura metadatos de un VG </td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgchange">vgchange</a></td>
<td>Cambia a tributos de un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgck">vgck</a></td>
<td>Comprueba metadatos de un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgconvert">vgconvert</a></td>
<td>Convierte un VG entre versiones</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgcreate">vgcreate</a></td>
<td>Crea un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgdisplay">vgdisplay</a></td>
<td>Muestra información detallada de un VG</td>
</tr>
</tbody>
  </table>
</div>

note:
| Command | Explanation |
|---------|-------------|
| [vgcfgbackup](http://linux.die.net/man/8/vgcfgbackup)  | Backs up volume group metadata, by default to files in /etc/lvm/backup .
| [vgcfgrestore](http://linux.die.net/man/8/vgcfgrestore)  | Restores volume group metadata, by default from files in /etc/lvm/backup .
| [vgchange](http://linux.die.net/man/8/vgchange)  | Changes certain volume group attributes. Most importantly, it may be used to manually activate or deactivate a volume group.
| [vgck](http://linux.die.net/man/8/vgck)      | Checks volume group metadata; similar to fsck for filesystems.
| [vgconvert](http://linux.die.net/man/8/vgconvert)  | Converts volume group metadata from one version to another. LVM version 2 (LVM2) is the current version in 2011 and has been for some time.
| [vgcreate](http://linux.die.net/man/8/vgcreate)  | Creates a volume group, starting with one or more physical volumes you specify.
| [vgdisplay](http://linux.die.net/man/8/vgdisplay)  | Displays information on your volume groups, including information on the number of physical devices and logical volumes as well as the total and free space.
| [vgexport](http://linux.die.net/man/8/vgexport)  | Makes a volume group unknown to the system, enabling it to be moved to another computer for integration into its LVM configuration.
| [vgextend](http://linux.die.net/man/8/vgextend)  | Adds physical volumes to an existing volume group.|
|



## Resumen comandos (II)

<div class="table-responsive">
  <table class="table table-hover table-condensed table-bordered">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Explicación</th>
      </tr>
    </thead>
    <tbody>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgexport">vgexport</a></td>
      <td>Marca el VG como desconocido</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgextend">vgextend</a></td>
      <td>Aumenta el número de PV de un  VG</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgimport">vgimport</a></td>
      <td>Marca el VG como conocido para el sistema.</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgimportclone">vgimportclone</a></td>
      <td>Importa un VG</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgmerge">vgmerge</a></td>
      <td>Une dos VG (pasa los PV de un VG a otro)</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgmknodes">vgmknodes</a></td>
      <td>Actualiza los ficheros de dispositivo del VG.</td>
      </tr>
      <tr>
      <td><a href="http://linux.die.net/man/8/vgreduce">vgreduce</a></td>
      <td>Borra PV de un VG</td>
      </tr>
    </tbody>
  </table>
</div>

note:
| Command | Explanation |
|---------|-------------|
| [vgcfgbackup](http://linux.die.net/man/8/vgcfgbackup)  | Backs up volume group metadata, by default to files in /etc/lvm/backup .
| [vgcfgrestore](http://linux.die.net/man/8/vgcfgrestore)  | Restores volume group metadata, by default from files in /etc/lvm/backup .
| [vgchange](http://linux.die.net/man/8/vgchange)  | Changes certain volume group attributes. Most importantly, it may be used to manually activate or deactivate a volume group.
| [vgck](http://linux.die.net/man/8/vgck)      | Checks volume group metadata; similar to fsck for filesystems.
| [vgconvert](http://linux.die.net/man/8/vgconvert)  | Converts volume group metadata from one version to another. LVM version 2 (LVM2) is the current version in 2011 and has been for some time.
| [vgcreate](http://linux.die.net/man/8/vgcreate)  | Creates a volume group, starting with one or more physical volumes you specify.
| [vgdisplay](http://linux.die.net/man/8/vgdisplay)  | Displays information on your volume groups, including information on the number of physical devices and logical volumes as well as the total and free space.
| [vgexport](http://linux.die.net/man/8/vgexport)  | Makes a volume group unknown to the system, enabling it to be moved to another computer for integration into its LVM configuration.
| [vgextend](http://linux.die.net/man/8/vgextend)  | Adds physical volumes to an existing volume group.|
|



## Resumen comandos (III)

<div class="table-responsive">
  <table class="table table-hover table-condensed table-bordered">
    <thead>
      <tr>
        <th>Comando</th>
        <th>Explicación</th>
      </tr>
    </thead>
    <tbody>
<tr>
<td><a href="http://linux.die.net/man/8/vgremove">vgremove</a></td>
<td>Borra un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgrename">vgrename</a></td>
<td>Renombra un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgs">vgs</a></td>
<td>Muestra información resumida de un VG</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgscan">vgscan</a></td>
<td>Busca VG en el sistema</td>
</tr>
<tr>
<td><a href="http://linux.die.net/man/8/vgsplit">vgsplit</a></td>
<td>Divide un VG en dos (reparte los PV) </td>
</tr>
</tbody>
  </table>
</div>

note:
| Command | Explanation |
|---------|-------------|
| [vgimport](http://linux.die.net/man/8/vgimport)  | Makes a volume group known to the system.|
| [vgimportclone](http://linux.die.net/man/8/vgimportclone)  | Imports a volume group, renaming conflicting logical volumes. This may be used if you’ve cloned a volume group for backup purposes and find you need to access the backup.
| [vgmerge](http://linux.die.net/man/8/vgmerge)  | Merges two volume groups, one of which must be inactive.
| [vgmknodes](http://linux.die.net/man/8/vgmknodes)  | Updates the device files in /dev that refer to logical volumes. Normally not called manually.
| [vgreduce](http://linux.die.net/man/8/vgreduce)  | Removes one or more unused physical volumes from a volume group.
| [vgremove](http://linux.die.net/man/8/vgremove)  | Completely removes a volume group from the computer; effectively the opposite of vgcreate .
| [vgrename](http://linux.die.net/man/8/vgrename)  | Renames a volume group.
| [vgs](http://linux.die.net/man/8/vgs)  | Displays summary information about a volume group; similar to vgdisplay but with more terse output.
| [vgscan](http://linux.die.net/man/8/vgscan)  | Scans the system for volume groups. Normally unneeded but may be helpful after hot-swapping a new disk.
| [vgsplit](http://linux.die.net/man/8/vgsplit)  | Splits a volume group into two; effectively the opposite of vgmerge .
