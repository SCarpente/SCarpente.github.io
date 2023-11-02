# Introducción

La seguridad informática es un aspecto fundamental en la gestión de sistemas y datos en cualquier entorno tecnológico. En este contexto, la implementación de un RAID 1+0 se destaca como una práctica esencial para los administradores de sistemas. Este nivel de redundancia ofrece una protección robusta al duplicar los datos y distribuirlos en múltiples discos, asegurando la disponibilidad continua de la información en caso de fallos en unidades de almacenamiento. Así, los administradores pueden mantener la integridad de los datos y minimizar el tiempo de inactividad, lo que resulta crucial para la continuidad de operaciones y la prevención de pérdidas críticas en entornos informáticos.

# Desarrollo

## RAID 1+0

Para esta practica utilizaremos la herramienta MDADM, asi que el primer paso sera instalarla.

Para realizar un raid 1+0 necesitaremos 4 discos del mismo tamaño en este caso crearemos 4 discos de dos gigas, en mi sistema aparecen como sdb, sdc, sde y sdd. Vamos a trabajar con ellos para crear el raid.

- Paso 1: Creacion raid 1
    
```bash
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
mdadm --create /dev/md1 --level=1 --raid-devices=2 /dev/sdd /dev/sde
```

- Paso 2: Creación raid 0

```bash
mdadm --create /dev/md2 --level=0 --raid-devices=2 /dev/md0 /dev/md1
```

- Paso 3: Asegurarnos de que la matriz se asamble automaticamente al inicio del sistema.

```bash
mdadm --detail --scan > /etc/mdadm/mdadm.conf
```

- Paso 4: Montar RAID en un directorio

```bash
mkfs.ext4 /dev/md0
mkdir /datos
mount /dev/md0 /datos
```

Paso 5: Creación discos repuesto

Vamos a añadir dos discos mas en nuestro sistema, haremos que funcionen de repuesto para nuestros RAID 1 por lo tanto añadiremos uno por RAID. (Cuidado al reiniciar el sistema puedo que el nombre de nuestos raid cambie en mi caso a cambiado a md126 y md127)

```bash
mdadm --add /dev/md126 /dev/sdf
mdadm --add /dev/md127 /dev/sdg
```

- Paso 6: Simular fallo disco

Vamos a simular un fallo en un disco de nuestro RAID y comprobar que entre en funcionamiento los discos de repuesto

Fallo disco:

```bash
mdadm /dev/md126 --fail /dev/sdb --remove /dev/sdb
mdadm /dev/md127 --fail /dev/sde --remove /dev/sde
```

Comprobamos que entren en funcionamiento los discos de repuesto:

```bash
mdadm --detail /dev/md126
mdadm --detail /dev/md127
```

## LVM

- Paso 1: Convertir RAID en volúmen físico.

```bash
pvcreate /dev/md125
```

- Paso 2: Crear grupo de volúmenes.

```bash
vgcreate VGroup1 /dev/md125
```

- Paso 3: Creamos dos volúmenes lógicos.

```bash
lvcreate -l 70%FREE -n LV1 VGroup1
lvcreate -l 30%FREE -n LV2 VGroup1
```

- Paso 4: Formatear LV´s en Ext4.

```bash
mkfs.ext4 /dev/VGroup1/LV1
mkfs.ext4 /dev/VGroup1/LV2
```

- Paso 5: Crear directorio y montar los volumenes logicos en ellos

```bash
mkdir /LVDatos1
mkdir /LVDatos2

mount /dev/VGroup1/LV1 /LVDatos1
mount /dev/VGroup1/LV2 /LVDatos2
```
