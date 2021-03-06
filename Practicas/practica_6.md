# Práctica Nº 6. Discos en RAID.

### Objetivo

- El objetivo de esta práctica es configurar varios discos en RAID 1.


### Tareas a realizar

- Hay que llevar a cabo las siguientes tareas:
	- Realizar la configuración de dos discos en RAID 1 bajo Ubuntu 12.04, automatizando el montaje del dispositivo creado al inicio del sistema.
	- Simular un fallo en uno de los discos del RAID (mediante comandos con el mdadm), retirarlo “en caliente”, comprobar que se puede acceder a la información que hay almacenada en el RAID, y por último, añadirlo al conjunto y comprobar que se reconstruye correctamente.

Adicionalmente, y como tarea opcional, se propone realizar una configuración de servidor NFS. La idea es que la máquina con el dispositivo RAID sea un servidor NFS y otra máquina monte dicha unidad usando este protocolo.

### Resultados

####Realizar la configuración de dos discos en RAID 1.

- Añadir los 2 discos a la máquina virtual antes de arrancar.


![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_1.jpg)
 

- instalación del software para configurar el RAID.
	````
	sudo apt-get install mdadm
	````
- Mostrar la información de ambos discos.
	````
	sudo fdisk -l
	````
![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_2.jpg)

- Crear el RAID 1

````
sudo mdadm -C /dev/md0 --level=raid1 --raid-devices=2 /dev/sdb /dev/sdc
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_3.jpg)

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_4.jpg)

- Dar formato.

````
sudo mkfs /dev/md0
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_5.jpg)

- crear el directorio donde se montará la unidad RAID.

````
sudo mkdir /dat
sudo mount /dev/md0 /dat
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_6.jpg)

- Comprobar el estado del RAID.

````
sudo mdadm --detail /dev/md0
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_7.jpg)


- Editar el fichero `/etc/fstab` para que el sistema monte automáticamente
 el dispositivo RAID.
	- Obtener el UUID 
	````
	ls -l /dev/disk/by-uuid/
	````
![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_8.jpg)

	- Añadir al final del archivo la línea para que monte automáticamente el dispositivo RAID.

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_11.jpg)


####Simular un fallo en uno de los discos del RAID retirarlo “en caliente”, comprobar que se puede acceder a la información que hay almacenada en el RAID, y por último, añadirlo al conjunto y comprobar que se reconstruye correctamente.

`nota: el sistema se reinicio,por lo que /dev/md0 Linux lo ha renombrado como  /dev/md127` 

- simular el fallo.

```` 
sudo mdadm --manage --set-faulty /dev/md127 /dev/sdb
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_12.jpg)

- retirar en caliente

```` 
sudo mdadm --manage --remove /dev/md127 /dev/sdb
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_13.jpg)

- añadir en caliente

```` 
sudo mdadm --manage --add /dev/md127 /dev/sdb
````

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/p6_14.jpg)



- opcional 

`nota esta configuración la realicé con otras máquinas virtuales con SO ubuntu server 16.04, ya que no logré solucionar lo de instalar el servidor nfs en el servidor que contiene el raid.`
 

- Instalar el servidor nfs

```
sudo apt-get install nfs-kernel-server
```
- Editar el fichero </etc/exports>

```
sudo nano /etc/exports
// añadimos al final
/directorio a compartir/ máquina1(opcion1,opcion2,...) máquina2(opcion1,opcion2,...) 
```
![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/t7_nfs.jpg)

- iniciar el servicio

```
service nfs-kernel-server start
```

- Instalar el cliente NFS

```
sudo apt-get install nfs-common
```
- Creamos el directorio en el cual vamos a realizar el montaje.
```
mkdir /COMPARTIR

sudo mount maquinaServidorNFS:/COMPARTIR /COMPARTIR

df -h 
```

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/t7_nfs_1.jpg)

-  Comprobamos los permisos de lectura y escritura

![imagen](https://github.com/marlenelis/SWAP1516/blob/master/images/t7_nfs_2.jpg)





