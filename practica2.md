# Política de contraseñas

## Introducción

La seguridad de nuestro sistema puede verse amenazada.  Una buena politica de contraseñas reforzaria la seguridad. El objetivo de la practica es aprender y configurar las directrices de seguridad, para ello utilizaremos el modulo pam pw_quality.

## Desarrollo

## Instalación

```bash
 $ sudo apt install libpwquality-tools
```

## Configuración

Para configurar nuestra politica de contraseñas lo haremos modificando el fichero `/etc/security/pwquality.conf`

Breve explicación del significado de cada opción de configuración.

      -Número de caracteres de la nueva contraseña que no deben estar presentes en la contraseña anterior.
      difok

      -Tamaño mínimo aceptable para la nueva contraseña.
      minlen

      -El crédito máximo por tener dígitos en la nueva contraseña.
      dcredit. (valor negativo = minimo)

      -El crédito máximo por tener caracteres en mayúsculas en la nueva contraseña. (valor negativo = minimo)
      ucredit

      -El crédito máximo por tener caracteres en minúscula en la nueva contraseña. (valor negativo = minimo)
      lcredit

      -El crédito máximo por tener caracteres especiales en la nueva contraseña. (valor negativo = minimo)
      ocredit

      -El número mínimo de clases de caracteres requeridas para la nueva contraseña.
      minclass

      -El número máximo de mismos caracteres consecutivos permitidos en la nueva contraseña.
      maxrepeat

      -El número máximo de caracteres consecutivos permitidos de la misma clase en la nueva contraseña.
      maxclassrepeat

      -Verifica información del usuario.
      gecoscheck

Nosotros crearemos la siguiente política sobre la que realizaremos las comprobaciones:

        difok = 2
        minlen = 9
        dcredit = -1
        ucredit = -1
        lcredit = -1
        ocredit = -1
        minclass = 4
        maxrepeat = 2
        maxclassrepeat = 4

Por lo tanto se nos exige que al menos tenga 2 caracteres nuevos, una longitud minima de 9, minimo un digito, una letra mayuscula, una letra minuscula y un caracter especial, requiere un caracter de cada clase, maximo de 2 caracteres iguales consecutivos y maximo de 4 caracteres de la misma clase consecutivos.

## Comprobación

Para realizar las comprobaciones utilizaremos la herramienta `pwscore` incluida en el paquete de `pwquality`.

* 1ª Comprobación:
        
        'echo "hola123456" | pwscore' 
        Falló la comprobación de calidad de la contraseña:
        La contraseña contiene más de 4 caracteres de la misma clase en forma consecutiva

* 2ª Comprobación: 

        'echo "Hola1S?2" | pwscore'
        Falló la comprobación de calidad de la contraseña:
        La contraseña tiene menos de 9 caracteres

* 3ª Comprobación:

        'echo "Hola1?Sw12" | pwscore'
        50
