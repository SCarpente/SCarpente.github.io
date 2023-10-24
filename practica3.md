# Introducción
Nuestro objetivo sera dominar los permisos de carpetas y ficheros, para ello se nos pide asignar adecuadamente permisos a los alumnos y profesores de un instituto.

# Desarrollo

Partimos de la siguiente estructura de ficheros:

    COMPARTIDO
    ├── TEACHERS
    │   ├── prueba1.txt
    │   ├── prueba2.txt
    │   └── prueba3.txt
    ├── STUDENTS
    │   ├── prueba1.txt
    │   ├── prueba2.txt
    │   └── prueba3.txt
    ├── ESO1
    │   ├── prueba1.txt
    │   ├── prueba2.txt
    │   └── prueba3.txt
    └── ESO2
        ├── prueba1.txt
        ├── prueba2.txt
        └── prueba3.txt

Y los siguientes usuarios, grupos y respectivos permisos:

|Usuarios|Grupos|Permisos|
|-|-|-|
|t1|teachers|RW: TEACHERS
|t2|teachers|-
|s1|students, eso1|-
|s2|students, eso2|-
|s3|students|-

|Grupos|Permisos|
|-|-|
|teachers|RW: STUDENTS, ESO1, ESO2 R:TEACHERS
|students|R: STUDENTS
|eso1|R:ESO1
|eso2|R:ESO2

Una vez creados los usuarios y carpetas procedemos a implementar los permisos.

## Implementación de permisos

Para implementar los permisos utilizaremos el comando setfacl.


```bash
# Carpeta teachers

$setfacl -Rdm u:t1:rw,g:teachers:r
```

```bash
# Carpeta STUDENTS

$setfacl -Rdm g:students:r,g:teachers:rw
```

```bash
# Carpeta ESO1 y ESO2

$setfacl -Rdm g:eso1:r,g:teachers:rw
$setfacl -Rdm g:eso2:r,g:teachers:rw
```
# Comprobación

```bash
# s1 No puede leer ESO2.
```

```bash
# Creamos carpeta MATEMATICAS en ESO1, s1 puede entrar.
```
```bash
# t2 no puede crear archivos en TEACHERS
```
