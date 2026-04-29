# Redes de Computadoras - Trabajo Práctico 3

### Grupo: Error de Capa 8

### Profesores:

- Facundo O. Cuneo

- Santiago M. Henn

### Integrantes

- Facundo Emanuel Avila Diaz Moreno

- Facundo Esteban Guerrero Pozzi

- Ignacio Joaquin Vigezzi

## Investigación conceptual

### a) ¿Qué es SSH y qué problema resuelve?

SSH (Secure Shell) es un protocolo de red que permite administrar y acceder a equipos remotos de forma segura a través de una conexión cifrada. Resuelve el problema de los protocolos previos como Telnet o rlogin, que transmitían credenciales y comandos en texto plano, exponiendo todo a quien estuviera capturando tráfico en la red.

### b) Diferencia entre autenticación y cifrado

Son dos términos que suelen confundirse.
La autenticación, por un lado, verifica la identidad ("¿quién sos?"). Confirma que el usuario sea quien dice ser, mediante sus credenciales. Por el otro lado, el cifrado protege la confidencialidad del contenido ("¿qué contiene el mensaje?"). Transforma los datos para que un tercero que los intercepte no pueda leerlos.

### c) ¿Qué es una clave pública y una clave privada?

Son un par de claves matemáticamente relacionadas usadas en criptografía asimétrica. La clave pública se distribuye libremente y se usa para cifrar mensajes o verificar firmas. La clave privada se mantiene en secreto y se usa para descifrar lo que se cifró con la pública o para firmar digitalmente. Lo que cifra una solo lo puede descifrar la otra.

### d) ¿Por qué la clave privada no debe compartirse?

La clave privada no debe compartirse debido es la prueba de identidad, cualquiera que la posea puede hacerse pasar por la persona, autenticarse en servidores donde está autorizada la clave pública, y descifrar contenido dirigido al dueño de la clave pública. Compartirla equivale a entregar tu identidad digital.

### e) ¿Qué ventajas tienen las claves SSH frente a contraseñas?

Las ventajas que tienen las claves SSH frente a contraseñas son las siguientes:

- Resisten ataques de fuerza bruta (las claves típicas son de 2048-4096 bits, imposibles de adivinar).

- No se transmiten por la red durante la autenticación (se usa un challenge-response).

- No se pueden "filtrar" por phishing como una contraseña.

- Permiten automatización segura (scripts, CI/CD) sin guardar contraseñas en texto plano.

- Se pueden revocar individualmente sin afectar a otros usuarios.

## Verificación conexión SSH con las VMs reservadas

Se utilizaron las PCs 1 y 2. Se conectó a la PC2 a traves de ssh utilizando el siguiente comando en la terminal:

```sh
ssh -i pc2_key.pem pc-alumnos-2@4.206.217.29
```

Finalmente, se creó la carpeta **ErrorDeCapa8** en el directorio home de la VM.

![](carpeta.jpg)

---

### Captura de tráfico SSH con Wireshark

Se utilizó Wireshark para capturar tráfico correspondiente a una sesión SSH. Al inspeccionar los paquetes capturados, se observa que el contenido de la carga útil aparece como datos cifrados (encrypted packet).

![](ssh-enc.jpg)

Esto es esperable, ya que SSH es un protocolo diseñado específicamente para garantizar la confidencialidad y la integridad de la comunicación. Una vez establecida la conexión y negociadas las claves de sesión mediante criptografía asimétrica, todo el tráfico posterior se cifra utilizando algoritmos simétricos.

Como consecuencia, aunque es posible visualizar metadatos como direcciones IP, puertos, longitud de los paquetes y números de secuencia TCP, no es posible descifrar el contenido de los datos transmitidos (por ejemplo, comandos o respuestas) sin acceso a las claves de cifrado utilizadas en la sesión.

En conclusión, no es posible interpretar el contenido de los paquetes SSH capturados en Wireshark debido a que la información viaja cifrada extremo a extremo, cumpliendo con los objetivos de seguridad del protocolo.

![](wireshark-enc.jpg)

---

### Montaje de un servidor TCP

Para la consigna 4, se trabajó en conjunto con el grupo Xi Jinping Revenge, ya que ambos se encontraban en la facultad al momento de hacer el trabajo.

Utilizando netcat, con el siguiente comando: **netcat l <puerto>** se monta un servidor, utilizando un puerto válido del 5000 al 6000. Se optó por utilizar el puerto 5555

![](netcat-local.jpg)

Con este comando, nuestra PC actúa como un servidor, escuchando por el puerto 5555 los mensajes de la PC1.
La PC1 envía los mensajes con el comando **ncat 4.206.217.219 5555**, el cual utiliza la terminal para enviar los mensajes a la PC2, que actúa como servidor. De forma alternativa, se puede utilizar **echo hello world | ncat 4.206.217.219 5555**

![](netcat.jpg)

La opción **Follow TCP Stream** en Wireshark, permite comprobar que el contenido es completamente legible, no posee ningún tipo de cifrado de por medio, por lo que cualquier interceptor podría descifrar el mensaje sin esfuerzo

Netcat no cifra absolutamente nada. Es una herramienta de red "cruda" que abre un socket TCP o UDP y manda los bytes tal cual. Por ello, en wireshark, se pudo observar completamente el handshake de tres vías (SYN → SYN-ACK → ACK), y luego cada mensaje escrito en la terminal apareció en texto plano dentro del payload del paquete.

---

### Montaje de servidor UDP

Para UDP se utilizó **ncat -u -l <puerto>** en la VM y **ncat -u <VM_IP> <puerto>** desde la notebook local. No hay handshake, ya que UDP es sin conexión, y los mensajes viajan igualmente en texto plano. Wireshark los muestra directo en el panel de bytes.

Netcat es el equivalente a hablar a los gritos en una sala llena de gente. Cualquiera en el camino (el router, el ISP, los routers intermedios, el datacenter de Azure) puede leer todo si tiene acceso al tráfico.

---

### Conexión con otra VM mediante netcat, envío de mensajes ida y vuelta

Trabajando nuevamente en conjunto con el grupo **Xi Jinping Revenge**, se procedió a establecer conexión de las dos PCs a través de netcat. Se pudo hacer un envío y recepción de frases entre ellas, al estilo chat entre las dos instancias.

![](enviorecepcion.jpg)

---

### Despliegue de servidor HTTP

En la carpeta **ErrorDeCapa8** de la PC2, se crea un archivo index.html, y se levanta un servidor web a esa página creada, a traves de Python y Http-Server

```sh
python3 -m http.server 8000
```

![](paginaweb.jpg)

Analizando en Wireshark, se puede comprobar que efectivamente se puede descifrar el contenido. El motivo es que se está utilizando HTTP, sin la S, el cual envía el contenido en texto plano. En Wireshark se aprecia perfectamente el request **(GET / HTTP/1.1)** y el response **(HTTP/1.0 200 OK)**, headers, y el HTML completo del index.html visible byte por byte.

### ¿Se podría intervenir?

Si, a través del ataque Man-in-the-Middle, un atacante posicionado en el medio podría leer todo las peticiones enviadas al servidor, y las respuestas de éste último (sniffing pasivo). También se podría modificar el contenido en el vuelo, por ejemplo, cambiar mi pagina index.html, el título de "Bienvenido a nuestro servidor!" por otra cosa, inyectar código JS malicioso, o redirigir a otro sitio. Además podría inyectar requests falsos, haciéndose pasar por el cliente.

## Video de Verisitasium

### Relacionar el problema que aborda el video con los TPs 1), 2) y 3). ¿Qué cosas que hemos aprendido se aplican directamente al problema demostrado?

La temática del video tiene mucha relación con lo que venimos viendo en los trabajos prácticos.
Empezando por el trabajo práctico 1, esto se puede relacionar pensando en la parte de control de errores, en el video no especifica puntualmente este apartado pero sí menciona que hay bits de flags, que determinan un comportamiento u otro, y justamente son los que se aprovechan para realizar el ataque, por lo que es importante que no haya fallos en estos mensajes provocados por ej por ruido debido a que un bit flipeado puede dar a un comportamiento totalmente distinto, como tomar una transacción verificada cuando no lo esta por ej.
En cuanto al tp2 no hay mucha relación de esos temas con el video, pero si quizás se podría mencionar que en la capa física, la información en lugar de viajar por un cable de red en este caso viaja por el aire ya que se aprovecha el uso de NFC en este ataque.
Y con el tp3 si se comparten muchos conceptos, en el video se habla sobre cifrado ,
donde dicen que los datos de las transacciones no están cifrados debido a que este protocolo se utiliza con distintos dispositivos de distintas marcas lo cual obligaría a tenerlos actualizados lo que puede ser un poco complicado, esto justamente es lo que deja vulnerable a los datos, pudiendo hacerse el estudio posterior de los bits de flags para poder engañar el sistema, esto se puede relacionar con que antes para al usar otros protocolos para acceder de forma remota a otros dispositivos se enviaban los datos en texto plano como el caso del video, dejando vulnerable la comunicación a diferencia de ahora que como vimos en el tp3 se usa ssh que utiliza cifrado para evitar eso justamente, como pudimos ver cuando quisimos descifrar lo capturado con wireshark.
También cuando habla de criptografía asimétrica, menciona claves privadas y públicas, estos conceptos como vimos en la investigación conceptual de este tp, son fundamentales para el cifrado RSA y la protección de la autenticación de cada persona y por consiguiente el acceso a sus datos.

### ¿Qué cosas deberíamos tener en cuenta dado el principio de confidencialidad en las redes de computadoras y los resultados obtenidos en este laboratorio?

Tomando en cuenta el principio de confidencialidad de las redes y lo que vimos en este tp3, podemos decir que queda bastante claro que no se puede confiar en la red como medio seguro por defecto. Cualquier persona con acceso a la misma red puede capturar el tráfico sin demasiado esfuerzo, y ver todo lo que está circulando. Esto rompe completamente la idea de privacidad si no se toman medidas adicionales.
Acá es donde aparece el problema del texto plano, protocolos como HTTP, Telnet, o incluso Netcat, envían la información sin ningún tipo de protección. En el laboratorio se vio que todo lo que se mandaba podía leerse directamente desde los paquetes capturados. Si en lugar de eso hubiese habido información sensible, también hubiera quedado expuesta sin ninguna barrera.
Por eso es fundamental el uso de protocolos seguros que implementen cifrado. Como vimos con el caso de SSH aunque los paquetes también se pueden capturar, el contenido ya no es legible, porque está cifrado y protegido mediante claves que se negocian durante el inicio de la comunicación.
En consecuencia, a nivel de diseño de sistemas o administración, esto obliga a evitar completamente el uso de protocolos en texto plano cuando hay datos sensibles. Sería recomendable utilizar siempre alternativas seguras, como HTTPS en lugar de HTTP o SSH en lugar de Telnet.
