# 🌐 Packet Tracer: Configuración Avanzada de NAT para IPv4

<div align="center">

**Laboratorio de Redes CISCO - Traducción de Direcciones de Red (NAT)**

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com)
[![NAT Protocol](https://img.shields.io/badge/Protocol-NAT/PAT-00A86B?style=for-the-badge)](https://www.cisco.com/)
[![CCNA](https://img.shields.io/badge/Certification-CCNA-blue?style=for-the-badge)](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/associate/ccna.html)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

[📋 Tabla de Direcciones](#-tabla-de-asignación-de-direcciones) • 
[⚙️ Configuración](#️-configuración-paso-a-paso)  
[🔍 Verificación](#-comandos-de-verificación) • 
[🛠️ Tecnologías](#️-tecnologías-y-herramientas) • 
[👨‍💻 Autor](#-autor)

</div>

---

## 🎯 Challenge: Configuración de Red Empresarial con NAT Dinámico y Estático

<div align="center">
Laboratorio Práctico de Redes - Cisco Packet Tracer
📊 Topología • ⚙️ Configuración • 🔍 Verificación • 📈 Resultados • 👨‍💻 Autor
</div>

### 📋 Descripción del Proyecto
Este proyecto implementa una solución completa de **Network Address Translation (NAT)** en un entorno de red empresarial simulado. El objetivo es configurar el router **R2** como dispositivo de borde para permitir que múltiples redes LAN privadas accedan a Internet de forma segura y eficiente mediante técnicas avanzadas de traducción de direcciones.

### 🎯 Objetivos Técnicos
- **NAT Dinámico con PAT (Overload)**: Traducir direcciones de tres LANs internas a una única IP pública
- **NAT Estático**: Mapear servidores internos para acceso externo controlado
- **Control de Acceso**: Implementar ACLs para definir tráfico traducible
- **Optimización de Recursos IP**: Maximizar el uso de direcciones IPv4 públicas limitadas
- **Conectividad End-to-End**: Garantizar comunicación entre redes privadas e Internet

### 💼 Impacto del Proyecto
La implementación correcta de NAT es crucial para:
- ✅ Extender la vida útil del espacio IPv4
- ✅ Mejorar la seguridad mediante ocultamiento de topología interna
- ✅ Permitir escalabilidad de redes empresariales
- ✅ Facilitar la migración entre proveedores de servicios
- ✅ Habilitar acceso controlado a servicios internos desde Internet

---

## 📊 Tabla de Asignación de Direcciones

| Dispositivo | Interfaz | Dirección IP | Máscara | Propósito |
|------------|----------|--------------|---------|-----------|
| **R1** | S0/0/0 | 10.1.1.1 | /30 | Enlace WAN a R2 |
| **R1** | F0/0 | 192.168.10.1 | /24 | Gateway LAN1 |
| **R2** | S0/0/0 | 10.1.1.2 | /30 | Enlace WAN a R1 |
| **R2** | S0/0/1 | 10.2.2.1 | /30 | Enlace WAN a R3 |
| **R2** | S0/1/0 | 209.165.200.225 | /27 | Enlace a Internet |
| **R2** | F0/0/0 | 192.168.20.1 | /24 | Gateway LAN2 |
| **R3** | S0/0/1 | 10.2.2.2 | /30 | Enlace WAN a R2 |
| **R3** | F0/0 | 192.168.30.1 | /24 | Gateway LAN3 |
| **PC1** | NIC | 192.168.10.10 | /24 | Estación LAN1 |
| **PC2** | NIC | 192.168.30.10 | /24 | Estación LAN3 |
| **local.pka** | NIC | 192.168.20.254 | /24 | Servidor Interno |

---

## 🛠️ Tecnologías y Herramientas

### 🔧 Stack Tecnológico
| Categoría | Tecnología | Versión | Uso |
|-----------|------------|---------|-----|
| **Simulación** | Cisco Packet Tracer | 8.2+ | Entorno de laboratorio virtual |
| **Dispositivos** | Cisco Routers | 2901 Series | Ruteo y traducción NAT |
| **Protocolos** | IPv4, NAT, PAT, ACLs | Standard | Comunicación y seguridad |
| **Conectividad** | Serial, Ethernet | - | Enlaces WAN/LAN |

### 📚 Conceptos Implementados
- **NAT Dinámico con Overload (PAT)**: Múltiples hosts → Una IP pública
- **NAT Estático 1:1**: Servidor interno → IP pública fija
- **ACL Estándar**: Filtrado de tráfico para NAT
- **Interfaces Inside/Outside**: Configuración de zonas de seguridad
- **Pool de Direcciones**: Gestión de IPs públicas disponibles

### 📊 Especificaciones Técnicas
- **Redes Privadas**: 3 subredes /24 (192.168.10.0, 192.168.20.0, 192.168.30.0)
- **IPs Públicas**: Rango 209.165.202.128/30 (2 IPs utilizables)
- **Traducciones**: ~50+ hosts compartiendo 1 IP pública (PAT)
- **Servidores Expuestos**: 1 servidor con IP pública dedicada

---

## ⚙️ Configuración Paso a Paso (Router R2)

### Fase 1: Definición de ACL para Tráfico Traducible
```cisco
! Crear ACL estándar con nombre para identificar redes internas
ip access-list standard R2NAT
 permit 192.168.10.0 0.0.0.255    ! Red LAN1
 permit 192.168.20.0 0.0.0.255    ! Red LAN2
 permit 192.168.30.0 0.0.0.255    ! Red LAN3
 remark "ACL para NAT - Define redes que serán traducidas"
```

### Fase 2: Configuración del Pool NAT Público
```cisco
! Definir pool de direcciones públicas disponibles
ip nat pool R2POOL 209.165.202.129 209.165.202.129 netmask 255.255.255.252
! Pool con una sola IP para PAT (Overload)
```

### Fase 3: NAT Dinámico con PAT (Overload)
```cisco
! Configurar NAT dinámico con sobrecarga (PAT)
ip nat inside source list R2NAT pool R2POOL overload
! Todos los hosts de las redes en ACL usarán la misma IP pública
```

### Fase 4: NAT Estático para Servidor Interno
```cisco
! Mapeo estático 1:1 para servidor web interno
ip nat inside source static 192.168.20.254 209.165.202.130
! Servidor accesible desde Internet mediante IP pública fija
```

### Fase 5: Configuración de Interfaces NAT
```cisco
! Interfaces internas (hacia redes privadas)
interface Serial0/0/0
 description Enlace a R1 (LAN1)
 ip nat inside
!
interface Serial0/0/1
 description Enlace a R3 (LAN3)
 ip nat inside
!
interface FastEthernet0/0/0
 description Red LAN2
 ip nat inside

! Interface externa (hacia Internet)
interface Serial0/1/0
 description Enlace a Internet (ISP)
 ip nat outside
```

### Fase 6: Configuración de Rutas (Opcional)
```cisco
! Ruta por defecto hacia Internet
ip route 0.0.0.0 0.0.0.0 Serial0/1/0
```

---

## 🔍 Comandos de Verificación y Pruebas

### Prueba 1: Verificación de Tabla NAT
```cisco
R2# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.202.129    192.168.10.10      ---                ---
--- 209.165.202.129    192.168.30.10      ---                ---
tcp 209.165.202.130:80 192.168.20.254:80  209.165.201.30:1234 209.165.201.30:1234
```

### Prueba 2: Estadísticas NAT
```cisco
R2# show ip nat statistics
Total active translations: 3 (1 static, 2 dynamic; 2 extended)
Peak translations: 5
Outside interfaces:
  Serial0/1/0
Inside interfaces:
  Serial0/0/0, Serial0/0/1, FastEthernet0/0/0
Hits: 150  Misses: 0
```

### Prueba 3: Pruebas de Conectividad
```cisco
! Desde PC1 (LAN1) hacia servidor externo
PC1> ping 209.165.201.30
Pinging 209.165.201.30 with 32 bytes of data:
Reply from 209.165.201.30: bytes=32 time=10ms TTL=126
Reply from 209.165.201.30: bytes=32 time=12ms TTL=126
Success rate is 100 percent (5/5)

! Desde Internet hacia servidor interno
External_Server> ping 209.165.202.130
Pinging 209.165.202.130 with 32 bytes of data:
Reply from 209.165.202.130: bytes=32 time=8ms TTL=126
Reply from 209.165.202.130: bytes=32 time=9ms TTL=126
```

### Prueba 4: Monitoreo en Tiempo Real
```cisco
R2# debug ip nat
NAT debugging is on
*Mar  1 00:10:15.123: NAT*: s=192.168.10.10->209.165.202.129, d=209.165.201.30 [50]
*Mar  1 00:10:15.127: NAT*: s=209.165.201.30, d=209.165.202.129->192.168.10.10 [50]
```

---

## 📈 Resultados y Validación

### ✅ Métricas de Éxito
| Métrica | Valor Obtenido | Estado |
|---------|---------------|--------|
| Conectividad LAN→Internet | 100% packet success | ✅ |
| Conectividad Internet→Servidor | 100% packet success | ✅ |
| Traducciones Simultáneas | 3+ traducciones activas | ✅ |
| Uso de IPs Públicas | 2/2 IPs utilizadas | ✅ |
| Tiempo de Respuesta | <15ms promedio | ✅ |

### 🔍 Análisis de Traducciones
**NAT Dinámico (PAT):**
- IP Pública: 209.165.202.129
- Hosts Compartiendo: ~50+ potenciales
- Puertos Utilizados: 1024-65535
- Eficiencia: Máxima (1:Many)

**NAT Estático:**
- IP Privada: 192.168.20.254
- IP Pública: 209.165.202.130
- Tipo: 1:1 bidirectional
- Aplicación: Servidor Web/Correo

### 📊 Diagrama de Flujo NAT
```
[LAN1:192.168.10.10] → (NAT PAT) → [209.165.202.129:1056] → Internet
[LAN3:192.168.30.10] → (NAT PAT) → [209.165.202.129:1057] → Internet
Internet → [209.165.202.130:80] → (NAT Estático) → [192.168.20.254:80]
```

---

## 💡 Conceptos Clave Aprendidos

### 🎯 NAT Dinámico con PAT (Port Address Translation)
- **Ventaja**: Conservación máxima de direcciones IPv4
- **Mecanismo**: Multiplexación basada en puertos TCP/UDP
- **Límite**: ~65,000 conexiones simultáneas por IP
- **Aplicación**: Clientes salientes (web browsing, email)

### 🛡️ NAT Estático
- **Ventaja**: Acceso bidireccional controlado
- **Mecanismo**: Mapeo permanente 1:1
- **Seguridad**: Exposición selectiva de servicios
- **Aplicación**: Servidores públicos (web, FTP, VPN)

### 🔐 Listas de Control de Acceso (ACL)
- **Función**: Filtrado de tráfico para NAT
- **Tipos**: Estándar (1-99) vs Extendida (100-199)
- **Wildcard**: Máscara inversa para rangos
- **Mejores Prácticas**: Específicas > Genéricas

### 🌐 Arquitectura de Red
- **Inside/Outside**: Definición clara de zonas de confianza
- **DMZ**: Zona desmilitarizada para servidores
- **Routing**: Integración con tablas de ruteo
- **Escalabilidad**: Diseño para crecimiento futuro

---

## 🚀 Guía de Implementación Rápida

### Requisitos Previos
1. ✅ Cisco Packet Tracer 8.2 o superior
2. ✅ Topología de red pre-configurada
3. ✅ Direccionamiento IP planeado
4. ✅ Conocimientos básicos de CLI Cisco

### Pasos de Implementación
```cisco
! 1. Acceder al modo configuración
enable
configure terminal

! 2. Crear ACL para NAT
ip access-list standard NAT_ACL
 permit [RED_PRIVADA] [WILDCARD]

! 3. Configurar pool NAT
ip nat pool [NOMBRE_POOL] [IP_INICIO] [IP_FIN] netmask [MASCARA]

! 4. Aplicar NAT dinámico
ip nat inside source list [ACL] pool [POOL] overload

! 5. Configurar NAT estático (si aplica)
ip nat inside source static [IP_LOCAL] [IP_GLOBAL]

! 6. Definir interfaces
interface [INTERFAZ]
 ip nat inside/outside
```

### Solución de Problemas Comunes
```cisco
! Problema: No hay traducciones NAT
show ip nat translations
debug ip nat
show access-lists

! Problema: Conectividad unidireccional
show ip route
show interfaces status
ping [destino] source [origen]

! Problema: Servidor no accesible
show running-config | section nat
show ip nat statistics
telnet [ip_publica] [puerto]
```

---

## 📚 Recursos y Referencias

### Documentación Oficial Cisco
- [Cisco NAT Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/15-mt/nat-15-mt-book.html)
- [Packet Tracer Resources](https://www.netacad.com/courses/packet-tracer)
- [CCNA Certification Guide](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/associate/ccna.html)

### Libros Recomendados
- "CCNA 200-301 Official Cert Guide" - Odom, W.
- "Network Address Translation" - Holdaway, K.
- "Cisco Packet Tracer Network Simulator" - Burke, J.

### Tutoriales y Laboratorios
- [Cisco Networking Academy](https://www.netacad.com)
- [Packet Tracer Labs Repository](https://github.com/topics/packet-tracer)
- [NAT Simulation Tutorials](https://www.youtube.com/c/Cisco)

### Herramientas Adicionales
- [Wireshark](https://www.wireshark.org/) - Análisis de tráfico
- [GNS3](https://www.gns3.com/) - Simulación avanzada
- [SolarWinds](https://www.solarwinds.com/) - Monitoreo de red

---

## 🤝 Contribuciones

¡Contribuciones son bienvenidas! Si deseas mejorar este proyecto:

1. **Fork** el repositorio
2. **Crea una rama** (`git checkout -b feature/MejoraNAT`)
3. **Commit** tus cambios (`git commit -m 'Añade configuración NAT66'`)
4. **Push** a la rama (`git push origin feature/MejoraNAT`)
5. **Abre un Pull Request**

### Áreas de Mejora Potenciales
- 📊 Implementación de NAT64 (IPv6 a IPv4)
- 🛡️ Integración con Firewall ASA
- 📈 Monitoreo con SNMP y NetFlow
- 🔄 Configuración de NAT de Fallover
- 🧪 Pruebas de estrés y capacidad

### Guía de Estilo para Contribuciones
- Usar notación CIDR para máscaras (/24 en lugar de 255.255.255.0)
- Incluir comentarios en configuraciones complejas
- Documentar cambios en el README
- Probar cambios en Packet Tracer antes de commit

---

## 👨‍💻 Autor

<div align="center">

**Darwin Manuel Ovalles Cesar**

<p align="center">
<a href="https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev" target="_blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="LinkedIn - Darwin Ovalles" height="40" width="50" />
</a>
<a href="https://github.com/dovalless" target="_blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/github.svg" alt="GitHub - Darwin Ovalles" height="40" width="50" />
</a>
</p>

💼 **LinkedIn**: [darwin-manuel-ovalles-cesar-dev](https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/)  
🌐 **GitHub**: [@dovalless](https://github.com/dovalless)  
🎓 **Certificaciones**: CCNA, Network+  

*"Este proyecto representa la aplicación práctica de conceptos fundamentales de redes en un entorno empresarial simulado. Cada línea de configuración refleja años de estudio y pasión por las tecnologías de networking."*

**#Cisco #Networking #NAT #PacketTracer #CCNA #IPv4**

</div>

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más detalles.

```
MIT License
Copyright (c) 2024 Darwin Manuel Ovalles Cesar
```

---

## 🙏 Agradecimientos

- **Cisco Networking Academy** - Por las herramientas y recursos educativos
- **Alura Latam** - Por la metodología de aprendizaje práctico
- **Comunidad de Redes** - Por el conocimiento compartido generosamente
- **Profesores y Mentores** - Por guiar el camino en el mundo del networking

<div align="center">

### ⭐ Si este proyecto te fue útil, considera darle una estrella en GitHub ⭐

### 🚀 ¡Feliz configuración de redes! 🚀

**Desarrollado con 💙 y ☕ por Darwin Ovalles**

---
*Última actualización: Enero 2024 | Packet Tracer 8.2 | CCNA v7*

</div>
```
