Explicación del laboratorio

Configuración de FortiGate

<img width="1919" height="1022" alt="Screenshot 2026-09-25 224400" src="https://github.com/user-attachments/assets/98829282-5122-4195-b44a-4709b4184b6a" />






<br>

Para entrar a la interfaz gráfica es necesario utilizar el la url http://192.168.1.12, como se había mencionado anteriormente. La contraseña para el usuario
admin configurada en el equipo es "clase1234".
<br>

<img width="1919" height="1029" alt="Screenshot 2026-09-25 222827" src="https://github.com/user-attachments/assets/20f17b9b-c25d-4a20-b70a-135f4fb87fb8" />

<br>
Desde la interfaz gráfica de FortiGate se crearon las interfaces VLAN necesarias para dividir la red interna de la topología. Todas fueron configuradas sobre port2, que funciona como enlace trunk hacia el switch.

La interfaz VLAN10-USERS utiliza el VLAN ID 10 y tiene la dirección 25.8.4.1/28. Esta interfaz corresponde a la red de usuarios.

La interfaz VLAN20-WEB utiliza el VLAN ID 20 y tiene la dirección 25.8.4.17/28. Esta interfaz corresponde a la red donde se encuentra el servidor WEB.

La interfaz VLAN30-DB utiliza el VLAN ID 30 y tiene la dirección 25.8.4.33/28. Esta interfaz corresponde a la red del servidor de base de datos.

En las tres interfaces VLAN se habilitó PING dentro de los Administrative Access. Esto se realizó para poder utilizar pruebas de conectividad mediante ICMP y verificar que la comunicación entre el FortiGate, el switch y los diferentes dispositivos de la topología funcionara correctamente.

El acceso administrativo habilitado mediante PING no significa que los dispositivos tengan acceso completo a la administración del FortiGate; únicamente permite responder a solicitudes ICMP para realizar las pruebas de conectividad.
<br>


<img width="1919" height="1029" alt="Screenshot 2026-09-25 222941" src="https://github.com/user-attachments/assets/a92ed5d2-f7e8-4bec-a063-c930475390a2" />

<br>
Se configuró una ruta estática hacia 192.168.1.1, que corresponde al gateway de la red externa conectada al port1 del FortiGate. Esta ruta permite que el FortiGate sepa hacia dónde enviar el tráfico destinado a redes que no pertenecen directamente a sus interfaces configuradas.

La ruta utiliza 0.0.0.0/0, por lo que funciona como una ruta predeterminada. De esta manera, cuando los dispositivos de las VLAN internas necesitan comunicarse con redes externas, el tráfico es enviado desde el FortiGate hacia 192.168.1.1 a través de port1.

Esta configuración es necesaria para que las redes internas puedan tener salida hacia el exterior y para que el FortiGate tenga definido el siguiente salto para el tráfico que no conoce directamente.

<br>
