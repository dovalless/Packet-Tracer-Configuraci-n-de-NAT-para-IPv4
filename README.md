Markdown# 🌐 Packet Tracer: Configuración de NAT Dinámico y Estático para IPv4

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7.svg)](https://www.netacad.com/courses/packet-tracer)
[![NAT](https://img.shields.io/badge/Protocol-NAT/PAT-green.svg)](https://www.cisco.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

<div align="center">

**Laboratorio de Redes: Traducción de Direcciones de Red (NAT)**

[📋 Tabla de Direcciones](#-tabla-de-asignación-de-direcciones) • 
[⚙️ Configuración](#️-configuración-paso-a-paso) • 
[🔍 Verificación](#-comandos-de-verificación) • 
[👨‍💻 Autor](#-autor)

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

### Stack de Redes
# Infraestructura
Cisco Packet Tracer >= 8.0
Protocolos IPv4 / Enrutamiento Estático

# Seguridad y NAT
NAT Estático (Inside Source Static)
PAT (Port Address Translation / Overload)
ACLs Estándar (Access Control Lists)

# Hardware Simulado
Routers Cisco ISR 1941 / 2911
Switches Cisco Catalyst 2960

---

## 📊 Tabla de Asignación de Direcciones

| Dispositivo | Interfaz | Dirección IP | Máscara |
| :--- | :--- | :--- | :--- |
| **R1** | S0/0/0 | 10.1.1.1 | /30 |
| **R1** | F0/0 | 192.168.10.1 | /24 |
| **R2** | S0/0/0 | 10.1.1.2 | /30 |
| **R2** | S0/1/0 | 209.165.200.225 | /27 |
| **R3** | F0/0 | 192.168.30.1 | /24 |
| **PC1** | NIC | 192.168.10.10 | /24 |
| **local.pka**| NIC | 192.168.20.254 | /24 |

---

## 📈 Metodología de Implementación

### Pipeline de Configuración en R2
📥 **Paso 1:** Definición de ACL (Identificación de tráfico interno)
    ↓
🌐 **Paso 2:** Creación del Pool de Direcciones Públicas
    ↓
⚙️ **Paso 3:** Configuración de NAT Dinámico (Overload)
    ↓
🔒 **Paso 4:** Mapeo de NAT Estático para Servidor
    ↓
🔌 **Paso 5:** Asignación de Interfaces (Inside vs Outside)

---

## ⚙️ Configuración Paso a Paso (Router R2)

### 1️⃣ Identificación de Redes (ACL)
Se crea una ACL estándar para permitir el tráfico de las tres subredes internas.
```cisco
ip access-list standard R2NAT
 permit 192.168.10.0 0.0.0.255
 permit 192.168.20.0 0.0.0.255
 permit 192.168.30.0 0.0.0.255
2️⃣ Pool y NAT Dinámico con PATDefinimos el espacio de direcciones públicas y aplicamos overload para compartir la IP.Cisco CLIip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
ip nat inside source list R2NAT pool R2POOL overload
3️⃣ NAT Estático para el ServidorConfiguramos el acceso permanente al servidor desde la red externa.Cisco CLIip nat inside source static 192.168.20.254 209.165.202.130
4️⃣ Roles de InterfazCisco CLIinterface Serial0/0/0
 ip nat inside
interface Serial0/1/0
 ip nat outside
🔍 Comandos de VerificaciónPara asegurar la integridad de la red, utilizamos los siguientes comandos de diagnóstico:ComandoPropósitoshow ip nat translationsVisualiza la tabla activa de mapeos IP.show ip nat statisticsRevisa el contador de paquetes traducidos y errores.debug ip natMonitoreo en tiempo real de las traducciones (uso controlado).💡 Conceptos Clave Aprendidos✅ Eficiencia IP: Cómo PAT permite que cientos de dispositivos naveguen con una sola IP pública.✅ Seguridad Perimetral: NAT actúa como una primera capa de seguridad al ocultar las IPs privadas reales.✅ Publicación de Servicios: El uso de NAT Estático para hacer que recursos internos (servidores) sean accesibles mundialmente.👨‍💻 Autor<div align="center">Darwin Manuel Ovalles Cesar<p align="center"><a href="https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/" target="_blank"><img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="LinkedIn - Darwin Ovalles" height="40" width="50" /></a></p>💼 LinkedIn: darwin-manuel-ovalles-cesar-dev🌐 GitHub: @dovalless"Este proyecto es una muestra de mis habilidades técnicas en el área de redes y telecomunicaciones, enfocado en la administración de dispositivos Cisco y la optimización de recursos IP."#CiscoPacketTracer #Networking #NAT #CCNA</div><div align="center">⭐ Si este laboratorio te resultó útil, considera darle una estrella en GitHub ⭐🚀 ¡Feliz configuración de redes! 🚀</div>
