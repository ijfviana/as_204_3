## Introducción

<div class="row">
    <div class="col-xs-11 col-sm-11 col-md-11 col-lg-11 col-centered">
        <a class="fancybox" href="img/particionado_estatico.png" data-fancybox-group="gallery" title="Particionamiento estático">
        <img height="550px" src="img/particionado_estatico.png" alt="Particionamiento estático">
        </a>
    </div>
</div>

note:
* Partitions, as traditionally used on most computers, are inflexible.
* A partition begins at a particular sector on the disk, ends at another sector on the disk, and contains all the sectors between those two, in sequential order.
* En el ejemplo se muestra un disco duro particionado. La eliminación, redimensionado o unión de particiones es una tarea peligrosa y lenta
* LVM exists, in part, to solve this type of problem.



## Vista general (I)

<div class="row">
    <div class="col-xs-8 col-sm-8 col-md-8 col-lg-8 col-centered">
        <a class="fancybox" href="img/overview_lvm.png" data-fancybox-group="gallery" title="Vista general lvm">
            <img src="img/overview_lvm.png" alt="Vista general lvm">
        </a>
    </div>
</div>

note:
* In an LVM configuration, logical volumes, which are the LVM equivalent of partitions, are allocated much like files in a filesystem.
* When you create a file, you needn’t worry about what sectors it’s occupying or whether there’s enough contiguous space left to hold a large file.
* The filesystem deals with those details and enables files to be broken into pieces to fit into multiple small chunks if there isn’t enough contiguous free space.
* LVM goes further, though: LVM enables logical volumes to span multiple partitions or even hard disks, thus enabling consolidation of space much like a linear RAID or RAID 0 configuration.



## Vista general (II)
### Desventajas

*  Su complejidad:
 * Añade nuevos elementos como los grupos de volúmenes y los volúmenes lógicos
 * Nuevos comandos para su gestión
* Puede llegar a ser muy peligrosos: LVM permite "unir en uno" varios discos, el fallo en uno implica la pérdida de datos



## Vista general (III)
### Pasos

<div class="row">
    <div class="col-xs-11 col-sm-11 col-md-11 col-lg-11 col-centered">
        <a class="fancybox" href="img/pasos_lvm.png" data-fancybox-group="gallery" title="Pasos para trabajar con volúmenes lógicos">
            <img height="550px" src="img/pasos_lvm.png" alt="Pasos para trabajar con volúmenes lógicos">
        </a>
    </div>
</div>

note:



## Vista general (IV)
### Ejemplo

<div class="container">
<div class="row">
    <div class="col-xs-4 col-sm-4 col-md-4 col-lg-4 col-centered">
        <a class="fancybox" href="img/lvm_ejemplo.png" data-fancybox-group="gallery" title="Ejemplo LVM">
            <img height="550px" src="img/lvm_ejemplo.png" alt="Ejemplo LVM">
        </a>
    </div>
</div>
</div>

note:

* We will explain the operation of LVM using the example described in this figure: a computer with two hard drives, the second with three partitions that configure with lvm
