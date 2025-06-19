# Clase 4: Redes y Protocolos Básicos para DevOps

**Módulo:** 1  
**Temática del Módulo:** Introducción a los fundamentos de DevOps  
**Tema Específico:** Conceptos fundamentales de redes y comunicación

## Contenido de la Clase:

### ¿Por qué Redes son Cruciales en DevOps? (La Analogía del Sistema Postal)

Imagina el **sistema postal mundial**:
- **Direcciones (IP):** Cada casa tiene una dirección única
- **Códigos postales (Puertos):** Diferentes tipos de correo van a lugares específicos
- **Protocolos (HTTP, SSH):** Reglas sobre cómo enviar diferentes tipos de paquetes
- **DNS:** Como la guía telefónica que convierte nombres en direcciones

**En DevOps necesitas entender redes porque:**
- Aplicaciones se comunican entre servidores
- Bases de datos están en servidores separados
- APIs conectan diferentes servicios
- Monitoreo requiere comunicación de red
- Seguridad depende de configuración de red

### Modelo OSI Simplificado para DevOps

**¿Qué es el modelo OSI?**
Es como las "capas de una cebolla" que explican cómo funciona la comunicación de red.

**Las 4 capas que necesitas conocer:**

1. **Física/Enlace:** Cables, WiFi (no te preocupes mucho por esto)
2. **Red (IP):** Direcciones y enrutamiento
3. **Transporte (TCP/UDP):** Cómo enviar datos confiablemente
4. **Aplicación (HTTP, SSH, FTP):** Protocolos que usan las aplicaciones

### Direcciones IP: La Base de Todo

**¿Qué es una dirección IP?**
Es como la dirección postal de una computadora en la red.

**IPv4 (más común):**
- Formato: `192.168.1.100`
- 4 números separados por puntos
- Cada número va de 0 a 255

**Tipos de direcciones IPv4:**

#### Direcciones Privadas (para redes internas):
- `192.168.x.x` (routers domésticos)
- `10.x.x.x` (redes empresariales grandes)
- `172.16.x.x` a `172.31.x.x` (redes empresariales medianas)

#### Direcciones Públicas:
- Todo lo demás (visibles en Internet)
- Ejemplo: `8.8.8.8` (DNS de Google)

#### Direcciones Especiales:
- `127.0.0.1` (localhost - tu propia máquina)
- `0.0.0.0` (todas las interfaces)

### Puertos: Las "Puertas" de los Servicios

**¿Qué es un puerto?**
Es como diferentes "puertas" en un edificio. Cada servicio usa su puerta específica.

**Puertos importantes para DevOps:**
- **22:** SSH (conexión remota segura)
- **80:** HTTP (páginas web sin cifrar)
- **443:** HTTPS (páginas web cifradas)
- **3306:** MySQL (base de datos)
- **5432:** PostgreSQL (base de datos)
- **6379:** Redis (cache)
- **8080:** Aplicaciones web (desarrollo)
- **9090:** Prometheus (monitoreo)

**Rangos de puertos:**
- **0-1023:** Puertos del sistema (requieren privilegios)
- **1024-49151:** Puertos registrados
- **49152-65535:** Puertos dinámicos/privados

### DNS: El "Traductor" de Internet

**¿Qué es DNS?**
**D**omain **N**ame **S**ystem - convierte nombres legibles en direcciones IP.

**Ejemplo:**
- Tú escribes: `www.google.com`
- DNS traduce: `142.250.191.110`

**Tipos de registros DNS importantes:**
- **A Record:** Nombre → IP (google.com → 8.8.8.8)
- **CNAME:** Alias (www.google.com → google.com)
- **MX:** Servidores de email
- **TXT:** Información adicional (verificaciones)

**Comandos útiles:**
```bash
nslookup google.com      # Resolver nombre a IP
dig google.com           # Información DNS detallada
host google.com          # Resolución rápida
```

### Protocolos de Red Esenciales

#### HTTP/HTTPS: La Base de la Web

**HTTP (HyperText Transfer Protocol):**
- Puerto 80
- Sin cifrado (inseguro)
- Métodos principales: GET, POST, PUT, DELETE

**HTTPS (HTTP Secure):**
- Puerto 443
- Con cifrado SSL/TLS (seguro)
- Obligatorio para aplicaciones modernas

**Códigos de respuesta HTTP:**
- **200:** OK (éxito)
- **301/302:** Redirección
- **404:** No encontrado
- **500:** Error del servidor
- **503:** Servicio no disponible

#### SSH: Conexión Remota Segura

**SSH (Secure Shell):**
- Puerto 22
- Conexión cifrada a servidores remotos
- Usado para administración y despliegues

**Comandos SSH básicos:**
```bash
ssh usuario@servidor.com          # Conectar
ssh -p 2222 usuario@servidor      # Puerto específico
ssh -i clave.pem usuario@servidor # Con clave privada
```

#### FTP/SFTP: Transferencia de Archivos

**FTP (File Transfer Protocol):**
- Puerto 21
- Sin cifrado (inseguro)
- Raramente usado en DevOps moderno

**SFTP (SSH File Transfer Protocol):**
- Puerto 22 (sobre SSH)
- Cifrado y seguro
- Preferido para transferencias

### TCP vs UDP: Protocolos de Transporte

#### TCP (Transmission Control Protocol):
- **Confiable:** Garantiza que los datos lleguen
- **Ordenado:** Los datos llegan en orden correcto
- **Verificación:** Confirma recepción
- **Usado por:** HTTP, SSH, FTP, bases de datos
- **Más lento** pero más seguro

#### UDP (User Datagram Protocol):
- **No confiable:** No garantiza entrega
- **Sin orden:** Los datos pueden llegar desordenados
- **Sin verificación:** "Dispara y olvida"
- **Usado por:** DNS, video streaming, juegos
- **Más rápido** pero menos seguro

### Firewalls y Seguridad de Red

**¿Qué es un firewall?**
Es como un "portero" que decide qué tráfico puede entrar y salir.

**Tipos de firewalls:**
- **iptables (Linux):** Firewall a nivel de sistema operativo
- **Security Groups (AWS):** Firewall a nivel de cloud
- **Network ACLs:** Firewall a nivel de red

**Reglas básicas de firewall:**
```bash
# Ver reglas actuales
sudo iptables -L

# Permitir puerto 80 (HTTP)
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Permitir SSH solo desde IP específica
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.1.100 -j ACCEPT
```

### Load Balancers: Distribución de Carga

**¿Qué es un Load Balancer?**
Es como un "director de tráfico" que distribuye las peticiones entre múltiples servidores.

**Beneficios:**
- **Alta disponibilidad:** Si un servidor falla, otros continúan
- **Escalabilidad:** Más servidores = más capacidad
- **Mejor rendimiento:** Carga distribuida uniformemente

**Tipos de Load Balancers:**

#### Layer 4 (Transporte):
- Decide basado en IP y puerto
- Más rápido, menos inteligente
- Ejemplo: distribución por round-robin

#### Layer 7 (Aplicación):
- Decide basado en contenido HTTP
- Más lento, más inteligente
- Ejemplo: rutear `/api` a servidores API, `/images` a servidores de imágenes

**Algoritmos de balanceo:**
- **Round Robin:** Turnos secuenciales
- **Least Connections:** Servidor con menos conexiones
- **IP Hash:** Basado en IP del cliente
- **Weighted:** Servidores con diferentes capacidades

### VPN: Redes Privadas Virtuales

**¿Qué es una VPN?**
Es como un "túnel privado" a través de Internet que conecta redes remotas de forma segura.

**Usos en DevOps:**
- Acceso seguro a servidores internos
- Conexión entre oficinas y data centers
- Acceso remoto para desarrolladores
- Conexión segura a recursos de cloud privado

**Tipos de VPN:**
- **Site-to-Site:** Conecta oficinas
- **Client-to-Site:** Empleados remotos
- **Point-to-Point:** Conexión directa entre dos puntos

### Conceptos de Red en la Nube

#### Virtual Private Cloud (VPC)

**¿Qué es una VPC?**
Es como tu "propia sección privada" dentro del data center de un proveedor de nube.

**Componentes de VPC:**
- **Subnets:** Segmentos de red (pública/privada)
- **Route Tables:** Tablas de enrutamiento
- **Internet Gateway:** Acceso a Internet
- **NAT Gateway:** Internet saliente para subnets privadas

#### Subnets Públicas vs Privadas

**Subnet Pública:**
- Tiene acceso directo a Internet
- Para web servers, load balancers
- IPs públicas asignables

**Subnet Privada:**
- No tiene acceso directo a Internet
- Para bases de datos, aplicaciones internas
- Solo IPs privadas

### Herramientas de Diagnóstico de Red

#### Comandos básicos de diagnóstico:

```bash
# Verificar conectividad
ping google.com                    # Conectividad básica
ping -c 4 8.8.8.8                 # 4 pings específicos

# Rastrear ruta
traceroute google.com              # Ver ruta de paquetes
mtr google.com                     # Traceroute continuo

# Verificar puertos
telnet servidor.com 80             # Probar conexión a puerto
nc -zv servidor.com 80-90          # Escanear rango de puertos

# Información de red local
ifconfig                           # Información de interfaces (Linux)
ip addr show                       # Información detallada de IP
netstat -tulpn                     # Puertos en escucha
ss -tulpn                          # Versión moderna de netstat
```

#### Herramientas avanzadas:

```bash
# Capturar tráfico de red
sudo tcpdump -i eth0               # Capturar paquetes
sudo tcpdump port 80               # Solo tráfico HTTP

# Análisis de DNS
dig +trace google.com              # Rastrear resolución DNS
nslookup -type=MX gmail.com        # Registros MX específicos

# Verificar certificados SSL
openssl s_client -connect google.com:443   # Verificar certificado
```

### Seguridad de Red en DevOps

#### Principios de Seguridad:

1. **Principio de menor privilegio:** Solo acceso necesario
2. **Defensa en profundidad:** Múltiples capas de seguridad
3. **Segmentación de red:** Separar diferentes tipos de tráfico

#### Implementación práctica:

**Network Segmentation:**
```
Internet → Load Balancer → Web Servers → App Servers → Database
   ↓            ↓              ↓            ↓           ↓
  DMZ      Public Subnet   Private Subnet  Private    Private
                                          Subnet     Subnet
```

**Security Groups (Cloud):**
- Reglas de entrada y salida por instancia
- Stateful (respuestas automáticamente permitidas)
- Específicas por protocolo, puerto y origen

### Monitoreo de Red

#### Métricas importantes:
- **Latencia:** Tiempo de respuesta
- **Throughput:** Datos transferidos por segundo
- **Packet Loss:** Paquetes perdidos
- **Bandwidth Utilization:** Uso del ancho de banda

#### Herramientas de monitoreo:
- **SNMP:** Protocolo para monitoreo de dispositivos
- **Nagios:** Monitoreo de infraestructura
- **Zabbix:** Monitoreo integral
- **Prometheus:** Métricas y alertas modernas

### Troubleshooting de Red en DevOps

#### Metodología de diagnóstico:

1. **¿Hay conectividad básica?** → `ping`
2. **¿El DNS está funcionando?** → `nslookup/dig`
3. **¿El puerto está abierto?** → `telnet/nc`
4. **¿Hay problemas de firewall?** → Revisar reglas
5. **¿El servicio está corriendo?** → `systemctl status`
6. **¿Hay problemas de certificados?** → `openssl s_client`

#### Problemas comunes y soluciones:

**Problema: "Connection refused"**
- El servicio no está corriendo
- Puerto incorrecto
- Firewall bloqueando

**Problema: "Connection timeout"**
- Firewall bloqueando
- Servidor sobrecargado
- Problemas de red

**Problema: "DNS resolution failed"**
- Servidor DNS no responde
- Registro DNS incorrecto
- Cache DNS obsoleto

### APIs y Microservicios

#### REST APIs:
**Métodos HTTP principales:**
- **GET:** Obtener datos
- **POST:** Crear nuevos recursos
- **PUT:** Actualizar recursos completos
- **PATCH:** Actualizar parcialmente
- **DELETE:** Eliminar recursos

**Códigos de estado importantes:**
- **2xx:** Éxito (200, 201, 204)
- **3xx:** Redirección (301, 302)
- **4xx:** Error del cliente (400, 401, 403, 404)
- **5xx:** Error del servidor (500, 502, 503)

#### Service Mesh:
Para comunicación entre microservicios:
- **Service Discovery:** Encontrar servicios
- **Load Balancing:** Distribución de carga
- **Circuit Breakers:** Prevenir cascadas de fallas
- **Observability:** Monitoreo y trazado

## Fuentes Relevantes:
- **Documentación oficial:** [Cisco Networking Basics](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/networking-basics.html)
- **Tutorial interactivo:** [Subnet Calculator](https://www.subnet-calculator.com/)
- **Referencia:** [IANA Port Numbers](https://www.iana.org/assignments/service-names-port-numbers/)
- **Libro recomendado:** "TCP/IP Illustrated" - W. Richard Stevens

## Videos Sugeridos:
- **"Computer Networking Complete Course"** - Kunal Kushwaha
- **"Redes desde cero"** - Pelado Nerd
- **"Networking Fundamentals"** - NetworkChuck
- **"Load Balancers Explained"** - TechWorld with Nana

## Resumen y Próximos Pasos:

**Conceptos clave de esta clase:**
- Las redes son fundamentales para la comunicación entre servicios en DevOps
- IP, puertos, DNS y protocolos (HTTP/HTTPS, SSH, TCP/UDP) son la base
- Firewalls y load balancers proporcionan seguridad y escalabilidad
- Herramientas de diagnóstico ayudan a resolver problemas de conectividad
- La seguridad de red debe implementarse en múltiples capas

**Próxima clase:** "Scripting para Automatización - Bash y PowerShell" donde aprenderás a automatizar tareas repetitivas, el primer paso hacia la automatización completa en DevOps.