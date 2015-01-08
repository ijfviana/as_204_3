as_204_3
========

Transparencias correspondientes al tema de  **Administración avanzada de dispositivos de almacenamiento: Gestión de Volúmenes Lógicos** impartido en la asignatura de **Administración de Servidores** del **Grado en Ingeniería Informática** de la **Universidad de Huelva**.


Instalación
===========

Para instalarlo en Ubuntu 14.04, se siguen los siguientes pasos:

1. Instale nodejs

```
sudo apt-get install nodejs nodejs-legacy
```

2. Instale npm

```
sudo apt-get install npm
```

3. Instale grunt

```
sudo npm install -g grunt-cli
```

4. Instalar bower

```
sudo npm install -g bower
```

5. Clone el repositorio

```
git clone https://github.com/ijfviana/as_204_3.git
```

6. Accedemos al directorio `as_204_3`

```
cd as_204_3
```

7. Instale las dependencias mediante bower

```
bower install
```

7. Instale las dependencias del proyecto
```
npm install
```
8. Ejecute la presentacion
```
grunt server
```
