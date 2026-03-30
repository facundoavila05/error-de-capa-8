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

## 1. Identificación de dispositivos y armado de la topología.

En el caso de este grupo, funcionamos como hosts de la red, teniendo un alumno que actuó como Router/Gateway predeterminado, y otro que actuó como host (debido a que un alumno no figuraba en los datos de alumnos, se tuvo un solo host), conformando así la red LAN.
El gateway predeterminado se encarga de ser la puerta de salida de los paquetes que se desean enviar fuera de la red. Además, cuando se reciben paquetes de otra red, estos llegan al gateway predeterminado, antes de decidir a qué host de la red local enviar, o si se deben enviar a otra red externa.

### NIC de los dispositivos de la red:

| Rol             | Dirección IP | Dirección MAC | Máscara de subred | Gateway por defecto |
| --------------- | ------------ | ------------- | ----------------- | ------------------- |
| Host            | 10.16.0.101  | AC:43:44      | 255.255           | 10.16.0.105         |
| Default gateway | 10.16.0.105  | AA:44:02      | 255.255           |                     |

El host de destino debía transmitir a la dirección IP 10.8.0.101, el siguiente payload: cd97 (1100110110010111 en binario)

## 2. Armado de topología

![](topografia.png)

Como se puede apreciar en la imagen, cada red LAN es conformada por una topología estrella, ya que todos los hosts se conectan a un nodo central, el default gateway, el cual gestiona el tráfico. Es la topología más común en redes LAN modernas por su alta confiabilidad: Si falla un cable, solo ese nodo pierde conexión, sin afectar al resto

## Parte 2. Inyección y detección de errores.

Para esta actividad se dividió el aula en dos grupos, tanto emisores como receptores. Cada uno tenía una técnica de EDAC distinta.

EDAC (Error Detection and Correction) hace referencia a un conjunto de técnicas utilizadas para detectar y, en algunos casos, corregir errores en datos transmitidos o almacenados. Estos errores pueden producirse debido a ruido en el canal, fallas de hardware u otras interferencias durante la transmisión.

---

### Objetivo en el laboratorio

El ejercicio consiste en simular un entorno donde:

- **Routers**: modifican intencionalmente uno o más bits del _payload_ de los paquetes.
- **Hosts (dispositivos finales)**:
  - Al enviar: aplican una técnica de EDAC.
  - Al recibir: verifican si el paquete fue modificado.

En el caso del grupo, al enviar se tuvo que aplicar la técnica de paridad por nibble, mientras que en la recepción se tuvo que aplicar la técnica del XOR por nibble.

### Paridad por nibble:

Teniendo el payload de 16 bits, se divide en 4 nibbles. Al enviar datos, se calcula el paridad de cada nibble del payload.

- Si la cantidad de unos en ese nibble es par, entonces es paridad 0 (se pone un 0)

- Si la cantidad de unos en ese nibble es impar, entonces es paridad 1 (se pone un 1)

Una vez obtenido el nibble con esta técnica, se envía junto al paquete para que el receptor pueda verificar la integridad del payload enviado.

### XOR por nibble:

Al recibir los datos, se calcula el XOR por nibble, dividiendo el payload en 4 nibbles, y aplicándoles XOR cuatro veces de manera sucesiva, se obtiene un único nibble, el cual es recibido junto al payload, funcionando como un checksum, para verificar la integridad de los datos recibidos.

---

## Paquetes

### Paquete enviado

- Dirección IP de origen: 10.16.0.1

- DIrección IP de destino: Desconocida (repartidos por el profesor)

- Payload: 5dce (0101 1101 1100 1110)

- Checksum: 9 (0101)

El checksum se calculó con la técnica de paridad por nibble, de la siguiente manera:

```
0101 1101 1100 1110

0101 -> 2 bits en uno, es par, por lo tanto = 0
1101 -> 3 bits en uno, es impar, por lo tanto = 1
1100 -> 2 bits en uno, es par, por lo tanto = 0
1110 -> 3 bits en uno, es impar, por lo tanto = 1

Checksum: 0101
```

De esta forma se envía el payload original junto al checksum 0101, para que el receptor verifique la integridad del paquete, que no haya sido modificado en el camino.

### Paquete recibido

- Dirección IP de origen: 10.1.1.2

- Dirección IP de destino: 10.16.0.1

- Payload: eeef (1110111011101111)

- Checksum: 0

Teniendo estos datos, se comprueba la integridad del paquete recibido mediante la técnica del XOR por nibble a continuación:

```
Paquete: 1110 1110 1110 1111
XOR por nibble:
1110 ⊕ 1110 = 0000
0000 ⊕ 1110 = 1110
1110 ⊕ 1111 = 0001
Resultado: 0001 (1)
```

Como se puede verificar, se obtiene un 1 al aplicarle el XOR por nibble al paquete recibido, lo cual difiere del checksum obtenido, el cual es igual a 0, lo que verifica que el paquete fue modificado en la transmisión.
Una característica que tiene la técnica del XOR es que, sabiendo que hubo un error en la transmisión por la discrepancia con el checksum, se puede obtener un conjunto finito de paquetes correctos. Esto es debido a que la operación es XOR, se pueden cambiar los valores de los bits para obtener
Debido a que se sabe que el checksum es 0, se quiere obtener ese mismo valor al realizar las tres operaciones XOR.

Se encontraron dos valores de payload que, al aplicarles la operación de XOR por nibble, se obtiene efectivamente el checksum correcto:

- 1110 1110 1110 1110

- 1110 1111 1110 1111

Se comprueba de la siguiente forma:

```
Paquete: 1110 1110 1110 1110
XOR por nibble:
1110 ⊕ 1110 = 0000
0000 ⊕ 1110 = 1110
1110 ⊕ 1110 = 0000
Resultado: 0
```

```
Paquete: 1110 1111 1110 1111
XOR por nibble:
1110 ⊕ 1111 = 0001
0001 ⊕ 1110 = 1111
1111 ⊕ 1111 = 0000
Resultado: 0
```

Se verifica que el paquete sufrió modificaciones en el camino, y que dos payload posibles son 1110111011101110 y 1110111111101111.

## Conclusión

Las técnicas de detección de errores permitieron verificar la integridad de los errores tanto en la emisión como en la recepción, con las operaciones de paridad de nibble y XOR por nibble, respectivamente. Permitieron verificar que el paquete recibido sufrió modificaciones en la transmisión, y se encontraron dos de un conjunto finito de posibles payload transmitidos. Lamentablemente, no se pudo identificar al grupo que recibió los paquetes transmitidos, por lo que por su lado no verificaron errores en la transmisión.

Es importante destacar que las técnicas de EDAC utilizadas en este laboratorio corresponden únicamente a **detección de errores**, y no a corrección. Es decir, permiten identificar que un error ocurrió, pero no permiten reconstruir el contenido original del mensaje.
