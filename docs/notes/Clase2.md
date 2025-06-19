## Clase 2: Control de Versiones con Git - Tu Primera Herramienta DevOps

**Módulo:** 1  
**Temática del Módulo:** Introducción a los fundamentos de DevOps  
**Tema Específico:** Sistema de control de versiones y colaboración con Git

### Contenido de la Clase:

#### ¿Qué es Control de Versiones? (La Analogía del Documento)

Imagina que estás escribiendo tu tesis. Sin control de versiones:
- `Tesis_final.docx`
- `Tesis_final_final.docx`
- `Tesis_final_final_v2.docx`
- `Tesis_final_DEFINITIVA.docx`
- `Tesis_final_DEFINITIVA_corregida.docx`

**Problemas:**
- ¿Cuál es la versión actual?
- ¿Qué cambió entre versiones?
- ¿Cómo colaborar con otros sin conflictos?
- ¿Cómo recuperar una versión anterior?

**Con Git (control de versiones):**
- Historial completo de cambios
- Colaboración sin conflictos
- Recuperación fácil de versiones anteriores
- Identificación clara de quién cambió qué y cuándo

#### ¿Qué es Git?

**Git** es un sistema de control de versiones **distribuido** que permite:
- Rastrear cambios en archivos a lo largo del tiempo
- Colaborar con múltiples personas en el mismo proyecto
- Mantener diferentes versiones del código simultáneamente
- Recuperar versiones anteriores cuando algo se rompe

**¿Por qué es fundamental en DevOps?**
- Es la base para automatización (CI/CD)
- Permite colaboración efectiva entre equipos
- Mantiene historial completo de cambios
- Facilita el rollback cuando hay problemas

#### Conceptos Fundamentales de Git

##### 1. **Repositorio (Repo)**
Es como una "carpeta inteligente" que contiene:
- Tu código
- Historial completo de cambios
- Información sobre quién hizo qué cambios

##### 2. **Commit**
Es como una "fotografía" de tu código en un momento específico:
- Guarda el estado de todos los archivos
- Incluye mensaje describiendo los cambios
- Se identifica con un hash único

##### 3. **Branch (Rama)**
Es como una "línea de tiempo paralela":
- Permite trabajar en funcionalidades sin afectar el código principal
- Main/Master: rama principal
- Feature branches: ramas para nuevas funcionalidades

##### 4. **Merge**
Es como "unir líneas de tiempo":
- Combina cambios de diferentes ramas
- Integra el trabajo de múltiples desarrolladores

#### Flujo Básico de Git

```
1. git init          → Crear repositorio
2. git add .         → Preparar archivos para commit
3. git commit        → Guardar cambios
4. git push          → Subir cambios al servidor
5. git pull          → Descargar cambios del servidor
```

#### Estados de los Archivos en Git

1. **Working Directory:** Archivos en tu computadora
2. **Staging Area:** Archivos preparados para commit
3. **Repository:** Archivos guardados en el historial

```
Working Directory → [git add] → Staging Area → [git commit] → Repository
```

#### Comandos Esenciales de Git (Para Principiantes)

##### Configuración inicial:
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

##### Crear un repositorio:
```bash
git init
```

##### Verificar estado:
```bash
git status
```

##### Preparar archivos:
```bash
git add archivo.txt    # Un archivo específico
git add .              # Todos los archivos
```

##### Guardar cambios:
```bash
git commit -m "Descripción del cambio"
```

##### Ver historial:
```bash
git log
```

#### GitHub vs Git

**Git:** La herramienta (como Microsoft Word)
**GitHub:** El servicio en la nube (como Google Drive)

**GitHub proporciona:**
- Almacenamiento remoto para repositorios
- Interfaz web para ver código
- Herramientas de colaboración
- Integración con herramientas DevOps

#### Flujo de Trabajo en Equipo

1. **Clone:** Descargar repositorio del equipo
2. **Branch:** Crear rama para tu funcionalidad
3. **Code:** Desarrollar tu funcionalidad
4. **Commit:** Guardar cambios
5. **Push:** Subir tu rama
6. **Pull Request:** Pedir revisión de código
7. **Merge:** Integrar cambios al código principal

#### ¿Por qué Git es Crucial para DevOps?

1. **Automatización:** Los pipelines CI/CD se activan con commits
2. **Colaboración:** Múltiples desarrolladores trabajando simultáneamente
3. **Rastreabilidad:** Saber exactamente qué cambió y cuándo
4. **Rollback:** Revertir cambios problemáticos rápidamente
5. **Branching:** Diferentes ambientes (desarrollo, testing, producción)

#### Mejores Prácticas con Git

1. **Commits frecuentes:** Pequeños cambios, commits regulares
2. **Mensajes descriptivos:** "Arreglar bug de login" vs "fix"
3. **Branches para funcionalidades:** No trabajar directamente en main
4. **Pull antes de push:** Sincronizar antes de subir cambios
5. **Revisar antes de commit:** git status y git diff

### Fuentes Relevantes:
- **Documentación oficial:** [Git Documentation](https://git-scm.com/doc)
- **Tutorial interactivo:** [Learn Git Branching](https://learngitbranching.js.org/)
- **Guía GitHub:** [GitHub Git Handbook](https://guides.github.com/introduction/git-handbook/)
- **Libro gratuito:** [Pro Git Book](https://git-scm.com/book)

### Videos Sugeridos:
- **"Git Tutorial for Beginners"** - Programming with Mosh
- **"Git y GitHub desde cero"** - HolaMundo
- **"Learn Git in 20 Minutes"** - Web Dev Simplified

### Resumen y Próximos Pasos:

**Conceptos clave de esta clase:**
- Git es un sistema de control de versiones esencial para DevOps
- Permite rastrear cambios, colaborar en equipo y automatizar procesos
- Los conceptos básicos incluyen repositorios, commits, branches y merges
- GitHub proporciona almacenamiento remoto y herramientas de colaboración

**Próxima clase:** "Linux Básico para DevOps - Dominando la Terminal" donde aprenderás comandos esenciales del sistema operativo más usado en servidores.
