```markdown
# 🌐 Packet Tracer: Configuración de NAT para IPv4

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7.svg)](https://www.netacad.com/courses/packet-tracer)
[![NAT](https://img.shields.io/badge/Protocol-NAT-green.svg)](https://www.cisco.com/c/en/us/support/docs/ip/network-address-translation-nat/26704-nat-faq-00.html)
[![IPv4](https://img.shields.io/badge/IP-IPv4-blue.svg)](https://es.wikipedia.org/wiki/IPv4)
[![Cisco IOS](https://img.shields.io/badge/Cisco-IOS-orange.svg)](https://www.cisco.com/c/en/us/products/ios-nx-os-software/ios-technologies/index.html)
[![License](https://img.shields.io/badge/License-Educational-yellow.svg)](LICENSE)

<div align="center">

**Laboratorio Práctico de Network Address Translation (NAT) y Port Address Translation (PAT)**

[📋 Tabla de Direcciones](#-tabla-de-asignación-de-direcciones) •
[🎯 Objetivos](#-objetivos-del-proyecto) •
[🚀 Configuración](#️-configuración-paso-a-paso) •
[🔍 Verificación](#-verificación-y-pruebas) •
[📚 Recursos](#-recursos-y-referencias)

</div>

---

## 📋 Descripción del Proyecto

Este proyecto implementa una solución completa de **Network Address Translation (NAT)** en un entorno empresarial simulado con Cisco Packet Tracer. La configuración aborda uno de los problemas más comunes en redes corporativas: permitir que múltiples dispositivos en redes privadas accedan a Internet utilizando un número limitado de direcciones IP públicas.

### 🔍 ¿Qué es NAT y por qué es importante?

**NAT (Network Address Translation)** es una tecnología fundamental que traduce direcciones IP privadas a direcciones IP públicas. En este laboratorio se implementan dos tipos de NAT:

- **NAT Dinámico con PAT (Port Address Translation)**: Conocido también como "NAT Overload", permite que cientos o miles de dispositivos internos compartan una única dirección IP pública mediante el uso de diferentes puertos. Esto soluciona la escasez de direcciones IPv4 públicas y añade una capa de seguridad al ocultar la topología interna de la red.

- **NAT Estático**: Crea un mapeo permanente uno a uno entre una dirección IP privada y una pública. Es ideal para servidores internos que deben ser accesibles desde Internet con una dirección IP pública constante.

### 🏢 Escenario del Laboratorio

Una empresa cuenta con tres redes LAN internas (LAN1, LAN2 y LAN3) que necesitan acceso a Internet, además de un servidor interno que debe ser accesible desde el exterior. El router **R2** actúa como punto central de traducción NAT, permitiendo:

- ✅ Que todos los dispositivos de las 3 LANs accedan a Internet compartiendo una sola IP pública
- ✅ Que el servidor interno sea accesible desde Internet con una IP pública dedicada
- ✅ Conservación eficiente del espacio limitado de direcciones IPv4 públicas
- ✅ Mayor seguridad al ocultar las direcciones internas de la organización

---

## 🎯 Objetivos del Proyecto

### Objetivos Técnicos

- 🔧 **Configurar NAT Dinámico con PAT**: Implementar traducción de direcciones para múltiples hosts usando overload
- 🔒 **Configurar NAT Estático**: Establecer mapeo permanente para servidor interno
- 📝 **Crear ACL Nombrada**: Implementar listas de control de acceso para definir tráfico NAT
- 🌊 **Configurar Pool de Direcciones**: Administrar el conjunto de IPs públicas disponibles
- ✅ **Validar Conectividad**: Verificar comunicación bidireccional entre redes internas e Internet

### Objetivos de Aprendizaje

- Comprender el funcionamiento de NAT y PAT en entornos reales
- Dominar la configuración de Cisco IOS para servicios de traducción de direcciones
- Aplicar mejores prácticas en el diseño de redes empresariales
- Desarrollar habilidades de troubleshooting en problemas de conectividad NAT

---

## 📊 Tabla de Asignación de Direcciones

### 🔹 Dispositivos de Red - Routers

| Dispositivo | Interfaz | Dirección IP | Máscara | Tipo de Red |
|------------|----------|--------------|---------|-------------|
| **R1** | S0/0/0 | 10.1.1.1 | /30 | WAN (Enlace R1-R2) |
| **R1** | F0/0 | 192.168.10.1 | /24 | LAN1 (Red Interna 1) |
| **R2** | S0/0/0 | 10.1.1.2 | /30 | WAN (Conexión a R1) |
| **R2** | S0/0/1 | 10.2.2.1 | /30 | WAN (Conexión a R3) |
| **R2** | S0/1/0 | 209.165.200.225 | /27 | Internet (Enlace ISP) |
| **R2** | F0/0/0 | 192.168.20.1 | /24 | LAN2 (Red del Servidor) |
| **R3** | S0/0/1 | 10.2.2.2 | /30 | WAN (Enlace R3-R2) |
| **R3** | F0/0 | 192.168.30.1 | /24 | LAN3 (Red Interna 3) |

### 🔹 Dispositivos Finales

| Dispositivo | IP | Máscara | Gateway | Descripción |
|------------|-----|---------|---------|-------------|
| **PC1** | 192.168.10.10 | /24 | 192.168.10.1 | Cliente en LAN1 |
| **PC2** | 192.168.30.10 | /24 | 192.168.30.1 | Cliente en LAN3 |
| **local.pka** | 192.168.20.254 | /24 | 192.168.20.1 | Servidor Web/DNS Interno |
| **PC exterior** | 209.165.201.14 | /28 | - | Cliente externo (Internet) |
| **cisco.pka** | 209.165.201.30 | /28 | - | Servidor público (Internet) |

### 🔹 Pool de Direcciones NAT

| Propósito | Dirección IP | Red CIDR | Uso |
|-----------|--------------|----------|-----|
| **NAT Dinámico (PAT)** | 209.165.202.129 | 209.165.202.128/30 | Primera dirección - Overload para todas las LANs |
| **NAT Estático** | 209.165.202.130 | 209.165.202.128/30 | Segunda dirección - Servidor local.pka |

**Nota**: La red 209.165.202.128/30 proporciona 2 direcciones IP utilizables (la .129 y .130), perfectas para nuestros dos propósitos.

---

## 🛠️ Tecnologías y Conceptos

### Stack Tecnológico

```
🖥️ Simulador de Red
   └─ Cisco Packet Tracer 7.3+

🌐 Protocolos y Tecnologías
   ├─ IPv4 Addressing & Subnetting
   ├─ NAT (Network Address Translation)
   ├─ PAT (Port Address Translation)
   ├─ ACL (Access Control Lists)
   ├─ Static Routing
   └─ ICMP Protocol

🔧 Equipamiento
   └─ Cisco Routers (2900 Series - IOS)
```

### Conceptos de NAT Implementados

| Tecnología | Descripción | Aplicación en este Lab |
|------------|-------------|------------------------|
| **NAT Dinámico con PAT** | Múltiples IPs internas → Una IP pública usando puertos diferentes | Acceso a Internet para LAN1, LAN2, LAN3 |
| **NAT Estático** | Mapeo permanente 1:1 entre IP privada y pública | Servidor local.pka accesible desde Internet |
| **Inside Interface** | Interfaz conectada a la red interna privada | S0/0/0, S0/0/1, F0/0 en R2 |
| **Outside Interface** | Interfaz conectada a la red externa pública | S0/1/0 en R2 |
| **ACL Nombrada** | Lista de control que define qué tráfico se traduce | R2NAT (permite 3 LANs) |
| **NAT Pool** | Conjunto de direcciones IP públicas para traducción | R2POOL (209.165.202.129) |

### Terminología NAT

```
┌─────────────────────────────────────────────────────────────┐
│  Inside Local:  IP privada del host interno (192.168.x.x)  │
│  Inside Global: IP pública que representa al host interno   │
│  Outside Local: IP del host externo vista desde dentro      │
│  Outside Global: IP real del host en Internet               │
└─────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Configuración Paso a Paso

### 🎯 Instrucciones Generales

En esta actividad se configurará **únicamente el router R2**, que actuará como gateway NAT para todas las redes internas. Siga cuidadosamente cada paso respetando los nombres exactos especificados.

---

### 📍 Fase 1: Crear Access Control List (ACL) Nombrada

Las ACLs determinan qué tráfico será traducido por NAT. En este caso, permitiremos que las tres redes LAN internas sean traducidas.

```cisco
! Acceder al modo de configuración global
R2> enable
R2# configure terminal

! Crear ACL estándar nombrada "R2NAT"
! ⚠️ IMPORTANTE: El nombre debe ser exactamente "R2NAT" (case-sensitive)
R2(config)# ip access-list standard R2NAT

! Permitir traducción de LAN1 (192.168.10.0/24)
R2(config-std-nacl)# permit 192.168.10.0 0.0.0.255

! Permitir traducción de LAN2 (192.168.20.0/24)
R2(config-std-nacl)# permit 192.168.20.0 0.0.0.255

! Permitir traducción de LAN3 (192.168.30.0/24)
R2(config-std-nacl)# permit 192.168.30.0 0.0.0.255

! Salir del modo de configuración de ACL
R2(config-std-nacl)# exit
```

**🔍 Explicación**:
- `ip access-list standard R2NAT`: Crea una ACL estándar con nombre descriptivo
- `permit 192.168.x.0 0.0.0.255`: La wildcard mask 0.0.0.255 equivale a /24 (coincide con los primeros 3 octetos)
- **Orden crítico**: Deben especificarse en orden LAN1 → LAN2 → LAN3

---

### 📍 Fase 2: Crear Pool de Direcciones NAT

El pool NAT define el conjunto de direcciones IP públicas disponibles para la traducción dinámica.

```cisco
! Crear pool NAT llamado "R2POOL"
! ⚠️ IMPORTANTE: El nombre debe ser exactamente "R2POOL" (case-sensitive)
R2(config)# ip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
```

**🔍 Desglose del comando**:

```
ip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
│            │      │                │                │
│            │      │                │                └─ Máscara de subred (/30)
│            │      │                └─ IP final del rango (misma = un solo IP)
│            │      └─ IP inicial del rango
│            └─ Nombre del pool
└─ Comando de creación
```

**📊 Análisis del espacio de direcciones 209.165.202.128/30**:

| Elemento | Dirección IP | Uso |
|----------|--------------|-----|
| Dirección de red | 209.165.202.128 | No utilizable |
| Primera IP utilizable | **209.165.202.129** | **NAT Dinámico (Pool)** |
| Segunda IP utilizable | **209.165.202.130** | **NAT Estático (Servidor)** |
| Broadcast | 209.165.202.131 | No utilizable |

---

### 📍 Fase 3: Configurar NAT Dinámico con PAT (Overload)

Este comando asocia la ACL con el pool y habilita PAT mediante la palabra clave `overload`.

```cisco
! Configurar NAT dinámico con PAT
R2(config)# ip nat inside source list R2NAT pool R2POOL overload
```

**🔍 Explicación de parámetros**:

| Parámetro | Función |
|-----------|---------|
| `ip nat inside source` | Define traducción para tráfico originado dentro de la red |
| `list R2NAT` | Usa la ACL "R2NAT" para identificar qué hosts traducir |
| `pool R2POOL` | Usa el pool "R2POOL" como fuente de IPs públicas |
| `overload` | **Habilita PAT**: permite múltiples hosts en una sola IP usando puertos |

**💡 ¿Cómo funciona el Overload (PAT)?**

```
Host Interno              Puerto    →    IP Pública Compartida
────────────────────────────────────────────────────────────────
192.168.10.10:3500        3500     →    209.165.202.129:3500
192.168.20.50:3501        3501     →    209.165.202.129:3501
192.168.30.10:3502        3502     →    209.165.202.129:3502
192.168.10.15:3503        3503     →    209.165.202.129:3503
```

Todos los dispositivos comparten la misma IP pública (209.165.202.129) pero R2 diferencia las conexiones por el número de puerto.

---

### 📍 Fase 4: Configurar NAT Estático para el Servidor

NAT estático crea un mapeo permanente para que el servidor interno sea accesible desde Internet.

```cisco
! Mapear servidor interno a IP pública
! Inside Local: 192.168.20.254 (local.pka)
! Inside Global: 209.165.202.130 (IP pública del servidor)
R2(config)# ip nat inside source static 192.168.20.254 209.165.202.130
```

**🔍 Análisis del mapeo**:

```
ip nat inside source static 192.168.20.254 209.165.202.130
                             │               │
                             │               └─ IP Pública (Inside Global)
                             └─ IP Privada del Servidor (Inside Local)
```

**📝 Características del NAT Estático**:
- ✅ Mapeo **permanente** (activo incluso sin tráfico)
- ✅ Siempre usa la **misma IP pública**
- ✅ Permite que hosts externos inicien conexiones hacia el servidor
- ✅ Ideal para servidores web, email, DNS, etc.

---

### 📍 Fase 5: Configurar Interfaces Inside y Outside

NAT necesita saber qué interfaces conectan a redes internas (inside) y cuál conecta a Internet (outside).

```cisco
! ═══════════════════════════════════════
! INTERFACES INSIDE (Redes Internas)
! ═══════════════════════════════════════

! Serial0/0/0 - Conexión a R1 (conduce tráfico de LAN1)
R2(config)# interface Serial0/0/0
R2(config-if)# ip nat inside
R2(config-if)# exit

! Serial0/0/1 - Conexión a R3 (conduce tráfico de LAN3)
R2(config)# interface Serial0/0/1
R2(config-if)# ip nat inside
R2(config-if)# exit

! FastEthernet0/0 - LAN2 directamente conectada (red del servidor)
R2(config)# interface FastEthernet0/0
R2(config-if)# ip nat inside
R2(config-if)# exit

! ═══════════════════════════════════════
! INTERFAZ OUTSIDE (Red Externa)
! ═══════════════════════════════════════

! Serial0/1/0 - Conexión a Internet (ISP)
R2(config)# interface Serial0/1/0
R2(config-if)# ip nat outside
R2(config-if)# exit

! ═══════════════════════════════════════
! GUARDAR CONFIGURACIÓN
! ═══════════════════════════════════════

R2(config)# exit
R2# write memory
! o alternativamente:
! R2# copy running-config startup-config
```

**🗺️ Diagrama de Interfaces**:

```
        INSIDE                      R2                      OUTSIDE
┌───────────────────┐     ┌─────────────────┐     ┌────────────────┐
│  LAN1             │     │                 │     │                │
│  192.168.10.0/24  ├─────┤ S0/0/0 (inside) │     │                │
│                   │     │                 │     │                │
│  LAN2             │     │                 │     │   Internet     │
│  192.168.20.0/24  ├─────┤ F0/0 (inside)   │     │                │
│  (Servidor)       │     │                 │     │ S0/1/0         │
│                   │     │                 ├─────┤ (outside)      │
│  LAN3             │     │ S0/0/1 (inside) │     │                │
│  192.168.30.0/24  ├─────┤                 │     │ 209.165.200.0  │
│                   │     │                 │     │                │
└───────────────────┘     └─────────────────┘     └────────────────┘
```

---

### 📋 Configuración Completa - Bloque Unificado

```cisco
!
! ═══════════════════════════════════════════════════════════════
! CONFIGURACIÓN COMPLETA DE NAT EN ROUTER R2
! Laboratorio: Configure NAT para IPv4
! ═══════════════════════════════════════════════════════════════
!
enable
configure terminal
!
! ───────────────────────────────────────────────────────────────
! PASO 1: Crear ACL Nombrada para definir tráfico NAT
! ───────────────────────────────────────────────────────────────
ip access-list standard R2NAT
 permit 192.168.10.0 0.0.0.255
 permit 192.168.20.0 0.0.0.255
 permit 192.168.30.0 0.0.0.255
 exit
!
! ───────────────────────────────────────────────────────────────
! PASO 2: Crear Pool de Direcciones NAT
! ───────────────────────────────────────────────────────────────
ip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
!
! ───────────────────────────────────────────────────────────────
! PASO 3: Configurar NAT Dinámico con PAT (Overload)
! ───────────────────────────────────────────────────────────────
ip nat inside source list R2NAT pool R2POOL overload
!
! ───────────────────────────────────────────────────────────────
! PASO 4: Configurar NAT Estático para Servidor
! ───────────────────────────────────────────────────────────────
ip nat inside source static 192.168.20.254 209.165.202.130
!
! ───────────────────────────────────────────────────────────────
! PASO 5: Configurar Interfaces NAT
! ───────────────────────────────────────────────────────────────
!
! Interfaces INSIDE
interface Serial0/0/0
 ip nat inside
!
interface Serial0/0/1
 ip nat inside
!
interface FastEthernet0/0
 ip nat inside
!
! Interfaz OUTSIDE
interface Serial0/1/0
 ip nat outside
!
! ───────────────────────────────────────────────────────────────
! Guardar configuración
! ───────────────────────────────────────────────────────────────
exit
write memory
!
end
!
```

---

## 🔍 Verificación y Pruebas

### 1️⃣ Verificar Traducciones NAT Activas

```cisco
R2# show ip nat translations

! ──────────────────────────────────────────────────────────
! Salida esperada:
! ──────────────────────────────────────────────────────────
! Pro Inside global      Inside local       Outside local      Outside global
! --- 209.165.202.130    192.168.20.254     ---                ---
! tcp 209.165.202.129:80 192.168.10.10:1025 209.165.201.14:80  209.165.201.14:80
! icmp 209.165.202.129:1 192.168.30.10:1    209.165.201.30:1   209.165.201.30:1
```

**Interpretación**:
- **Primera línea**: NAT estático del servidor (permanente)
- **Segunda línea**: NAT dinámico de PC1 navegando web
- **Tercera línea**: NAT dinámico de PC2 haciendo ping

---

### 2️⃣ Verificar Estadísticas NAT

```cisco
R2# show ip nat statistics

! ──────────────────────────────────────────────────────────
! Información mostrada:
! ──────────────────────────────────────────────────────────
! Total active translations: 5 (1 static, 4 dynamic; 2 extended)
! Peak translations: 8
! Outside interfaces:
!   Serial0/1/0
! Inside interfaces:
!   Serial0/0/0, Serial0/0/1, FastEthernet0/0
! Hits: 247  Misses: 0
! Pool R2POOL: netmask 255.255.255.252
!         start 209.165.202.129 end 209.165.202.129
!         type generic, total addresses 1, allocated 1 (100%), misses 0
```

**Métricas importantes**:
- **Hits**: Número de paquetes traducidos exitosamente
- **Misses**: Paquetes que no encontraron traducción (debe ser 0)
- **Pool allocated**: Porcentaje de IPs del pool en uso

---

### 3️⃣ Verificar ACL

```cisco
R2# show access-lists

! ──────────────────────────────────────────────────────────
! Salida esperada:
! ──────────────────────────────────────────────────────────
! Standard IP access list R2NAT
!     10 permit 192.168.10.0, wildcard bits 0.0.0.255 (15 matches)
!     20 permit 192.168.20.0, wildcard bits 0.0.0.255 (8 matches)
!     30 permit 192.168.30.0, wildcard bits 0.0.0.255 (12 matches)
```

**Nota**: Los `matches` indican cuántos paquetes han coincidido con cada regla.

---

### 4️⃣ Verificar Pool NAT

```cisco
R2# show ip nat pool

! ──────────────────────────────────────────────────────────
! Salida esperada:
! ──────────────────────────────────────────────────────────
! Pool R2POOL
!  Refcount 3
!  Netmask 255.255.255.252
!  Start 209.165.202.129
!  End 209.165.202.129
!  Type generic, Total addresses 1, Allocated 1 (100%), Misses 0
```

---

### 5️⃣ Probar Conectividad End-to-End

#### Prueba 1: Desde PC1 (LAN1) hacia Internet

```cisco
! En PC1 (192.168.10.10)
PC> ping 209.165.201.14

! ──────────────────────────────────────────────────────────
! Salida esperada:
! ──────────────────────────────────────────────────────────
! Pinging 209.165.201.14 with 32 bytes of data:
! 
! Reply from 209.165.201.14: bytes=32 time=10ms TTL=125
! Reply from 209.165.201.14: bytes=32 time=9ms TTL=125
! Reply from 209.165.201.14: bytes=32 time=11ms TTL=125
! Reply from 209.165.201.14: bytes=32 time=10ms TTL=125
! 
! Ping statistics for 209.165.201.14:
!     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

**✅ Resultado esperado**: Ping exitoso (4/4 paquetes recibidos)

#### Prueba 2: Desde PC2 (LAN3) hacia servidor externo

```cisco
! En PC2 (192.168.30.10)
PC> ping cisco.pka

! Debe resolver a 209.165.201.30 y recibir respuestas
```

**✅ Resultado esperado**: Ping exitoso

#### Prueba 3: Desde Internet hacia el Servidor Interno (NAT Estático)

```cisco
! En PC-Exterior (209.165.201.14)
PC> ping 209.165.202.130

! ──────────────────────────────────────────────────────────
! Salida esperada:
! ──────────────────────────────────────────────────────────
! Pinging 209.165.202.130 with 32 bytes of data:
! 
! Reply from 209.165.202.130: bytes=32 time=5ms TTL=126
! Reply from 209.165.202.130: bytes=32 time=6ms TTL=126
! Reply from 209.165.202.130: bytes=32 time=5ms TTL=126
! Reply from 209.165.202.130: bytes=32 time=6ms TTL=126
```

**✅ Resultado esperado**: El servidor local.pka (192.168.20.254) responde a través de su IP pública

#### Prueba 4: Verificar traducciones simultáneas (PAT)

```cisco
! Generar tráfico desde múltiples hosts al mismo tiempo:
! - PC1: ping 209.165.201.14
! - PC2: ping 209.165.201.30
! - Desde R1: ping 209.165.201.14

! En R2
R2# show ip nat translations verbose

! Debe mostrar múltiples entradas usando 209.165.202.129 con diferentes puertos
```

**✅ Resultado esperado**: Múltiples traducciones activas compartiendo la misma IP pública

---

## 🔧 Troubleshooting

### Problema 1: No hay traducción NAT

**🔴 Síntomas**:
```
PC> ping 209.165.201.14
Request timed out.
Request timed out.
```

**🔍 Diagnóstico**:

```cisco
R2# show ip nat translations
! Tabla vacía o sin entrada para la IP origen

R2# show ip nat statistics
! Muestra Misses > 0
```

**✅ Soluciones**:

1. Verificar que las interfaces estén correctamente marcadas:
```cisco
R2# show ip interface Serial0/0/0 | include NAT
! Debe mostrar: NAT: inside

R2# show ip interface Serial0/1/0 | include NAT
! Debe mostrar: NAT: outside
```

2. Verificar que la ACL permita la red:
```cisco
R2# show access-lists
! Confirmar que la red del host esté en R2NAT
```

3. Re-aplicar configuración de interfaces si es necesario:
```cisco
R2(config)# interface Serial0/0/0
R2(config-if)# ip nat inside
```

---

### Problema 2: El servidor no es accesible desde Internet

**🔴 Síntomas**:
```
PC-Exterior> ping 209.165.202.130
Request timed out.
```

**🔍 Diagnóstico**:

```cisco
R2# show ip nat translations | include 192.168.20.254
! No aparece la entrada estática
```

**✅ Soluciones**:

1. Verificar configuración de NAT estático:
```cisco
R2# show running-config | include ip nat inside source static
! Debe mostrar: ip nat inside source static 192.168.20.254 209.165.202.130
```

2. Re-configurar si es necesario:
```cisco
R2(config)# no ip nat inside source static 192.168.20.254 209.165.202.130
R2(config)# ip nat inside source static 192.168.20.254 209.165.202.130
```

3. Verificar que la interfaz F0/0 esté como inside:
```cisco
R2# show ip interface FastEthernet0/0 | include NAT
```

---

### Problema 3: Nombres de ACL o Pool incorrectos

**🔴 Síntomas**:
```
R2(config)# ip nat inside source list R2NAT pool R2POOL overload
% Dynamic mapping not found
```

**🔍 Diagnóstico**:

```cisco
R2# show ip nat pool
! No aparece R2POOL

R2# show access-lists
! No aparece R2NAT
```

**✅ Solución**:

```cisco
! Eliminar configuración incorrecta
R2(config)# no ip nat inside source list [NOMBRE_INCORRECTO] pool [POOL_INCORRECTO] overload
R2(config)# no ip nat pool [POOL_INCORRECTO]
R2(config)# no ip access-list standard [ACL_INCORRECTA]

! Volver a Fase 1, 2 y 3 con nombres correctos: R2NAT y R2POOL
```

⚠️ **Importante**: Los nombres son case-sensitive. Debe ser exactamente `R2NAT` y `R2POOL`.

---

### Problema 4: Limpiar traducciones NAT (para troubleshooting)

```cisco
! Limpiar todas las traducciones dinámicas
R2# clear ip nat translation *

! Limpiar una traducción específica
R2# clear ip nat translation inside 192.168.10.10

! Reiniciar estadísticas
R2# clear ip nat statistics
```

---

## 💡 Conceptos Clave y Teoría

### ¿Por qué usar NAT?

1. **Conservación de direcciones IPv4**: Solo hay ~4.3 mil millones de direcciones IPv4. NAT permite que miles de dispositivos privados compartan pocas IPs públicas.

2. **Seguridad adicional**: Oculta la estructura interna de la red. Desde el exterior, solo se ve la IP pública del router.

3. **Flexibilidad**: Permite cambiar de proveedor de Internet sin renumerar toda la red interna.

4. **Reutilización de direcciones privadas**: Múltiples organizaciones pueden usar los mismos rangos privados (RFC 1918) sin conflictos.

### Direcciones IP Privadas (RFC 1918)

| Clase | Rango | CIDR | Número de Hosts |
|-------|-------|------|-----------------|
| Clase A | 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 | 16,777,216 |
| Clase B | 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 | 1,048,576 |
| Clase C | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | 65,536 |

En este laboratorio usamos redes **Clase C privadas** (192.168.x.0/24).

### Diferencias: NAT Dinámico vs NAT Estático vs PAT

| Característica | NAT Dinámico | NAT Estático | PAT (Overload) |
|----------------|--------------|--------------|----------------|
| **Mapeo** | Temporal | Permanente | Temporal con puertos |
| **Relación** | N:N (pool) | 1:1 | N:1 |
| **Direccionalidad** | Inside→Outside | Bidireccional | Inside→Outside |
| **Conservación IP** | Media | Baja | Muy alta |
| **Uso típico** | Múltiples usuarios | Servidores | Organizaciones pequeñas/medianas |
| **Este lab** | - | local.pka | PC1, PC2, otros hosts |

---

## 📚 Recursos y Referencias

### Documentación Oficial de Cisco

- 📘 [Cisco NAT Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/15-mt/nat-15-mt-book.html)
- 📘 [NAT: Local and Global Definitions](https://www.cisco.com/c/en/us/support/docs/ip/network-address-translation-nat/4606-8.html)
- 📘 [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
- 📘 [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-4m-t/products-command-reference-list.html)

### RFCs Relevantes

| RFC | Título | Año |
|-----|--------|-----|
| **RFC 1918** | Address Allocation for Private Internets | 1996 |
| **RFC 1631** | The IP Network Address Translator (NAT) | 1994 |
| **RFC 3022** | Traditional IP Network Address Translator | 2001 |

### Tutoriales y Cursos Recomendados

- 🎥 [Jeremy's IT Lab - Free CCNA 200-301 Course](https://www.youtube.com/c/JeremysITLab)
- 🎥 [NetworkChuck - Cisco Packet Tracer Tutorials](https://www.youtube.com/c/NetworkChuck)
- 📖 [Cisco Networking Academy](https://www.netacad.com/)
- 📖 [Cisco Learning Network](https://learningnetwork.cisco.com/)

### Comunidades y Foros

- 💬 [Reddit - r/ccna](https://www.reddit.com/r/ccna/)
- 💬 [Cisco Community](https://community.cisco.com/)
- 💬 [TechExams Forums - Cisco Certification](https://www.techexams.net/forums/)

---

## ✅ Checklist de Validación Final

Antes de dar por completado el laboratorio, verifica todos estos puntos:

### Configuración

- [ ] ✅ ACL estándar `R2NAT` creada con nombre exacto
- [ ] ✅ Las 3 redes LAN permitidas en orden: LAN1 → LAN2 → LAN3
- [ ] ✅ Pool `R2POOL` creado con dirección 209.165.202.129
- [ ] ✅ Máscara del pool correcta: 255.255.255.252
- [ ] ✅ NAT dinámico configurado con palabra clave `overload`
- [ ] ✅ NAT estático configurado: 192.168.20.254 ↔ 209.165.202.130
- [ ] ✅ Interfaces S0/0/0, S0/0/1, F0/0 marcadas como `inside`
- [ ] ✅ Interfaz S0/1/0 marcada como `outside`
- [ ] ✅ Configuración guardada con `write memory`

### Verificación

- [ ] ✅ `show ip nat translations` muestra traducciones activas
- [ ] ✅ `show ip nat statistics` muestra configuración correcta
- [ ] ✅ `show access-lists` muestra R2NAT con las 3 redes
- [ ] ✅ `show ip nat pool` muestra R2POOL correctamente
- [ ] ✅ PC1 puede hacer ping a 209.165.201.14
- [ ] ✅ PC2 puede hacer ping a 209.165.201.30
- [ ] ✅ PC-Exterior puede hacer ping a 209.165.202.130 (servidor)
- [ ] ✅ Múltiples hosts pueden acceder simultáneamente (PAT funciona)

---

## 🎓 Preguntas de Reflexión

Para profundizar tu comprensión, responde estas preguntas:

<details>
<summary><b>1. ¿Por qué es necesario usar PAT (overload) en este escenario?</b></summary>

**Respuesta**: Porque tenemos 3 redes internas (LAN1, LAN2, LAN3) con potencialmente decenas o cientos de hosts, pero solo una dirección IP pública disponible para NAT dinámico (209.165.202.129). PAT permite que todos estos dispositivos compartan esa única IP pública diferenciando las conexiones mediante números de puerto únicos. Sin PAT, necesitaríamos tantas IPs públicas como hosts simultáneos, lo cual es costoso e ineficiente.

</details>

<details>
<summary><b>2. ¿Cuál es la diferencia entre las traducciones de PC1 y del servidor local.pka?</b></summary>

**Respuesta**: 
- **PC1 usa NAT dinámico con PAT**: La traducción solo existe cuando hay tráfico activo. El router asigna dinámicamente un puerto disponible en la IP 209.165.202.129. Cuando la conexión termina, la traducción se elimina.
- **Servidor local.pka usa NAT estático**: El mapeo 192.168.20.254 ↔ 209.165.202.130 es permanente y siempre está en la tabla NAT, incluso sin tráfico. Esto permite que hosts externos inicien conexiones hacia el servidor.

</details>

<details>
<summary><b>3. ¿Qué pasaría si no se configura la interfaz Serial0/1/0 como "outside"?</b></summary>

**Respuesta**: NAT no funcionaría. El router necesita saber qué interfaz conecta al mundo exterior para poder aplicar las traducciones correctamente. Sin una interfaz marcada como "outside", el router no sabría dónde reemplazar las direcciones IP privadas por públicas ni dónde aplicar las traducciones inversas en el tráfico de retorno.

</details>

<details>
<summary><b>4. ¿Por qué la ACL usa wildcard mask en lugar de subnet mask?</b></summary>

**Respuesta**: Las ACLs de Cisco usan **wildcard masks**, que funcionan de manera opuesta a las subnet masks:
- **Subnet mask /24**: 255.255.255.0 (unos a la izquierda, ceros a la derecha)
- **Wildcard mask /24**: 0.0.0.255 (ceros a la izquierda, unos a la derecha)

La wildcard 0.0.0.255 significa "coincidir exactamente los primeros 3 octetos, ignorar el cuarto". Es equivalente a /24 pero con notación inversa.

</details>

<details>
<summary><b>5. ¿Qué ventajas de seguridad ofrece NAT?</b></summary>

**Respuesta**: 
1. **Ocultamiento de topología**: Los hosts externos solo ven las IPs públicas del router, no la estructura interna ni direcciones privadas.
2. **Protección contra escaneos**: Un atacante no puede saber cuántos dispositivos hay internamente ni sus direcciones.
3. **Punto de control centralizado**: Todo el tráfico pasa por el router NAT, donde se pueden aplicar políticas de seguridad adicionales (ACLs, firewalls).
4. **Dificulta ataques directos**: Los hosts internos no son directamente accesibles desde Internet (excepto NAT estático explícito).

</details>

---

## 🤝 Contribuciones

Este proyecto de laboratorio puede mejorarse continuamente. ¡Las contribuciones son bienvenidas!

### Cómo Contribuir

1. **Fork** este repositorio
2. Crea una rama para tu mejora (`git checkout -b feature/mejora-nat-ipv6`)
3. **Commit** tus cambios (`git commit -m 'Añade configuración de NAT64'`)
4. **Push** a la rama (`git push origin feature/mejora-nat-ipv6`)
5. Abre un **Pull Request**

### Ideas de Mejora

- 📊 Implementar NAT64 para transición IPv6
- 🔒 Añadir configuración de firewall con ACLs extendidas
- 📈 Documentar límites de escalabilidad y optimización
- 🎨 Crear diagramas animados del flujo de paquetes
- 🧪 Integrar scripts de pruebas automatizadas
- 📝 Traducir documentación a otros idiomas

---

## 👨‍💻 Autor

<div align="center">

**Darwin Manuel Ovalles Cesar**

<p align="center">
<a href="https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/" target="_blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="LinkedIn - Darwin Ovalles" height="40" width="50" />
</a>
</p>

💼 **LinkedIn**: [darwin-manuel-ovalles-cesar-dev](https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/)  
🌐 **GitHub**: [@dovalless](https://github.com/dovalless)  
📧 **Email**: Disponible en LinkedIn

---

### Sobre el Autor

*Profesional en formación en el área de redes y telecomunicaciones, con enfoque en infraestructura de red empresarial y servicios de traducción de direcciones. Este laboratorio forma parte de mi portafolio de prácticas en Cisco Packet Tracer, demostrando competencias en configuración de routers Cisco IOS, protocolos de enrutamiento y servicios de red.*

**Áreas de interés**: Redes empresariales, Seguridad de red, Virtualización, Cloud Computing

</div>

---

## 📄 Licencia

Este proyecto tiene fines **exclusivamente educativos** y está disponible bajo la licencia MIT.

```
MIT License

Copyright (c) 2024 Darwin Manuel Ovalles Cesar

Se concede permiso para usar, copiar, modificar y distribuir este 
software con fines educativos y de aprendizaje.
```

Ver archivo [LICENSE](LICENSE) para más detalles.

---

## 🏆 Agradecimientos

- 🎓 **Cisco Networking Academy** - Por proporcionar Packet Tracer y recursos educativos de calidad
- 🌐 **Comunidad CCNA** - Por compartir conocimientos y mejores prácticas
- 📚 **Cisco Press** - Por documentación técnica exhaustiva
- 👥 **Instructores y Mentores** - Por guía y retroalimentación constante
- ☕ **Comunidad Open Source** - Por herramientas y recursos complementarios

---

## 📊 Estadísticas del Proyecto

<div align="center">

| Métrica | Valor |
|---------|-------|
| **Dispositivo principal configurado** | Router R2 (Cisco 2900) |
| **Redes internas** | 3 LANs (10, 20, 30) |
| **Direcciones IP públicas utilizadas** | 2 de /30 |
| **Hosts soportados** | Ilimitados (gracias a PAT) |
| **Tiempo estimado del laboratorio** | 30-45 minutos |
| **Nivel de dificultad** | Intermedio |
| **Comandos Cisco IOS principales** | 15+ |
| **Tecnologías implementadas** | NAT, PAT, ACL, Static Routing |

</div>

---

<div align="center">

## ⭐ Si este proyecto te fue útil, considera darle una estrella ⭐

**🚀 ¡Feliz configuración y aprendizaje de redes! 🚀**

---

Desarrollado con 💙 y ☕ para estudiantes de redes

**#CiscoPacketTracer #NAT #PAT #CCNA #Networking #CiscoIOS #RedesEmpresariales**

---

*Última actualización: 2024*

</div>
```
