Rossanna Desangles De Salas

2025-0804

Link video explicativo: https://youtu.be/TUKTnvE3BAk


El objetivo principal de este proyecto es armar una red de computadoras que sea segura y que esté dividida en dos partes usando un firewall FortiGate. Para que el laboratorio sea único y auténtico, 
todas las direcciones IP se calcularon usando los números de mi matrícula estudiantil, que es la 2025-0804. El sistema trabaja con la IP base 25.8.4.0. A excepción del port1 del FortiGate que tiene
salida hacia cloud, en este puerto se encuentra una IP colacada mediante el protocolo de dhcp con la red 192.168.1.0/24.

Para esta topología, debido a su sencillez, no posee una estructura compleja. Consiste en un Cloud conectado mediante el port1 del FortiGate 7.0.9 con la IP 192.168.1.12, IP que es utilizada para accedeer
a la GUI del equipo. Luego se utilizó un Switch Cisco vIOS Switch como dispostivo intermediaro, el cual separa los equipo en 3 vlans diferentes (más adelante se mostrará un desglose de las redes). Por último,
debido a la naturaleza del servidor web, en lugar de utilizar una PC virtual, opté por utlizar una máquina con Rocky Linux 10, para mejor demostración de ataque de este.




<img width="1203" height="646" alt="Screenshot 2026-09-25 215637" src="https://github.com/user-attachments/assets/3900cd07-a76d-450b-a3b0-bf6e29cd9acf" />





**Desglose de Direcciones IP con sus respectivos puertos**

**FortiGate port1:** 192.168.1.12/24, Gateway 192.168.1.1

**FortiGate port2:** Conexión con Switch

**vlan 10 users:** Red 25.8.4.0/28, Gateway 25.8.4.1, Users 25.8.4.2/28

**vlan 20 Web**: Red 25.8.4.16/28, Gateway 25.8.4.17, WEB-Server 25.8.4.18/28

**vlan 30 DB**: Red 25.8.4.32/28, Gateway 25.8.4.33, DB-Server 25.8.4.34/28

**Switch Gi0/0**: FortiGate port2, Troncal, permitidas las vlan 10, vlan 20, vlan 30

**Switch Gi0/1:** DB-Server, vlan 30

**Switch Gi0/2:** WEB-Server, vlan 20

**Switch Gi0/3:** Users, vlan 10
