# Logs centralizados

## Introducción
 El trabajo del administrador de sistemas pude verse simplificado controlando los registros de procesos del sistema (Logs)
## Desarrollo
Para facilitar el trabajo del administrador, haremos que los logs se guarden tambien en un servidor. Para ello utilizaremos la ayuda de la herramienta ***rsyslog***.

En caso de no tenerlo lo instalaremos y realizaremos los siguientes pasos:

**Configuración servidor**

- Paso 1

    Modificamos el fichero /etc/rsyslog.conf :
    ```bash
    $ nano /etc/rsyslog.conf
    ```
    Descomentamos las siguientes líneas:
    ```bash
    $ModLoad imudp
    $UDPServerRun 514
    ```

- Paso 2

    Reiniciamos el servicio y comprobamos el estado:
    ```bash
    $ systemctl restart rsyslog
    $ systemctl status rsyslog
    ```

- Paso 3
  
    Configuramos el cortafuegos para que permita la comunicacióncon por el puerto 514:
    ```bash
    $ ufw allow 514/tcp
    $ ufw allow 514/udp
    ```
    Y reiniciamos el cortafuegos:
    ```bash
    $ ufw reload
    ```

**Configuración del cliente**

Repetimos el proceso de instalación de rsyslog si fuese necesario.

- Paso 1

    Modificamos el fichero /etc/rsyslog.conf :
    ```bash
    $ nano /etc/rsyslog.conf
    ```
    Añadimos la siguiente línea al final del archivo:
    ```bash
    *.* IP_SERVIDOR:514
    ```

- Paso 2

    Reiniciamos el servicio:
    ```bash
    $ systemctl restart rsyslog
    ```

**Comprobación**

Desde el cliente escribimos el siguiente comando para crear una entrada en el registro:
 ```bash
    $ logger "prueba"
```

En el servidor escribimos el siguiente comando para leer las ultimas lineas del registro:
```bash
    $ tail /var/log/syslog
```
Y deberíamos ver el registro que acabamos de crear reflejado en el archivo.
