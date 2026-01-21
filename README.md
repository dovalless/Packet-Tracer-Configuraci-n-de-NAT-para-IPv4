# 🌐 Packet Tracer: Configuración de NAT Dinámico y Estático para IPv4

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7.svg)](https://www.netacad.com/courses/packet-tracer)
[![NAT](https://img.shields.io/badge/Protocol-NAT/PAT-green.svg)](https://www.cisco.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

<div align="center">

**Laboratorio de Redes: Traducción de Direcciones de Red (NAT)**

📊 [Tabla de Direcciones](#-tabla-de-asignación-de-direcciones) • ⚙️ [Configuración](#-configuración-paso-a-paso-router-r2) • 🔍 [Verificación](#-comandos-de-verificación) • 👨‍💻 [Autor](#-autor)

</div>

---

## 📋 Descripción del Proyecto
Este proyecto consiste en la implementación y configuración de **Network Address Translation (NAT)** en un entorno de red empresarial simulado en Cisco Packet Tracer. El objetivo principal es configurar el router **R2** para permitir que tres redes LAN privadas accedan a Internet de forma segura y eficiente, optimizando el uso de direcciones IP públicas.



### 🎯 Objetivos Técnicos
* **NAT Dinámico con PAT (Overload):** Traducir las direcciones de las tres LAN internas (LAN1, LAN2, LAN3) a una única dirección pública global.
* **NAT Estático:** Mapear un servidor interno (`local.pka`) a una dirección pública específica para permitir el acceso externo.
* **Control de Tráfico:** Utilizar Listas de Control de Acceso (ACL) para definir qué redes participan en el proceso de traducción.

---

## 🛠️ Tecnologías y Herramientas

### Stack Tecnológico
* **Simulador:** Cisco Packet Tracer v8.x
* **Protocolos:** IPv4, NAT, PAT, Static Routing.
* **Seguridad:** Access Control Lists (ACL).

### Conceptos Clave Implementados
| Técnica | Propósito |
| :--- | :--- |
| **NAT Dinámico (PAT)** | Ahorro de IPs públicas mapeando múltiples hosts internos a una sola IP mediante puertos. |
| **NAT Estático** | Acceso persistente desde el exterior a servidores internos (1:1). |
| **Inside/Outside** | Definición de dominios de confianza y dominios públicos en el router. |
| **ACL Estándar** | Filtrado e identificación de tráfico interesante para traducción. |

---

## 📊 Tabla de Asignación de Direcciones

| Dispositivo | Interfaz | Dirección IP | Máscara | Rol |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | S0/0/0 | 10.1.1.1 | /30 | Router Interno |
| **R2** | S0/1/0 | 209.165.200.225 | /27 | **Gateway (NAT)** |
| **R2** | F0/0/0 | 192.168.20.1 | /24 | LAN 2 Gateway |
| **R3** | F0/0 | 192.168.30.1 | /24 | LAN 3 Gateway |
| **local.pka**| NIC | 192.168.20.254 | /24 | Servidor Interno |

---

## ⚙️ Configuración Paso a Paso (Router R2)

A continuación, se detalla la lógica de comandos aplicada en el router central de borde (**R2**):

### 1️⃣ Definición de la ACL (Identificación de Redes)
Se crea una ACL estándar para identificar el tráfico de las tres LANs que tienen permitido salir a internet.
```cisco
ip access-list standard R2NAT
 permit 192.168.10.0 0.0.0.255
 permit 192.168.20.0 0.0.0.255
 permit 192.168.30.0 0.0.0.255
2️⃣ Configuración del Pool y PAT
Definimos el espacio de IPs públicas y activamos la sobrecarga (Overload).

Cisco CLI

ip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
ip nat inside source list R2NAT pool R2POOL overload
3️⃣ NAT Estático para el Servidor
Mapeo dedicado para que el servidor sea accesible externamente.

Cisco CLI

ip nat inside source static 192.168.20.254 209.165.202.130
4️⃣ Definición de Interfaces (Inside/Outside)
Es crucial definir el flujo del tráfico para que el proceso NAT sepa dónde traducir.

Cisco CLI

interface Serial0/0/0
 ip nat inside
interface Serial0/0/1
 ip nat inside
interface FastEthernet0/0/0
 ip nat inside
interface Serial0/1/0
 ip nat outside
🔍 Comandos de Verificación
Para validar que la traducción está funcionando correctamente, utiliza los siguientes comandos en R2:

show ip nat translations: Visualiza la tabla activa de mapeos entre IPs privadas y públicas.

show ip nat statistics: Revisa el contador de éxitos (hits), el pool activo y las interfaces configuradas.

clear ip nat translation *: Limpia la tabla de traducciones para realizar nuevas pruebas.

👨‍💻 Autor
<div align="center">

Darwin Manuel Ovalles Cesar

<p align="center"> <a href="https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/" target="_blank"> <img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="LinkedIn - Darwin Ovalles" height="40" width="50" /> </a> </p>

💼 LinkedIn: darwin-manuel-ovalles-cesar-dev

🌐 GitHub: @dovalless

"Este proyecto es una muestra de mis habilidades técnicas en el área de redes y telecomunicaciones, enfocado en la administración de dispositivos Cisco y la optimización de recursos IP."

#CiscoPacketTracer #Networking #NAT #CCNA

</div>

📄 Licencia
Este proyecto está bajo la Licencia MIT. Consulta el archivo LICENSE para más detalles.

<div align="center">

⭐ Si este laboratorio te resultó útil, considera darle una estrella en GitHub ⭐

🚀 ¡Feliz configuración! 🚀

Desarrollado con 💚 y ☕ por Darwin Ovalles

</div>


¿Necesitas que ajuste algún detalle de las interfaces o el direccionamiento antes de que lo publiques?
