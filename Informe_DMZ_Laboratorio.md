# Informe Técnico – Implementación de DMZ

## 1. Objetivo
El objetivo de este laboratorio fue diseñar e implementar una zona desmilitarizada (DMZ) segura utilizando direccionamiento IP, NAT estático y listas de control de acceso (ACLs).

## 2. Topología de Red
La red está compuesta por tres segmentos:

- LAN Interna: 192.168.1.0/24
- DMZ: 192.168.2.0/24
- Red Externa: 192.168.3.0/24

Dispositivos utilizados:
- Router_FW
- PC_Internal
- Server_Web_DMZ
- PC_External
- Switches

## 3. Configuración IP

### PC_Internal
- IP: 192.168.1.10
- Gateway: 192.168.1.1

### Server_DMZ
- IP: 192.168.2.10
- Gateway: 192.168.2.1

### PC_External
- IP: 192.168.3.10
- Gateway: 192.168.3.1


## 4. Configuración del Router

Las interfaces fueron configuradas de la siguiente manera:

- GigabitEthernet0/0 → 192.168.1.1
- GigabitEthernet0/1 → 192.168.2.1
- GigabitEthernet0/2 → 192.168.3.1


## 5. Configuración de NAT

Se implementó NAT estático para permitir el acceso al servidor web desde la red externa:
_ip nat inside source static 192.168.2.10 192.168.3.1_


## 6. Configuración del Servidor

Se activaron los siguientes servicios en el servidor DMZ:

- HTTP: ON
- HTTPS: ON

Además, se modificó el archivo index.html para mostrar un mensaje personalizado.


## 7. Configuración de ACLs

### ACL Externa (Internet → DMZ)

Se configuró una ACL para permitir únicamente tráfico HTTP desde cualquier origen hacia la IP pública del servidor:
_access-list 100 permit tcp any host 192.168.3.1 eq 80_
aplicada en la interface:
_interface GigabitEthernet0/2_
_ip access-group 100 in_


### ACL DMZ → LAN

Se configuró una ACL para bloquear completamente el acceso desde la DMZ hacia la red interna:
_access-list 101 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
access-list 101 permit ip any any_
aplicada en la interface:
_interface GigabitEthernet0/1
ip access-group 101 in_


## 8. Pruebas Realizadas

Se realizaron las siguientes pruebas para verificar el funcionamiento:

### Desde PC_External
- Acceso web al servidor: exitoso
- Ping al servidor: bloqueado

### Desde PC_Internal
- Acceso web al servidor DMZ: probado(request timeout)

### Desde Server_DMZ
- Ping hacia la red interna: bloqueado


## 9. Conclusión

Se logró implementar una DMZ funcional que permite el acceso controlado desde Internet al servidor web, mientras se protege la red interna mediante el uso de ACLs y NAT.

