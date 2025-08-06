# Clase 5: Scripting para Automatización - Bash y PowerShell

**Módulo:** 2  
**Temática del Módulo:** Automatización  
**Tema Específico:** Fundamentos de scripting para automatizar tareas repetitivas

## Contenido de la Clase:

### ¿Por qué Scripting es Fundamental en DevOps? (La Analogía del Chef)

Imagina un **chef profesional en un restaurante**:
- **Recetas escritas (Scripts):** Instrucciones paso a paso que cualquier cocinero puede seguir
- **Mise en place (Variables):** Ingredientes preparados y organizados antes de cocinar
- **Técnicas estándar (Funciones):** Métodos reutilizables como "cortar en juliana" o "saltear"
- **Control de calidad (Validaciones):** Verificar temperatura, tiempo, presentación

**En DevOps necesitas scripting porque:**
- **Elimina errores humanos:** Las tareas se ejecutan exactamente igual cada vez
- **Ahorra tiempo:** Una tarea de 30 minutos se convierte en 30 segundos
- **Permite escalabilidad:** Un script puede ejecutarse en 1 o 1000 servidores
- **Documenta procesos:** El script es la documentación viviente del proceso
- **Facilita troubleshooting:** Los logs de scripts son más consistentes y trazables

### La Mentalidad del DevOps Engineer

**Principio fundamental:** *"Si lo haces más de 2 veces, automatízalo"*

**Antes del scripting:**
```
1. SSH al servidor
2. Verificar espacio en disco
3. Limpiar logs antiguos
4. Reiniciar servicio
5. Verificar que funcione
6. Actualizar documentación
7. Notificar al equipo
```
*Tiempo: 15-20 minutos por servidor*

**Después del scripting:**
```bash
./maintain_server.sh production-web-01
```
*Tiempo: 30 segundos por servidor*

### Bash: El Lenguaje Universal de Linux

#### ¿Qué es Bash?

**Bash (Bourne Again Shell)** es el intérprete de comandos más común en sistemas Linux/Unix.

**Características clave:**
- Disponible en prácticamente todos los sistemas Linux
- Excelente para automatización de tareas del sistema
- Integración nativa con comandos del sistema operativo
- Ideal para DevOps en entornos Linux

#### Estructura Básica de un Script Bash

```bash
#!/bin/bash
# Shebang: indica que interpreter usar

# Comentarios empiezan con #
# Este es un script básico

# Variables
NOMBRE="DevOps Engineer"
FECHA=$(date)

# Comandos
echo "Hola $NOMBRE"
echo "Fecha actual: $FECHA"
```

#### Variables en Bash

**Declaración y uso:**
```bash
#!/bin/bash

# Variables simples
SERVIDOR="web-server-01"
PUERTO=8080
AMBIENTE="production"

# Variables del sistema
USUARIO=$(whoami)
DIRECTORIO_ACTUAL=$(pwd)
FECHA_HORA=$(date '+%Y-%m-%d %H:%M:%S')

# Variables de entorno
export DATABASE_URL="postgresql://localhost:5432/app"

# Usar variables
echo "Conectando a $SERVIDOR en puerto $PUERTO"
echo "Usuario actual: $USUARIO"
echo "Directorio: $DIRECTORIO_ACTUAL"
```

**Variables especiales:**
```bash
#!/bin/bash
# $0 = nombre del script
# $1, $2, $3... = argumentos pasados al script
# $# = número de argumentos
# $@ = todos los argumentos
# $? = código de salida del último comando

echo "Script ejecutado: $0"
echo "Primer argumento: $1"
echo "Segundo argumento: $2"
echo "Total argumentos: $#"
echo "Todos los argumentos: $@"
```

#### Entrada del Usuario

```bash
#!/bin/bash

# Solicitar entrada interactiva
read -p "Ingresa el nombre del servidor: " SERVIDOR
read -s -p "Ingresa la contraseña: " PASSWORD  # -s oculta la entrada
echo  # nueva línea después de password oculta

# Con valores por defecto
read -p "Puerto [8080]: " PUERTO
PUERTO=${PUERTO:-8080}  # Usa 8080 si no se ingresa nada

echo "Configuración:"
echo "Servidor: $SERVIDOR"
echo "Puerto: $PUERTO"
```

#### Estructuras de Control

**Condicionales if-then-else:**
```bash
#!/bin/bash

ESPACIO_LIBRE=$(df / | awk 'NR==2 {print $5}' | sed 's/%//')

if [ $ESPACIO_LIBRE -gt 80 ]; then
    echo "ALERTA: Espacio en disco bajo ($ESPACIO_LIBRE%)"
    # Limpiar logs antiguos
    find /var/log -name "*.log" -mtime +7 -delete
elif [ $ESPACIO_LIBRE -gt 70 ]; then
    echo "ADVERTENCIA: Monitoreando espacio en disco ($ESPACIO_LIBRE%)"
else
    echo "Espacio en disco OK ($ESPACIO_LIBRE%)"
fi
```

**Operadores de comparación:**
```bash
# Numéricos
[ $a -eq $b ]  # igual
[ $a -ne $b ]  # no igual
[ $a -gt $b ]  # mayor que
[ $a -lt $b ]  # menor que
[ $a -ge $b ]  # mayor o igual
[ $a -le $b ]  # menor o igual

# Strings
[ "$a" = "$b" ]   # igual
[ "$a" != "$b" ]  # no igual
[ -z "$a" ]       # string vacío
[ -n "$a" ]       # string no vacío

# Archivos
[ -f "$archivo" ]  # existe y es archivo regular
[ -d "$directorio" ]  # existe y es directorio
[ -x "$programa" ]    # existe y es ejecutable
```

**Bucles for:**
```bash
#!/bin/bash

# Iterar sobre lista
SERVIDORES="web-01 web-02 web-03 db-01"

for servidor in $SERVIDORES; do
    echo "Verificando $servidor..."
    ping -c 1 $servidor > /dev/null
    if [ $? -eq 0 ]; then
        echo "✅ $servidor está activo"
    else
        echo "❌ $servidor no responde"
    fi
done

# Iterar sobre archivos
for archivo in /var/log/*.log; do
    if [ -f "$archivo" ]; then
        echo "Procesando: $archivo"
        # Comprimir logs antiguos
        gzip "$archivo"
    fi
done

# Bucle con rango numérico
for i in {1..5}; do
    echo "Intento $i de 5"
    sleep 1
done
```

**Bucles while:**
```bash
#!/bin/bash

# Monitoreo continuo
CONTADOR=0
while [ $CONTADOR -lt 10 ]; do
    CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    echo "Uso de CPU: $CPU_USAGE%"
    
    if (( $(echo "$CPU_USAGE > 80" | bc -l) )); then
        echo "ALERTA: CPU alta!"
        # Enviar notificación
    fi
    
    CONTADOR=$((CONTADOR + 1))
    sleep 5
done
```

#### Funciones en Bash

```bash
#!/bin/bash

# Definición de función
verificar_servicio() {
    local servicio=$1  # variable local
    local puerto=$2
    
    # Verificar si el servicio está corriendo
    if systemctl is-active --quiet $servicio; then
        echo "✅ $servicio está activo"
        
        # Verificar puerto
        if netstat -tulpn | grep ":$puerto " > /dev/null; then
            echo "✅ Puerto $puerto está en escucha"
            return 0
        else
            echo "❌ Puerto $puerto no está disponible"
            return 1
        fi
    else
        echo "❌ $servicio no está activo"
        return 1
    fi
}

# Función para logs con timestamp
log() {
    local mensaje=$1
    local nivel=${2:-INFO}
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$nivel] $mensaje"
}

# Función para backup con validación
backup_directorio() {
    local origen=$1
    local destino=$2
    
    # Validaciones
    if [ ! -d "$origen" ]; then
        log "Error: Directorio origen no existe: $origen" "ERROR"
        return 1
    fi
    
    if [ ! -d "$destino" ]; then
        log "Creando directorio destino: $destino" "INFO"
        mkdir -p "$destino"
    fi
    
    # Realizar backup
    log "Iniciando backup de $origen a $destino" "INFO"
    tar -czf "$destino/backup_$(date +%Y%m%d_%H%M%S).tar.gz" -C "$(dirname $origen)" "$(basename $origen)"
    
    if [ $? -eq 0 ]; then
        log "Backup completado exitosamente" "INFO"
        return 0
    else
        log "Error durante el backup" "ERROR"
        return 1
    fi
}

# Usar las funciones
verificar_servicio "nginx" "80"
verificar_servicio "mysql" "3306"

backup_directorio "/etc/nginx" "/backups"
```

#### Manejo de Errores

```bash
#!/bin/bash

# Configuración para manejo de errores
set -e  # Salir si cualquier comando falla
set -u  # Salir si se usa variable no definida
set -o pipefail  # Fallar si cualquier comando en pipe falla

# Función de limpieza
cleanup() {
    echo "Limpiando recursos..."
    # Limpiar archivos temporales
    rm -f /tmp/script_temp_*
    # Cerrar conexiones
    # Restaurar configuraciones
}

# Trap para ejecutar cleanup al salir
trap cleanup EXIT

# Función de manejo de errores
handle_error() {
    local exit_code=$?
    local line_number=$1
    echo "Error en línea $line_number. Código de salida: $exit_code"
    cleanup
    exit $exit_code
}

trap 'handle_error $LINENO' ERR

# Script principal
echo "Iniciando proceso..."

# Crear archivo temporal
TEMP_FILE="/tmp/script_temp_$$"
touch "$TEMP_FILE"

# Verificar comando crítico
if ! command -v docker &> /dev/null; then
    echo "Error: Docker no está instalado"
    exit 1
fi

echo "Proceso completado exitosamente"
```

#### Scripts Prácticos para DevOps

**Script de Monitoreo del Sistema:**
```bash
#!/bin/bash

# system_monitor.sh - Monitoreo completo del sistema

LOGFILE="/var/log/system_monitor.log"
ALERT_EMAIL="admin@company.com"

log_with_timestamp() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOGFILE"
}

check_disk_space() {
    log_with_timestamp "Verificando espacio en disco..."
    
    df -h | while read filesystem size used available percent mountpoint; do
        if [[ "$percent" =~ ^[0-9]+% ]]; then
            usage=${percent%?}  # Remover el símbolo %
            if [ $usage -gt 80 ]; then
                log_with_timestamp "ALERTA: Disco $mountpoint al $percent"
                echo "Espacio en disco crítico en $mountpoint ($percent)" | \
                    mail -s "ALERTA: Disco Lleno" "$ALERT_EMAIL"
            fi
        fi
    done
}

check_memory() {
    log_with_timestamp "Verificando memoria..."
    
    MEMORY_USAGE=$(free | grep Mem | awk '{printf("%.2f", $3/$2 * 100)}')
    if (( $(echo "$MEMORY_USAGE > 80" | bc -l) )); then
        log_with_timestamp "ALERTA: Memoria al $MEMORY_USAGE%"
    else
        log_with_timestamp "Memoria OK: $MEMORY_USAGE%"
    fi
}

check_services() {
    local services=("nginx" "mysql" "redis")
    
    log_with_timestamp "Verificando servicios críticos..."
    
    for service in "${services[@]}"; do
        if systemctl is-active --quiet $service; then
            log_with_timestamp "✅ $service está activo"
        else
            log_with_timestamp "❌ $service está inactivo - intentando reiniciar"
            systemctl restart $service
            sleep 5
            if systemctl is-active --quiet $service; then
                log_with_timestamp "✅ $service reiniciado exitosamente"
            else
                log_with_timestamp "❌ Error: No se pudo reiniciar $service"
                echo "$service falló y no se pudo reiniciar" | \
                    mail -s "ALERTA: Servicio Caído" "$ALERT_EMAIL"
            fi
        fi
    done
}

# Ejecutar verificaciones
check_disk_space
check_memory
check_services

log_with_timestamp "Monitoreo completado"
```

**Script de Deployment Automatizado:**
```bash
#!/bin/bash

# deploy.sh - Script de deployment automatizado

# Configuración
APP_NAME="mi-aplicacion"
REPO_URL="https://github.com/usuario/mi-aplicacion.git"
APP_DIR="/opt/$APP_NAME"
BACKUP_DIR="/backups/$APP_NAME"
SERVICE_NAME="mi-aplicacion"

# Colores para output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

print_status() {
    echo -e "${BLUE}[INFO]${NC} $1"
}

print_success() {
    echo -e "${GREEN}[SUCCESS]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

print_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

# Validaciones previas
pre_deployment_checks() {
    print_status "Ejecutando verificaciones previas..."
    
    # Verificar espacio en disco
    AVAILABLE_SPACE=$(df /opt | tail -1 | awk '{print $4}')
    if [ $AVAILABLE_SPACE -lt 1048576 ]; then  # 1GB en KB
        print_error "Espacio insuficiente en disco"
        exit 1
    fi
    
    # Verificar conectividad al repositorio
    if ! git ls-remote "$REPO_URL" > /dev/null 2>&1; then
        print_error "No se puede conectar al repositorio"
        exit 1
    fi
    
    # Verificar que el servicio existe
    if ! systemctl list-unit-files | grep -q "$SERVICE_NAME"; then
        print_warning "Servicio $SERVICE_NAME no encontrado"
    fi
    
    print_success "Verificaciones previas completadas"
}

# Crear backup de la versión actual
create_backup() {
    print_status "Creando backup de la versión actual..."
    
    if [ -d "$APP_DIR" ]; then
        BACKUP_NAME="$APP_NAME-$(date +%Y%m%d-%H%M%S)"
        mkdir -p "$BACKUP_DIR"
        tar -czf "$BACKUP_DIR/$BACKUP_NAME.tar.gz" -C "$(dirname $APP_DIR)" "$(basename $APP_DIR)"
        
        if [ $? -eq 0 ]; then
            print_success "Backup creado: $BACKUP_DIR/$BACKUP_NAME.tar.gz"
            echo "$BACKUP_DIR/$BACKUP_NAME.tar.gz" > /tmp/last_backup
        else
            print_error "Error creando backup"
            exit 1
        fi
    else
        print_warning "Directorio de aplicación no existe, saltando backup"
    fi
}

# Descargar nueva versión
download_new_version() {
    print_status "Descargando nueva versión..."
    
    # Crear directorio temporal
    TEMP_DIR="/tmp/deploy-$APP_NAME-$$"
    mkdir -p "$TEMP_DIR"
    
    # Clonar repositorio
    git clone "$REPO_URL" "$TEMP_DIR"
    if [ $? -ne 0 ]; then
        print_error "Error descargando repositorio"
        rm -rf "$TEMP_DIR"
        exit 1
    fi
    
    print_success "Código descargado en $TEMP_DIR"
    echo "$TEMP_DIR" > /tmp/temp_deploy_dir
}

# Detener servicios
stop_services() {
    print_status "Deteniendo servicios..."
    
    if systemctl is-active --quiet "$SERVICE_NAME"; then
        systemctl stop "$SERVICE_NAME"
        print_success "Servicio $SERVICE_NAME detenido"
    else
        print_warning "Servicio $SERVICE_NAME ya estaba detenido"
    fi
}

# Instalar nueva versión
install_new_version() {
    print_status "Instalando nueva versión..."
    
    TEMP_DIR=$(cat /tmp/temp_deploy_dir)
    
    # Remover versión antigua
    if [ -d "$APP_DIR" ]; then
        rm -rf "$APP_DIR"
    fi
    
    # Instalar nueva versión
    mv "$TEMP_DIR" "$APP_DIR"
    
    # Configurar permisos
    chown -R app:app "$APP_DIR"
    chmod +x "$APP_DIR/start.sh"
    
    print_success "Nueva versión instalada"
}

# Instalar dependencias
install_dependencies() {
    print_status "Instalando dependencias..."
    
    cd "$APP_DIR"
    
    # Ejemplo para Node.js
    if [ -f "package.json" ]; then
        npm install --production
    fi
    
    # Ejemplo para Python
    if [ -f "requirements.txt" ]; then
        pip install -r requirements.txt
    fi
    
    print_success "Dependencias instaladas"
}

# Iniciar servicios
start_services() {
    print_status "Iniciando servicios..."
    
    systemctl start "$SERVICE_NAME"
    sleep 5
    
    if systemctl is-active --quiet "$SERVICE_NAME"; then
        print_success "Servicio $SERVICE_NAME iniciado"
    else
        print_error "Error iniciando servicio $SERVICE_NAME"
        rollback_deployment
        exit 1
    fi
}

# Verificar deployment
verify_deployment() {
    print_status "Verificando deployment..."
    
    # Verificar que el servicio responde
    sleep 10
    
    # Ejemplo de verificación HTTP
    if command -v curl > /dev/null; then
        if curl -f -s "http://localhost:8080/health" > /dev/null; then
            print_success "Aplicación responde correctamente"
        else
            print_error "Aplicación no responde"
            rollback_deployment
            exit 1
        fi
    fi
}

# Rollback en caso de error
rollback_deployment() {
    print_warning "Iniciando rollback..."
    
    # Detener servicio
    systemctl stop "$SERVICE_NAME"
    
    # Restaurar backup
    if [ -f /tmp/last_backup ]; then
        BACKUP_FILE=$(cat /tmp/last_backup)
        if [ -f "$BACKUP_FILE" ]; then
            rm -rf "$APP_DIR"
            tar -xzf "$BACKUP_FILE" -C "$(dirname $APP_DIR)"
            systemctl start "$SERVICE_NAME"
            print_success "Rollback completado"
        fi
    fi
}

# Limpieza
cleanup() {
    print_status "Limpiando archivos temporales..."
    
    if [ -f /tmp/temp_deploy_dir ]; then
        TEMP_DIR=$(cat /tmp/temp_deploy_dir)
        rm -rf "$TEMP_DIR"
        rm -f /tmp/temp_deploy_dir
    fi
    
    rm -f /tmp/last_backup
    
    print_success "Limpieza completada"
}

# Función principal
main() {
    print_status "Iniciando deployment de $APP_NAME"
    
    pre_deployment_checks
    create_backup
    download_new_version
    stop_services
    install_new_version
    install_dependencies
    start_services
    verify_deployment
    cleanup
    
    print_success "Deployment completado exitosamente"
}

# Configurar trap para cleanup en caso de error
trap cleanup EXIT

# Verificar que se ejecuta como root
if [ "$EUID" -ne 0 ]; then
    print_error "Este script debe ejecutarse como root"
    exit 1
fi

# Ejecutar función principal
main
```

### PowerShell: La Potencia de Windows

#### ¿Qué es PowerShell?

**PowerShell** es un shell de línea de comandos y lenguaje de scripting desarrollado por Microsoft.

**Características clave:**
- Orientado a objetos (no solo texto)
- Multiplataforma (Windows, Linux, macOS)
- Integración profunda con .NET
- Cmdlets con sintaxis consistente (Verb-Noun)
- Excelente para automatización en entornos Windows

#### Diferencias Clave: Bash vs PowerShell

| Aspecto | Bash | PowerShell |
|---------|------|------------|
| **Filosofía** | Texto y pipes | Objetos y propiedades |
| **Sintaxis** | `command | grep pattern` | `Get-Process | Where-Object Name -eq "notepad"` |
| **Variables** | `$variable` | `$variable` |
| **Plataformas** | Linux/Unix nativo | Multiplataforma |
| **Estructurado** | Menos estructurado | Altamente estructurado |

#### Estructura Básica de PowerShell

```powershell
# Comentarios en PowerShell
# Este es un script básico de PowerShell

# Variables
$ServerName = "web-server-01"
$Port = 8080
$Environment = "Production"

# Cmdlets básicos
Write-Host "Conectando a $ServerName en puerto $Port"
Write-Output "Ambiente: $Environment"

# Obtener fecha
$CurrentDate = Get-Date
Write-Host "Fecha actual: $CurrentDate"
```

#### Variables y Tipos de Datos

```powershell
# Variables básicas
$String = "Hello DevOps"
$Number = 42
$Boolean = $true
$Array = @("web-01", "web-02", "db-01")
$HashTable = @{
    Server = "web-01"
    Port = 80
    Status = "Active"
}

# Variables automáticas importantes
Write-Host "Script ejecutado: $($MyInvocation.MyCommand.Name)"
Write-Host "Directorio actual: $PWD"
Write-Host "Usuario actual: $env:USERNAME"
Write-Host "Computadora: $env:COMPUTERNAME"

# Parámetros del script
param(
    [Parameter(Mandatory=$true)]
    [string]$ServerName,
    
    [Parameter(Mandatory=$false)]
    [int]$Port = 80,
    
    [Parameter(Mandatory=$false)]
    [switch]$Verbose
)

Write-Host "Servidor: $ServerName"
Write-Host "Puerto: $Port"
if ($Verbose) {
    Write-Host "Modo verbose activado"
}
```

#### Entrada del Usuario

```powershell
# Solicitar entrada básica
$ServerName = Read-Host "Ingresa el nombre del servidor"

# Solicitar contraseña (oculta)
$SecurePassword = Read-Host "Ingresa la contraseña" -AsSecureString
$Password = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($SecurePassword))

# Con validación
do {
    $Port = Read-Host "Ingresa el puerto (80-65535)"
} while ($Port -lt 80 -or $Port -gt 65535)

# Confirmación
$Confirmation = Read-Host "¿Continuar con el deployment? (Y/N)"
if ($Confirmation -eq 'Y' -or $Confirmation -eq 'y') {
    Write-Host "Continuando..."
} else {
    Write-Host "Operación cancelada"
    exit
}
```

#### Estructuras de Control

**Condicionales:**
```powershell
# If-ElseIf-Else
$FreeSpace = (Get-WmiObject -Class Win32_LogicalDisk -Filter "DeviceID='C:'" | Select-Object -ExpandProperty FreeSpace) / 1GB

if ($FreeSpace -lt 2) {
    Write-Warning "ALERTA: Poco espacio en disco ($($FreeSpace.ToString('F2')) GB)"
    # Limpiar archivos temporales
    Get-ChildItem -Path $env:TEMP -Recurse | Remove-Item -Force -Recurse
} elseif ($FreeSpace -lt 5) {
    Write-Warning "ADVERTENCIA: Monitoreando espacio en disco ($($FreeSpace.ToString('F2')) GB)"
} else {
    Write-Host "Espacio en disco OK ($($FreeSpace.ToString('F2')) GB)" -ForegroundColor Green
}

# Switch
$ServiceStatus = (Get-Service -Name "IIS").Status
switch ($ServiceStatus) {
    "Running" { 
        Write-Host "IIS está corriendo" -ForegroundColor Green 
    }
    "Stopped" { 
        Write-Host "IIS está detenido - iniciando..." -ForegroundColor Yellow
        Start-Service -Name "IIS"
    }
    "StartPending" { 
        Write-Host "IIS está iniciando..." -ForegroundColor Yellow 
    }
    default { 
        Write-Host "Estado desconocido: $ServiceStatus" -ForegroundColor Red 
    }
}
```

**Bucles:**
```powershell
# ForEach-Object (pipeline)
$Servers = @("web-01", "web-02", "db-01")

$Servers | ForEach-Object {
    $Server = $_
    Write-Host "Verificando $Server..." -ForegroundColor Blue
    
    if (Test-Connection -ComputerName $Server -Count 1 -Quiet) {
        Write-Host "✅ $Server está activo" -ForegroundColor Green
    } else {
        Write-Host "❌ $Server no responde" -ForegroundColor Red
    }
}

# Foreach tradicional
foreach ($Server in $Servers) {
    Write-Host "Procesando $Server"
    # Lógica aquí
}

# For loop
for ($i = 1; $i -le 5; $i++) {
    Write-Host "Intento $i de 5"
    Start-Sleep -Seconds 1
}

# While loop
$Counter = 0
while ($Counter -lt 10) {
    $CpuUsage = (Get-Counter "\Processor(_Total)\% Processor Time").CounterSamples.CookedValue
    Write-Host "Uso de CPU: $($CpuUsage.ToString('F2'))%"
    
    if ($CpuUsage -gt 80) {
        Write-Warning "ALERTA: CPU alta!"
    }
    
    $Counter++
    Start-Sleep -Seconds 5
}
```

#### Funciones en PowerShell

```powershell
# Función básica
function Test-ServiceHealth {
    param(
        [Parameter(Mandatory=$true)]
        [string]$ServiceName,
        
        [Parameter(Mandatory=$false)]
        [int]$Port
    )
    
    try {
        $Service = Get-Service -Name $ServiceName -ErrorAction Stop
        
        if ($Service.Status -eq "Running") {
            Write-Host "✅ $ServiceName está activo" -ForegroundColor Green
            
            # Verificar puerto si se proporciona
            if ($Port) {
                $PortTest = Test-NetConnection -ComputerName "localhost" -Port $Port -WarningAction SilentlyContinue
                if ($PortTest.TcpTestSucceeded) {
                    Write-Host "✅ Puerto $Port está disponible" -ForegroundColor Green
                    return $true
                } else {
                    Write-Host "❌ Puerto $Port no está disponible" -ForegroundColor Red
                    return $false
                }
            }
            return $true
        } else {
            Write-Host "❌ $ServiceName no está activo" -ForegroundColor Red
            return $false
        }
    }
    catch {
        Write-Host "❌ Servicio $ServiceName no encontrado" -ForegroundColor Red
        return $false
    }
}

# Función avanzada con logging
function Write-Log {
    param(
        [Parameter(Mandatory=$true)]
        [string]$Message,
        
        [Parameter(Mandatory=$false)]
        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$Level = "INFO",
        
        [Parameter(Mandatory=$false)]
        [string]$LogFile = "C:\Logs\script.log"
    )
    
    $Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $LogEntry = "[$Timestamp] [$Level] $Message"
    
    # Crear directorio si no existe
    $LogDir = Split-Path $LogFile -Parent
    if (!(Test-Path $LogDir)) {
        New-Item -ItemType Directory -Path $LogDir -Force
    }
    
    # Escribir a archivo y consola
    Add-Content -Path $LogFile -Value $LogEntry
    
    switch ($Level) {
        "INFO" { Write-Host $LogEntry -ForegroundColor White }
        "WARNING" { Write-Host $LogEntry -ForegroundColor Yellow }
        "ERROR" { Write-Host $LogEntry -ForegroundColor Red }
    }
}

# Función para backup con validación
function Backup-Directory {
    param(
        [Parameter(Mandatory=$true)]
        [string]$SourcePath,
        
        [Parameter(Mandatory=$true)]
        [string]$BackupPath,
        
        [Parameter(Mandatory=$false)]
        [switch]$Compress
    )
    
    # Validaciones
    if (!(Test-Path $SourcePath)) {
        Write-Log -Message "Error: Directorio origen no existe: $SourcePath" -Level "ERROR"
        return $false
    }
    
    if (!(Test-Path $BackupPath)) {
        Write-Log -Message "Creando directorio destino: $BackupPath" -Level "INFO"
        New-Item -ItemType Directory -Path $BackupPath -Force
    }
    
    try {
        $BackupName = "backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
        $FullBackupPath = Join-Path $BackupPath $BackupName
        
        Write-Log -Message "Iniciando backup de $SourcePath a $FullBackupPath" -Level "INFO"
        
        if ($Compress) {
            $ZipPath = "$FullBackupPath.zip"
            Compress-Archive -Path $SourcePath -DestinationPath $ZipPath -Force
            Write-Log -Message "Backup comprimido creado: $ZipPath" -Level "INFO"
        } else {
            Copy-Item -Path $SourcePath -Destination $FullBackupPath -Recurse -Force
            Write-Log -Message "Backup creado: $FullBackupPath" -Level "INFO"
        }
        
        return $true
    }
    catch {
        Write-Log -Message "Error durante el backup: $($_.Exception.Message)" -Level "ERROR"
        return $false
    }
}

# Usar las funciones
Test-ServiceHealth -ServiceName "IIS" -Port 80
Test-ServiceHealth -ServiceName "MSSQLSERVER" -Port 1433

Backup-Directory -SourcePath "C:\inetpub\wwwroot" -BackupPath "C:\Backups" -Compress
```

#### Manejo de Errores

```powershell
# Configuración global de manejo de errores
$ErrorActionPreference = "Stop"  # Detener en cualquier error

# Try-Catch-Finally
function Deploy-Application {
    param(
        [string]$AppPath,
        [string]$ServiceName
    )
    
    try {
        Write-Log -Message "Iniciando deployment..." -Level "INFO"
        
        # Detener servicio
        if (Get-Service $ServiceName -ErrorAction SilentlyContinue) {
            Stop-Service -Name $ServiceName -Force
            Write-Log -Message "Servicio $ServiceName detenido" -Level "INFO"
        }
        
        # Simular error para demostrar manejo
        if (!(Test-Path $AppPath)) {
            throw "Aplicación no encontrada en $AppPath"
        }
        
        # Copiar archivos
        Copy-Item -Path $AppPath -Destination "C:\Apps\" -Recurse -Force
        
        # Iniciar servicio
        Start-Service -Name $ServiceName
        
        Write-Log -Message "Deployment completado exitosamente" -Level "INFO"
    }
    catch [System.ServiceProcess.ServiceCommandException] {
        Write-Log -Message "Error con el servicio: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
    catch [System.IO.FileNotFoundException] {
        Write-Log -Message "Archivo no encontrado: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
    catch {
        Write-Log -Message "Error inesperado: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
    finally {
        Write-Log -Message "Limpiando recursos..." -Level "INFO"
        # Limpieza aquí
    }
}
```

#### Scripts Prácticos para DevOps

**Script de Monitoreo del Sistema:**
```powershell
# SystemMonitor.ps1 - Monitoreo completo del sistema Windows

param(
    [string]$LogPath = "C:\Logs\SystemMonitor.log",
    [string]$AlertEmail = "admin@company.com",
    [int]$DiskThreshold = 80,
    [int]$MemoryThreshold = 80,
    [int]$CpuThreshold = 80
)

# Importar módulos necesarios
Import-Module -Name "Microsoft.PowerShell.Management" -Force

function Send-AlertEmail {
    param(
        [string]$Subject,
        [string]$Body,
        [string]$To
    )
    
    try {
        $SMTPServer = "smtp.company.com"
        $SMTPPort = 587
        $Username = "monitoring@company.com"
        $SecurePassword = ConvertTo-SecureString "password" -AsPlainText -Force
        $Credential = New-Object System.Management.Automation.PSCredential ($Username, $SecurePassword)
        
        Send-MailMessage -To $To -Subject $Subject -Body $Body -SmtpServer $SMTPServer -Port $SMTPPort -Credential $Credential -UseSsl
        Write-Log -Message "Email enviado: $Subject" -Level "INFO"
    }
    catch {
        Write-Log -Message "Error enviando email: $($_.Exception.Message)" -Level "ERROR"
    }
}

function Test-DiskSpace {
    Write-Log -Message "Verificando espacio en disco..." -Level "INFO"
    
    $Disks = Get-WmiObject -Class Win32_LogicalDisk | Where-Object { $_.DriveType -eq 3 }
    
    foreach ($Disk in $Disks) {
        $UsedPercent = [math]::Round((($Disk.Size - $Disk.FreeSpace) / $Disk.Size) * 100, 2)
        $FreeSpaceGB = [math]::Round($Disk.FreeSpace / 1GB, 2)
        
        if ($UsedPercent -gt $DiskThreshold) {
            $AlertMessage = "ALERTA: Disco $($Disk.DeviceID) al $UsedPercent% ($FreeSpaceGB GB libres)"
            Write-Log -Message $AlertMessage -Level "ERROR"
            Send-AlertEmail -Subject "ALERTA: Disco Lleno" -Body $AlertMessage -To $AlertEmail
        } else {
            Write-Log -Message "Disco $($Disk.DeviceID): $UsedPercent% usado ($FreeSpaceGB GB libres)" -Level "INFO"
        }
    }
}

function Test-MemoryUsage {
    Write-Log -Message "Verificando memoria..." -Level "INFO"
    
    $OS = Get-WmiObject -Class Win32_OperatingSystem
    $TotalMemory = [math]::Round($OS.TotalVisibleMemorySize / 1MB, 2)
    $FreeMemory = [math]::Round($OS.FreePhysicalMemory / 1MB, 2)
    $UsedPercent = [math]::Round((($TotalMemory - $FreeMemory) / $TotalMemory) * 100, 2)
    
    if ($UsedPercent -gt $MemoryThreshold) {
        $AlertMessage = "ALERTA: Memoria al $UsedPercent% ($FreeMemory GB libres de $TotalMemory GB)"
        Write-Log -Message $AlertMessage -Level "ERROR"
        Send-AlertEmail -Subject "ALERTA: Memoria Alta" -Body $AlertMessage -To $AlertEmail
    } else {
        Write-Log -Message "Memoria OK: $UsedPercent% usado ($FreeMemory GB libres de $TotalMemory GB)" -Level "INFO"
    }
}

function Test-CpuUsage {
    Write-Log -Message "Verificando CPU..." -Level "INFO"
    
    # Obtener múltiples muestras para mayor precisión
    $CpuSamples = @()
    for ($i = 0; $i -lt 3; $i++) {
        $CpuSamples += (Get-Counter "\Processor(_Total)\% Processor Time").CounterSamples.CookedValue
        Start-Sleep -Seconds 2
    }
    
    $AvgCpuUsage = [math]::Round(($CpuSamples | Measure-Object -Average).Average, 2)
    
    if ($AvgCpuUsage -gt $CpuThreshold) {
        $AlertMessage = "ALERTA: CPU al $AvgCpuUsage%"
        Write-Log -Message $AlertMessage -Level "ERROR"
        Send-AlertEmail -Subject "ALERTA: CPU Alta" -Body $AlertMessage -To $AlertEmail
    } else {
        Write-Log -Message "CPU OK: $AvgCpuUsage%" -Level "INFO"
    }
}

function Test-CriticalServices {
    $CriticalServices = @("IIS", "MSSQLSERVER", "W3SVC")
    
    Write-Log -Message "Verificando servicios críticos..." -Level "INFO"
    
    foreach ($ServiceName in $CriticalServices) {
        try {
            $Service = Get-Service -Name $ServiceName -ErrorAction Stop
            
            if ($Service.Status -eq "Running") {
                Write-Log -Message "✅ $ServiceName está activo" -Level "INFO"
            } else {
                Write-Log -Message "❌ $ServiceName está inactivo - intentando reiniciar" -Level "WARNING"
                
                Start-Service -Name $ServiceName
                Start-Sleep -Seconds 10
                
                $Service = Get-Service -Name $ServiceName
                if ($Service.Status -eq "Running") {
                    Write-Log -Message "✅ $ServiceName reiniciado exitosamente" -Level "INFO"
                } else {
                    $AlertMessage = "❌ Error: No se pudo reiniciar $ServiceName"
                    Write-Log -Message $AlertMessage -Level "ERROR"
                    Send-AlertEmail -Subject "ALERTA: Servicio Caído" -Body $AlertMessage -To $AlertEmail
                }
            }
        }
        catch {
            Write-Log -Message "❌ Servicio $ServiceName no encontrado" -Level "WARNING"
        }
    }
}

# Ejecutar todas las verificaciones
Write-Log -Message "=== Iniciando monitoreo del sistema ===" -Level "INFO"

Test-DiskSpace
Test-MemoryUsage
Test-CpuUsage
Test-CriticalServices

Write-Log -Message "=== Monitoreo completado ===" -Level "INFO"
```

**Script de Deployment Automatizado:**
```powershell
# Deploy.ps1 - Script de deployment automatizado

[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string]$AppName,
    
    [Parameter(Mandatory=$true)]
    [string]$SourcePath,
    
    [Parameter(Mandatory=$false)]
    [string]$TargetPath = "C:\Apps",
    
    [Parameter(Mandatory=$false)]
    [string]$ServiceName,
    
    [Parameter(Mandatory=$false)]
    [string]$BackupPath = "C:\Backups",
    
    [Parameter(Mandatory=$false)]
    [switch]$SkipTests
)

# Variables globales
$DeploymentStartTime = Get-Date
$LogFile = "C:\Logs\Deploy_$AppName_$(Get-Date -Format 'yyyyMMdd_HHmmss').log"

function Write-DeployLog {
    param([string]$Message, [string]$Level = "INFO")
    Write-Log -Message $Message -Level $Level -LogFile $LogFile
}

function Test-PreDeploymentConditions {
    Write-DeployLog -Message "Ejecutando verificaciones previas..." -Level "INFO"
    
    # Verificar espacio en disco
    $TargetDisk = Split-Path $TargetPath -Qualifier
    $DiskInfo = Get-WmiObject -Class Win32_LogicalDisk | Where-Object { $_.DeviceID -eq $TargetDisk }
    $FreeSpaceGB = [math]::Round($DiskInfo.FreeSpace / 1GB, 2)
    
    if ($FreeSpaceGB -lt 2) {
        Write-DeployLog -Message "Error: Espacio insuficiente en disco ($FreeSpaceGB GB libres)" -Level "ERROR"
        throw "Espacio insuficiente en disco"
    }
    
    # Verificar que el origen existe
    if (!(Test-Path $SourcePath)) {
        Write-DeployLog -Message "Error: Ruta de origen no existe: $SourcePath" -Level "ERROR"
        throw "Ruta de origen no válida"
    }
    
    # Verificar permisos de escritura
    try {
        $TestFile = Join-Path $TargetPath "deploy_test_$(Get-Random).tmp"
        New-Item -Path $TestFile -ItemType File -Force | Out-Null
        Remove-Item $TestFile -Force
    }
    catch {
        Write-DeployLog -Message "Error: Sin permisos de escritura en $TargetPath" -Level "ERROR"
        throw "Permisos insuficientes"
    }
    
    Write-DeployLog -Message "Verificaciones previas completadas" -Level "INFO"
}

function New-BackupOfCurrentVersion {
    $AppTargetPath = Join-Path $TargetPath $AppName
    
    if (Test-Path $AppTargetPath) {
        Write-DeployLog -Message "Creando backup de la versión actual..." -Level "INFO"
        
        $BackupName = "$AppName-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
        $FullBackupPath = Join-Path $BackupPath "$BackupName.zip"
        
        try {
            # Crear directorio de backup si no existe
            if (!(Test-Path $BackupPath)) {
                New-Item -ItemType Directory -Path $BackupPath -Force
            }
            
            Compress-Archive -Path $AppTargetPath -DestinationPath $FullBackupPath -Force
            Write-DeployLog -Message "Backup creado: $FullBackupPath" -Level "INFO"
            
            # Guardar ruta del backup para posible rollback
            $Global:LastBackupPath = $FullBackupPath
            return $true
        }
        catch {
            Write-DeployLog -Message "Error creando backup: $($_.Exception.Message)" -Level "ERROR"
            throw "Error en backup"
        }
    } else {
        Write-DeployLog -Message "Primera instalación, saltando backup" -Level "INFO"
        return $true
    }
}

function Stop-ApplicationServices {
    if ($ServiceName) {
        Write-DeployLog -Message "Deteniendo servicio $ServiceName..." -Level "INFO"
        
        try {
            $Service = Get-Service -Name $ServiceName -ErrorAction Stop
            
            if ($Service.Status -eq "Running") {
                Stop-Service -Name $ServiceName -Force -TimeoutSec 30
                
                # Esperar confirmación
                $Timeout = 30
                $Counter = 0
                do {
                    Start-Sleep -Seconds 1
                    $Service = Get-Service -Name $ServiceName
                    $Counter++
                } while ($Service.Status -eq "Running" -and $Counter -lt $Timeout)
                
                if ($Service.Status -eq "Stopped") {
                    Write-DeployLog -Message "Servicio $ServiceName detenido exitosamente" -Level "INFO"
                } else {
                    Write-DeployLog -Message "Timeout deteniendo servicio $ServiceName" -Level "WARNING"
                }
            } else {
                Write-DeployLog -Message "Servicio $ServiceName ya estaba detenido" -Level "INFO"
            }
        }
        catch {
            Write-DeployLog -Message "Error deteniendo servicio: $($_.Exception.Message)" -Level "ERROR"
            throw
        }
    }
}

function Install-NewVersion {
    Write-DeployLog -Message "Instalando nueva versión..." -Level "INFO"
    
    $AppTargetPath = Join-Path $TargetPath $AppName
    
    try {
        # Remover versión anterior si existe
        if (Test-Path $AppTargetPath) {
            Remove-Item -Path $AppTargetPath -Recurse -Force
        }
        
        # Copiar nueva versión
        Copy-Item -Path $SourcePath -Destination $AppTargetPath -Recurse -Force
        
        Write-DeployLog -Message "Nueva versión instalada en $AppTargetPath" -Level "INFO"
        
        # Configurar permisos específicos si es necesario
        # icacls $AppTargetPath /grant "IIS_IUSRS:(OI)(CI)F" /T
        
        return $true
    }
    catch {
        Write-DeployLog -Message "Error instalando nueva versión: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
}

function Install-Dependencies {
    $AppTargetPath = Join-Path $TargetPath $AppName
    
    Write-DeployLog -Message "Instalando dependencias..." -Level "INFO"
    
    Push-Location $AppTargetPath
    
    try {
        # Verificar si hay package.json (Node.js)
        if (Test-Path "package.json") {
            Write-DeployLog -Message "Detectado proyecto Node.js, ejecutando npm install..." -Level "INFO"
            & npm install --production
            if ($LASTEXITCODE -ne 0) {
                throw "Error ejecutando npm install"
            }
        }
        
        # Verificar si hay requirements.txt (Python)
        if (Test-Path "requirements.txt") {
            Write-DeployLog -Message "Detectado proyecto Python, ejecutando pip install..." -Level "INFO"
            & pip install -r requirements.txt
            if ($LASTEXITCODE -ne 0) {
                throw "Error ejecutando pip install"
            }
        }
        
        # Verificar si hay packages.config (.NET)
        if (Test-Path "packages.config") {
            Write-DeployLog -Message "Detectado proyecto .NET, restaurando paquetes..." -Level "INFO"
            & nuget restore
            if ($LASTEXITCODE -ne 0) {
                throw "Error restaurando paquetes NuGet"
            }
        }
        
        Write-DeployLog -Message "Dependencias instaladas correctamente" -Level "INFO"
    }
    catch {
        Write-DeployLog -Message "Error instalando dependencias: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
    finally {
        Pop-Location
    }
}

function Start-ApplicationServices {
    if ($ServiceName) {
        Write-DeployLog -Message "Iniciando servicio $ServiceName..." -Level "INFO"
        
        try {
            Start-Service -Name $ServiceName
            
            # Esperar confirmación
            $Timeout = 60
            $Counter = 0
            do {
                Start-Sleep -Seconds 2
                $Service = Get-Service -Name $ServiceName
                $Counter += 2
            } while ($Service.Status -ne "Running" -and $Counter -lt $Timeout)
            
            if ($Service.Status -eq "Running") {
                Write-DeployLog -Message "Servicio $ServiceName iniciado exitosamente" -Level "INFO"
            } else {
                throw "Timeout iniciando servicio $ServiceName"
            }
        }
        catch {
            Write-DeployLog -Message "Error iniciando servicio: $($_.Exception.Message)" -Level "ERROR"
            throw
        }
    }
}

function Test-DeploymentHealth {
    if ($SkipTests) {
        Write-DeployLog -Message "Saltando pruebas de salud (SkipTests especificado)" -Level "INFO"
        return $true
    }
    
    Write-DeployLog -Message "Verificando salud del deployment..." -Level "INFO"
    
    Start-Sleep -Seconds 10  # Dar tiempo para que la aplicación inicie
    
    try {
        # Verificar servicio si está definido
        if ($ServiceName) {
            $Service = Get-Service -Name $ServiceName
            if ($Service.Status -ne "Running") {
                throw "Servicio $ServiceName no está corriendo"
            }
        }
        
        # Verificar endpoint HTTP si es una aplicación web
        $ConfigFile = Join-Path (Join-Path $TargetPath $AppName) "web.config"
        if (Test-Path $ConfigFile) {
            try {
                $Response = Invoke-WebRequest -Uri "http://localhost" -TimeoutSec 30 -UseBasicParsing
                if ($Response.StatusCode -eq 200) {
                    Write-DeployLog -Message "Aplicación web responde correctamente (HTTP 200)" -Level "INFO"
                }
            }
            catch {
                Write-DeployLog -Message "Advertencia: No se pudo verificar endpoint HTTP: $($_.Exception.Message)" -Level "WARNING"
            }
        }
        
        Write-DeployLog -Message "Verificación de salud completada" -Level "INFO"
        return $true
    }
    catch {
        Write-DeployLog -Message "Error en verificación de salud: $($_.Exception.Message)" -Level "ERROR"
        throw
    }
}

function Invoke-RollbackDeployment {
    Write-DeployLog -Message "Iniciando rollback..." -Level "WARNING"
    
    try {
        # Detener servicio
        if ($ServiceName) {
            Stop-Service -Name $ServiceName -Force -ErrorAction SilentlyContinue
        }
        
        # Restaurar backup si existe
        if ($Global:LastBackupPath -and (Test-Path $Global:LastBackupPath)) {
            $AppTargetPath = Join-Path $TargetPath $AppName
            
            if (Test-Path $AppTargetPath) {
                Remove-Item -Path $AppTargetPath -Recurse -Force
            }
            
            Expand-Archive -Path $Global:LastBackupPath -DestinationPath (Split-Path $AppTargetPath -Parent) -Force
            
            # Reiniciar servicio
            if ($ServiceName) {
                Start-Service -Name $ServiceName
            }
            
            Write-DeployLog -Message "Rollback completado exitosamente" -Level "INFO"
        } else {
            Write-DeployLog -Message "No hay backup disponible para rollback" -Level "ERROR"
        }
    }
    catch {
        Write-DeployLog -Message "Error durante rollback: $($_.Exception.Message)" -Level "ERROR"
    }
}

function Remove-TemporaryFiles {
    Write-DeployLog -Message "Limpiando archivos temporales..." -Level "INFO"
    
    # Limpiar archivos temporales específicos del deployment
    $TempFiles = Get-ChildItem -Path $env:TEMP -Filter "*deploy*" -ErrorAction SilentlyContinue
    $TempFiles | Remove-Item -Force -Recurse -ErrorAction SilentlyContinue
    
    Write-DeployLog -Message "Limpieza completada" -Level "INFO"
}

# Función principal
function Invoke-Deployment {
    try {
        Write-DeployLog -Message "=== Iniciando deployment de $AppName ===" -Level "INFO"
        
        Test-PreDeploymentConditions
        New-BackupOfCurrentVersion
        Stop-ApplicationServices
        Install-NewVersion
        Install-Dependencies
        Start-ApplicationServices
        Test-DeploymentHealth
        Remove-TemporaryFiles
        
        $DeploymentDuration = (Get-Date) - $DeploymentStartTime
        Write-DeployLog -Message "=== Deployment completado exitosamente en $($DeploymentDuration.TotalMinutes.ToString('F2')) minutos ===" -Level "INFO"
        
        return $true
    }
    catch {
        Write-DeployLog -Message "Error durante deployment: $($_.Exception.Message)" -Level "ERROR"
        Invoke-RollbackDeployment
        return $false
    }
}

# Verificar permisos de administrador
if (-NOT ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) {
    Write-Error "Este script requiere permisos de administrador. Ejecuta PowerShell como administrador."
    exit 1
}

# Ejecutar deployment
$Result = Invoke-Deployment

if ($Result) {
    Write-Host "Deployment exitoso" -ForegroundColor Green
    exit 0
} else {
    Write-Host "Deployment falló" -ForegroundColor Red
    exit 1
}
```

### Mejores Prácticas para Scripting en DevOps

#### 1. Idempotencia
```bash
# Bash - Idempotente
if ! grep -q "max_connections = 1000" /etc/mysql/my.cnf; then
    echo "max_connections = 1000" >> /etc/mysql/my.cnf
fi
```

```powershell
# PowerShell - Idempotente
$ConfigFile = "C:\App\config.ini"
$Setting = "MaxConnections=1000"

if (!(Get-Content $ConfigFile | Select-String $Setting)) {
    Add-Content -Path $ConfigFile -Value $Setting
}
```

#### 2. Logging Estructurado
```bash
#!/bin/bash
# Logging con diferentes niveles
log() {
    local level=$1
    shift
    local message="$@"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $message" | tee -a /var/log/script.log
}

log "INFO" "Iniciando proceso"
log "ERROR" "Falló la conexión"
log "DEBUG" "Variable X = $X"
```

#### 3. Configuración Externa
```bash
#!/bin/bash
# Cargar configuración desde archivo
CONFIG_FILE="${CONFIG_FILE:-/etc/script/config.conf}"

if [ -f "$CONFIG_FILE" ]; then
    source "$CONFIG_FILE"
else
    echo "Error: Archivo de configuración no encontrado: $CONFIG_FILE"
    exit 1
fi

echo "Servidor: $SERVER_NAME"
echo "Puerto: $PORT"
```

#### 4. Validación de Parámetros
```powershell
param(
    [Parameter(Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$ServerName,
    
    [Parameter(Mandatory=$false)]
    [ValidateRange(1, 65535)]
    [int]$Port = 80,
    
    [Parameter(Mandatory=$false)]
    [ValidateSet("Development", "Staging", "Production")]
    [string]$Environment = "Development"
)
```

#### 5. Manejo Seguro de Credenciales
```bash
#!/bin/bash
# Nunca hardcodear credenciales
DB_PASSWORD="${DB_PASSWORD:-$(cat /etc/secrets/db_password)}"

# Usar variables de entorno
if [ -z "$DB_PASSWORD" ]; then
    echo "Error: DB_PASSWORD no está definida"
    exit 1
fi
```

### Herramientas y Recursos

#### Editores Recomendados
- **Visual Studio Code:** Con extensiones para Bash y PowerShell
- **PowerShell ISE:** Específico para PowerShell en Windows
- **Vim/Nano:** Para edición rápida en servidores

#### Debuggers y Testing
```bash
#!/bin/bash
# Debug en Bash
set -x  # Mostrar comandos ejecutados
set -e  # Salir en error
set -u  # Error si variable no definida
```

```powershell
# Debug en PowerShell
Set-PSDebug -Trace 1  # Mostrar comandos ejecutados
$DebugPreference = "Continue"  # Mostrar mensajes de debug
Write-Debug "Variable X = $X"
```

#### Linters y Analizadores
- **ShellCheck:** Para scripts Bash
- **PSScriptAnalyzer:** Para scripts PowerShell

### Integración con CI/CD

#### Ejemplo de uso en pipeline
```yaml
# .github/workflows/deploy.yml
name: Deploy Application

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Run deployment script
      run: |
        chmod +x ./scripts/deploy.sh
        ./scripts/deploy.sh production
      env:
        DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

## Fuentes Relevantes:
- **Documentación oficial:** [Bash Manual](https://www.gnu.org/software/bash/manual/)
- **PowerShell Docs:** [Microsoft PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)
- **Mejores prácticas:** [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html)
- **Libro recomendado:** "Learning the bash Shell" - Cameron Newham

## Videos Sugeridos:
- **"Bash Scripting Full Course"** - freeCodeCamp
- **"PowerShell Master Class"** - John Savill
- **"Scripting para DevOps"** - Pelado Nerd
- **"Automation with Bash and PowerShell"** - TechWorld with Nana

## Ejercicios Prácticos:

### Ejercicio 1: Script de Mantenimiento Básico
**Crear un script que:**
- Verifique espacio en disco
- Limpie logs antiguos (más de 7 días)
- Reinicie un servicio específico
- Envíe un reporte por email

### Ejercicio 2: Script de Backup Automatizado
**Desarrollar un script que:**
- Haga backup de múltiples directorios
- Comprima los archivos
- Mantenga solo los últimos 5 backups
- Verifique la integridad del backup

### Ejercicio 3: Script de Deployment Simple
**Implementar un script que:**
- Descargue código desde Git
- Detenga la aplicación
- Actualice los archivos
- Reinicie la aplicación
- Verifique que funciona correctamente

## Resumen y Próximos Pasos:

### 🎯 **Conceptos clave dominados en esta clase:**

**Fundamentos del scripting para DevOps:**
- **Automatización esencial:** Eliminación de tareas manuales repetitivas y propensas a errores
- **Bash para Linux:** Dominio del shell más utilizado en entornos DevOps para automatización de sistemas
- **PowerShell multiplataforma:** Scripting orientado a objetos para administración moderna de infraestructura
- **Estructuras de control:** Condicionales, bucles y funciones para lógica compleja de automatización
- **Manejo de errores:** Implementación robusta de validaciones y recuperación ante fallos
- **Logging estructurado:** Trazabilidad completa de operaciones para troubleshooting eficiente
- **Idempotencia:** Scripts que pueden ejecutarse múltiples veces sin efectos secundarios
- **Seguridad:** Manejo seguro de credenciales y validación de entrada de datos

**Habilidades prácticas desarrolladas:**
- Creación de scripts de monitoreo del sistema con alertas automatizadas
- Desarrollo de procesos de deployment con rollback automático
- Implementación de backups automatizados con rotación y verificación
- Integración de scripts con pipelines de CI/CD para automatización completa

**Impacto en el flujo DevOps:**
- **Consistencia:** Los procesos se ejecutan exactamente igual cada vez
- **Velocidad:** Reducción dramática en tiempo de deployment y mantenimiento  
- **Confiabilidad:** Menor probabilidad de errores humanos en operaciones críticas
- **Escalabilidad:** Capacidad de gestionar múltiples servidores simultáneamente
- **Documentación viviente:** Los scripts sirven como documentación ejecutable de procesos

### 🚀 **Próxima clase:** 
**"Introducción a la Nube (AWS/Azure/GCP)"** donde aprenderás los fundamentos de cloud computing y cómo los principales proveedores de nube (Amazon Web Services, Microsoft Azure y Google Cloud Platform) revolucionan la infraestructura moderna. Exploraremos servicios core como compute, storage, networking y databases, además de cómo migrar workloads tradicionales al cloud y aprovechar servicios managed para acelerar el desarrollo.

### 💡 **Conexión con el próximo módulo:**
Los scripts que desarrollaste en esta clase serán fundamentales para:
- **Automatizar deployments en la nube** usando APIs de proveedores cloud
- **Gestionar infraestructura como código** con herramientas como Terraform
- **Configurar recursos automáticamente** mediante Ansible y otros tools de configuración
- **Integrar con servicios managed** como databases, queues y storage en la nube
- **Implementar CI/CD pipelines** que utilicen tanto scripts locales como servicios cloud

La base sólida en scripting que ahora dominas te permitirá maximizar el potencial de automatización que ofrece la nube, creando workflows verdaderamente eficientes y escalables.