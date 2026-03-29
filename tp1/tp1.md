# Redes de Computadoras - Trabajo Práctico 1

### Grupo: Error de Capa 8

### Profesores:

- Facundo O. Cuneo

- Santiago M. Henn

## Integrantes

| Nombre                            | Correo Electrónico              |
| --------------------------------- | ------------------------------- |
| Facundo Emanuel Avila Diaz Moreno | facundo.avila.027@mi.unc.edu.ar |
|                                   |                                 |
|                                   |                                 |

## Identificación de dispositivos y armado de la topología.

En el caso de este grupo, funcionamos como hosts de la red, teniendo un alumno que actuó como Router/Gateway predeterminado, y otro que actuó como host (debido a que un alumno no figuraba en los datos de alumnos, se tuvo un solo host), conformando así la red LAN.

### NIC de los dispositivos de la red:

| Rol             | Direccion IP | Direccion MAC | Máscara de subred | Gateway por defecto |
| --------------- | ------------ | ------------- | ----------------- | ------------------- |
| Host            | 10.16.0.101  | AC:43:44      | 255.255           | 10.16.0.105         |
| Default gateway | 10.16.0.105  | AA:44:02      | 255.255           |                     |

El host de destino debía transmitir a la dirección IP 10.8.0.101, el siguiente payload: cd97 (1100110110010111 en binario)
