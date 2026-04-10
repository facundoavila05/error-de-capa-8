# Redes de Computadoras - Trabajo Práctico 2

### Grupo: Error de Capa 8

### Profesores:

- Facundo O. Cuneo

- Santiago M. Henn

### Integrantes

- Facundo Emanuel Avila Diaz Moreno

- Facundo Esteban Guerrero Pozzi

- Ignacio Joaquin Vigezzi

## Armado y verificación de cables Cat5/Cat5e bajo estándar T568A/B

En este laboratorio utilizamos 1 metro de cable UTP Cat5e y conectores RJ45 para construir un cable de conexión bajo la norma T568A/B en configuración DERECHO, no cruzado.

![cable](https://github.com/user-attachments/assets/f872875c-d989-4593-92b6-46a3762e0991)
<img width="756" height="614" alt="image" src="https://github.com/user-attachments/assets/901b0229-e956-4389-80ce-27444a43d840" />

Prestando especial atención al crimpado de los conectores y utilizando un tester para verificar que cada uno de los 8 conductores de los pares trenzados tenga una conexión adecuada en los extremos.

![conectores](https://github.com/user-attachments/assets/908bbc00-b2e4-40c0-807a-c69b38e6b7ab)

### Conexión

Para verificar el correcto funcionamiento del cable, utilizamos un switch para conectar 2 computadoras y comunicarnos con otro grupo. Definimos manualmente nuestra IP 192.168.1.1, el otro grupo estableció la dirección 192.168.1.2.
![ping](https://github.com/user-attachments/assets/dc087208-a7a2-4afd-94ea-74206e5db19b)

Mediante ping podemos ver que la conexión es exitosa y sin pérdida de paquetes, por lo tanto, el cable funciona correctamente.

---

## Parte 2: Equipamiento físico, verificación y utilización de equipos de red y análisis de tráfico.

En este parte el grupo no pudo conectarse al Switch “Cisco Catalyst 2950 Series”, ya que no contó con el tiempo suficiente. Sin embargo, se pudo conectar al router del aula.

El Switch Cisco Catalyst 2950 Series, según su datasheet, posee las siguientes especificaciones:

### Rendimiento

- 12 puertos 10/100 Mbps
- Ancho de banda de reenvío máximo: 4.8 Gbps.
- Tasa de reenvío: 3.6 Mpps (basada en paquetes de 64 bytes).
- Memoria: 16 MB de DRAM y 8 MB de memoria Flash.
- Búfer de paquetes: 8 MB de arquitectura compartida por todos los puertos.
- Tabla de direcciones MAC: COnfigurable hasta 8000 direcciones.

### Características físicas y eléctricas

- Dimensiones: 4,36 x 44,45 x 24,18 cm. Ocupa 1 RU (unidad de rack).
- Peso: 3 kg
- Consumo de energía: 30W máximo.
- Fuente de alimentación: Interna de rango automático (100 a 240 VAC) y conector para el sistema de alimentación redundante opcional Cisco RPS 675.

### Funcionalidades Destacadas

- Software: Equipados con el software Standard Image (SI), que ofrece funciones básicas de datos, voz y video.

- Seguridad: Soporte para IEEE 802.1x, seguridad de puerto basada en direcciones MAC y SSHv2 para sesiones de administración cifradas.

- Calidad de Servicio (QoS): Soporta cuatro colas de salida por puerto, clasificación basada en 802.1p (CoS) y algoritmos de programación Strict Priority y Weighted Round Robin (WRR).

- Gestión: Administrable vía Web (Cisco Device Manager), SNMP (v1, v2 y v3 no criptográfico) y CLI.

## Indicadores para conectarse al Switch Cisco a través de PUTTY

### Conectar una PC al puerto de consola del switch Cisco a 9600 baudios utilizando PUTTY.

- Abrir el programa PuTTY y seleccionar el tipo de conexión Serial.
- Serial Line: Ingresar el puerto (ejemplo: `COM1` en Windows o `/dev/ttyUSB0` en Linux).
- En Speed (Baudios), escribir `9600`.
- En Data bits: 8
- En Stop bits: 1
- En Parity: None
- Ir a la categoría _Connection_ -> _Serial_.
- Verificar que _Flow Control_ esté en `None`.
- Hacer clic en Open.
- Apretar Enter.

![](1.webp)
