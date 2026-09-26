**Explicación del laboratorio
**


**Switch**


**Creación de lasvlan**

Decidí crear tres vlan para separar los dispositivos según la función que cumplen dentro de la topología. De esta forma, no todos quedan en la misma red y puedo controlar mejor qué comunicación puede tener cada uno.

La VLAN 10 es para los usuarios, ya que desde ahí se realizan las conexiones a los servicios. La VLAN 20 es para el servidor WEB, que necesita recibir conexiones de los usuarios. Por último, la VLAN 30 es para el servidor de base de datos, ya que es un servicio que debe estar más protegido y no necesita estar disponible directamente para los usuarios.

Separarlos de esta manera también me permite crear reglas más específicas en el FortiGate. Por ejemplo, permití que el servidor WEB se comunique con la base de datos por MySQL, pero bloqueé ese mismo acceso directamente desde los usuarios.




**Configuración de FortiGate**
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
Se configuró una ruta estática hacia 192.168.1.1,que es el gateway de Cloud
 conectada al port1 del FortiGate. Esta ruta permite que el FortiGate sepa hacia dónde enviar el tráfico destinado a redes que no pertenecen directamente a sus interfaces configuradas.

La ruta utiliza 0.0.0.0/0, por lo que funciona como una ruta predeterminada. De esta manera, cuando los dispositivos de las VLAN internas necesitan comunicarse con redes externas, el tráfico es enviado desde el FortiGate hacia 192.168.1.1 a través de port1.

Esta configuración es necesaria para que las redes internas puedan tener salida hacia el exterior y para que el FortiGate tenga definido el siguiente salto para el tráfico que no conoce directamente.

<br>

<img width="1919" height="1031" alt="Screenshot 2026-09-25 223121" src="https://github.com/user-attachments/assets/f0deb89d-5ceb-49b6-bee3-8fc06da78efb" />

Las Firewall Policies se configuraron en el FortiGate para controlar el tráfico entre las diferentes redes de la topología. Estas políticas permiten definir qué comunicación está permitida y cuál debe ser bloqueada, tomando en cuenta el origen, destino, servicio y dirección de la comunicación.

Se creó una política para permitir que los usuarios de la VLAN 10 tengan salida hacia Internet mediante la interfaz port1, utilizando NAT para realizar la traducción de las direcciones privadas.

También se configuró una política para permitir la comunicación de los usuarios con el servidor WEB de la VLAN 20 mediante HTTPS. En esta política se aplicaron perfiles de seguridad como IPS, Deep Inspection y File Filter, con el objetivo de analizar y controlar el tráfico que llega al servidor.

Para la comunicación con la VLAN 30, se configuró una política que bloquea a los usuarios el acceso al servicio MySQL mediante el puerto 3306 del servidor de base de datos.

Por otro lado, se permitió que el servidor WEB pueda comunicarse con el servidor de base de datos mediante MySQL (TCP/3306), ya que esta comunicación es necesaria para que una aplicación web pueda utilizar la base de datos. También se mantiene una política posterior que bloquea otros tipos de tráfico entre ambas redes.

Las políticas se organizan en un orden específico, ya que el FortiGate evalúa las reglas de arriba hacia abajo. Por esta razón, las reglas que permiten un servicio específico se colocan antes de las reglas generales de bloqueo.


<img width="1919" height="1029" alt="Screenshot 2026-09-25 223245" src="https://github.com/user-attachments/assets/66e06aae-44b9-4de3-b4c9-ae53576e1c06" />


Se configuró una firma de IPS (Intrusion Prevention System) llamada HTTP.URI.SQL.Injection, utilizada para detectar intentos de inyección SQL realizados a través de la URL de una solicitud HTTP.

La firma fue habilitada dentro del sensor de IPS protect_http_server y se configuró con la acción Block, por lo que cuando FortiGate identifica tráfico que coincide con esta firma, bloquea la solicitud para evitar que llegue al servidor WEB.

Esta firma se aplicó a la política de comunicación entre los usuarios de la VLAN 10 y el servidor WEB de la VLAN 20, lo que hace que el tráfico HTTPS sea inspeccionado en busca de este tipo de ataque.


<img width="1919" height="1031" alt="Screenshot 2026-09-25 223418" src="https://github.com/user-attachments/assets/238a6755-e672-4fc0-a52c-20444602088c" />

Se creó un Traffic Shaper para limitar el ancho de banda de los usuarios cuando acceden al servidor WEB. Se configuró Limit-Users-WEB como un Per IP Shaper, con un límite de 1024 kbps por usuario.

Esta configuración es lo que evita que los usuarios consuman demasiado ancho de banda y afecte a los demás. Se aplicó al tráfico HTTP y HTTPS de los usuarios de la VLAN 10 hacia el servidor WEB de la VLAN 20


<img width="1919" height="1031" alt="Screenshot 2026-09-25 223530" src="https://github.com/user-attachments/assets/87ff8105-25fa-4b2c-be7c-f72da2cb5e83" />

Aquí se puede visualizar la política de traffic shaping ya aplicada con el perfil anteriormente creado.

<img width="1919" height="1030" alt="Screenshot 2026-09-25 223620" src="https://github.com/user-attachments/assets/fa19f171-0bd7-4c7f-a8cf-9fd26b16cef5" />

Se creó el filtro NoEjecutables para bloquear archivos ejecutables .exe. Este filtro se aplicó a la política de comunicación entre los usuarios y el servidor WEB.

Con esto, cuando un usuario intente enviar un archivo ejecutable mediante esta conexión, FortiGate lo identifica y lo bloquea. Este perfil de seguridad se puede visualizar ya entre las imágenes de arriba, en la seccióde Firewall Policies.



