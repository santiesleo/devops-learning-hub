## Clase 3: Linux Básico para DevOps - Dominando la Terminal

**Módulo:** 1  
**Temática del Módulo:** Introducción a los fundamentos de DevOps  
**Tema Específico:** Sistemas operativos Linux y línea de comandos

### Contenido de la Clase:

#### ¿Por qué Linux en DevOps? (La Analogía del Mecánico)

Imagina que eres mecánico. Puedes:
- **Opción A:** Usar herramientas automáticas que hacen todo por ti, pero no sabes cómo funcionan
- **Opción B:** Dominar herramientas manuales precisas que te dan control total

**Linux es la "Opción B" para DevOps:**
- Control total sobre el sistema
- Eficiencia y velocidad
- Automatización poderosa
- Estabilidad para servidores

**Estadísticas importantes:**
- 96% de los servidores web usan Linux
- 100% de los top 500 supercomputadores usan Linux
- Todas las principales plataformas cloud son Linux

#### ¿Qué es Linux?

**Linux** es un sistema operativo **libre y de código abierto** basado en Unix.

**Características clave:**
- **Multiusuario:** Varios usuarios simultáneamente
- **Multitarea:** Múltiples procesos corriendo
- **Estable:** Puede funcionar meses sin reiniciar
- **Seguro:** Permisos granulares de archivos
- **Eficiente:** Usa pocos recursos

**Distribuciones populares:**
- **Ubuntu:** Fácil para principiantes
- **CentOS/RHEL:** Popular en empresas
- **Amazon Linux:** Optimizado para AWS
- **Alpine:** Muy ligero para contenedores

#### La Terminal: Tu Nueva Mejor Amiga

**¿Qué es la terminal?**
Es una interfaz de texto que te permite comunicarte directamente con el sistema operativo.

**¿Por qué usar terminal en lugar de interfaz gráfica?**
1. **Velocidad:** Más rápido que hacer clic en menús
2. **Automatización:** Los comandos se pueden scripting
3. **Precisión:** Control exacto de lo que haces
4. **Recursos:** Consume menos memoria y CPU
5. **Remoto:** Funciona sobre conexiones SSH

#### Estructura del Sistema de Archivos Linux

```
/                    (Root - raíz del sistema)
├── bin/             (Binarios/ejecutables básicos)
├── etc/             (Archivos de configuración)
├── home/            (Directorios de usuarios)
│   └── usuario/     (Tu directorio personal)
├── var/             (Archivos variables - logs, datos)
├── tmp/             (Archivos temporales)
├── usr/             (Programas de usuario)
└── opt/             (Software opcional)
```

**Diferencias con Windows:**
- No hay drives C:, D: - todo es un solo árbol
- Separador de directorios: `/` en lugar de `\`
- Case-sensitive: `archivo.txt` ≠ `Archivo.txt`

#### Comandos Fundamentales de Linux

##### Navegación:
```bash
pwd                  # ¿Dónde estoy? (Print Working Directory)
ls                   # Listar archivos
ls -la               # Listar con detalles (incluso ocultos)
cd /home/usuario     # Cambiar directorio
cd ..                # Subir un nivel
cd ~                 # Ir al directorio home
```

##### Manipulación de archivos:
```bash
touch archivo.txt    # Crear archivo vacío
mkdir carpeta        # Crear directorio
cp origen destino    # Copiar archivo
mv archivo nueva_ubicacion  # Mover/renombrar
rm archivo.txt       # Eliminar archivo
rm -rf carpeta/      # Eliminar carpeta y contenido
```

##### Ver contenido de archivos:
```bash
cat archivo.txt      # Mostrar contenido completo
less archivo.txt     # Ver contenido paginado
head archivo.txt     # Primeras 10 líneas
tail archivo.txt     # Últimas 10 líneas
tail -f log.txt      # Seguir cambios en tiempo real
```

##### Búsqueda:
```bash
find . -name "*.txt"     # Buscar archivos .txt
grep "palabra" archivo   # Buscar texto en archivo
grep -r "texto" .        # Buscar recursivamente
```

##### Información del sistema:
```bash
ps aux               # Procesos en ejecución
top                  # Monitor de procesos en tiempo real
df -h                # Espacio en disco
free -h              # Memoria RAM
uname -a             # Información del sistema
```

#### Permisos en Linux

Cada archivo tiene permisos para:
- **Usuario (u):** Propietario del archivo
- **Grupo (g):** Grupo al que pertenece el archivo
- **Otros (o):** Todos los demás usuarios

**Tipos de permisos:**
- **r (read):** Leer - valor 4
- **w (write):** Escribir - valor 2
- **x (execute):** Ejecutar - valor 1

**Ejemplo:**
```bash
-rw-r--r-- 1 usuario grupo 1024 Nov 18 10:30 archivo.txt
```

**Interpretación:**
- `-`: Es un archivo (d sería directorio)
- `rw-`: Usuario puede leer y escribir
- `r--`: Grupo solo puede leer
- `r--`: Otros solo pueden leer

**Cambiar permisos:**
```bash
chmod 755 script.sh      # rwxr-xr-x
chmod +x script.sh       # Agregar permiso de ejecución
chown usuario:grupo archivo  # Cambiar propietario
```

#### Redirección y Pipes

**Redirección:**
```bash
comando > archivo        # Guardar salida en archivo
comando >> archivo       # Agregar salida a archivo
comando < archivo        # Usar archivo como entrada
```

**Pipes (tuberías):**
```bash
comando1 | comando2      # Salida de cmd1 va a entrada de cmd2
ls -la | grep ".txt"     # Listar solo archivos .txt
cat log.txt | grep "error" | wc -l  # Contar líneas con "error"
```

#### Variables de Entorno

**Ver variables:**
```bash
echo $HOME               # Tu directorio home
echo $PATH               # Rutas donde busca ejecutables
env                      # Todas las variables
```

**Definir variables:**
```bash
export MI_VARIABLE="valor"    # Crear variable
echo $MI_VARIABLE             # Usar variable
```

#### Procesos en Background

```bash
comando &                # Ejecutar en background
nohup comando &          # Ejecutar aunque cierres terminal
jobs                     # Ver procesos en background
fg %1                    # Traer proceso al foreground
kill PID                 # Terminar proceso por ID
killall nombre           # Terminar procesos por nombre
```

#### SSH: Conexión Remota

**SSH** permite conectarte a servidores remotos:
```bash
ssh usuario@servidor.com     # Conectar a servidor
ssh -i clave.pem usuario@ip  # Conectar con clave privada
scp archivo usuario@servidor:/ruta  # Copiar archivo
```

#### Editores de Texto en Terminal

**nano (fácil para principiantes):**
```bash
nano archivo.txt         # Abrir archivo
# Ctrl+O: Guardar
# Ctrl+X: Salir
```

**vim (potente pero complejo):**
```bash
vim archivo.txt          # Abrir archivo
# i: Modo inserción
# Esc: Modo comando
# :w: Guardar
# :q: Salir
# :wq: Guardar y salir
```

#### Package Managers

**Ubuntu/Debian (apt):**
```bash
sudo apt update          # Actualizar lista de paquetes
sudo apt install git     # Instalar software
sudo apt remove programa # Desinstalar
```

**CentOS/RHEL (yum/dnf):**
```bash
sudo yum update          # Actualizar sistema
sudo yum install git     # Instalar software
```

#### Tareas Comunes de DevOps en Linux

1. **Verificar logs de aplicación:**
```bash
tail -f /var/log/aplicacion.log
```

2. **Monitorear uso de CPU:**
```bash
top
htop  # Versión mejorada
```

3. **Verificar conectividad de red:**
```bash
ping google.com
curl -I https://miapp.com
```

4. **Administrar servicios:**
```bash
sudo systemctl status nginx    # Ver estado
sudo systemctl start nginx     # Iniciar servicio
sudo systemctl enable nginx    # Habilitar al inicio
```

### Fuentes Relevantes:
- **Documentación:** [Linux Documentation Project](https://tldp.org/)
- **Tutorial interactivo:** [Linux Journey](https://linuxjourney.com/)
- **Referencia rápida:** [Linux Command Line Cheat Sheet](https://cheatography.com/davechild/cheat-sheets/linux-command-line/)
- **Libro gratuito:** [The Linux Command Line](http://linuxcommand.org/tlcl.php)

### Videos Sugeridos:
- **"Linux Command Line Tutorial"** - ExplainingComputers
- **"Linux para Principiantes"** - Pelado Nerd
- **"Linux Basics for DevOps"** - TechWorld with Nana

### Resumen y Próximos Pasos:

**Conceptos clave de esta clase:**
- Linux es fundamental en DevOps por su estabilidad y control
- La terminal es más eficiente que las interfaces gráficas
- Comandos básicos de navegación, manipulación de archivos y procesos
- Permisos y variables de entorno son conceptos cruciales
- SSH permite administrar servidores remotamente

**Próxima clase:** "Redes y Protocolos Básicos para DevOps" donde aprenderás cómo se comunican las aplicaciones a través de la red.