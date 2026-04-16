---
title: 'Guía de instalación de Claude Code en Windows: Git, PATH, variables de entorno, PowerShell, WSL y solución completa de problemas (2026)'
published: true
description: 'Una guía muy detallada de Windows para instalar Claude Code para principiantes. Cubre PowerShell, winget, Git for Windows, Node.js, PATH, variables de entorno, símbolo del sistema vs PowerShell vs WSL, y problemas comunes posteriores a la instalación.'
tags: 'ai, cli, tutorial, windows'
canonical_url: null
cover_image: 'https://lsky.zhongzhuan.chat/i/2025/12/29/69523b0cd9f0d.png'
id: 3511043
date: '2026-04-16T14:48:33Z'
---

Los usuarios de Windows generalmente se encuentran con un conjunto diferente de problemas de Claude Code que los usuarios de macOS.

No porque Claude Code sea imposible en Windows, sino porque Windows tiene más combinaciones de entorno:

- Símbolo del sistema
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node instalado desde MSI
- Node instalado desde winget
- Git instalado pero no agregado a PATH
- variables de entorno establecidas para un shell pero no para otro

Por eso esta guía avanza más lentamente y explica la cadena de configuración desde cero.

## Antes de instalar nada: elige tu ruta de Windows

Hay dos rutas realistas.

### Opción A: Configuración nativa de Windows

Utiliza:
- Windows Terminal
- PowerShell
- Git for Windows
- Node.js para Windows
- instalación global de npm

Esto es más fácil para la mayoría de principiantes.

### Opción B: Configuración WSL

Utiliza:
- WSL2
- Ubuntu o Debian dentro de WSL
- pasos de instalación de Linux dentro de WSL

Esto suele ser más limpio para desarrolladores, pero añade una capa adicional. Si eres completamente nuevo, comienza con Windows nativo primero a menos que ya uses WSL.

## Configuración recomendada para principiantes

Para la mayoría de nuevos usuarios, recomiendo:

- Windows 11 o Windows 10 actualizado
- Windows Terminal
- PowerShell
- winget para instalaciones de paquetes
- Git for Windows
- Node.js LTS
- Claude Code vía npm

## Paso 1: Abre la terminal correcta

Instala o abre **Windows Terminal** si es posible.

Luego abre **PowerShell**.

Verifica en qué shell te encuentras:

```powershell
$PSVersionTable.PSVersion
```

Si PowerShell se abre y funciona, mantente allí durante toda la configuración. No mezcles PowerShell, cmd, Git Bash y WSL durante la instalación inicial a menos que sepas por qué.

## Paso 2: Comprueba si `winget` está disponible

`winget` es la forma más fácil de instalar Git y Node en Windows moderno.

```powershell
winget --version
```

Si funciona, excelente.

Si no:
- actualiza App Installer desde Microsoft Store
- o instala paquetes manualmente desde sitios web oficiales

## Paso 3: Instala Git

Verifica primero:

```powershell
git --version
```

Si falta Git, instálalo con winget:

```powershell
winget install --id Git.Git -e --source winget
```

Luego **cierra y reabre PowerShell**.

Verifica:

```powershell
git --version
where.exe git
```

### Por qué Git importa también en Windows

Por la misma razón que en macOS:
- flujo de trabajo consciente del repositorio
- diffs
- ediciones más seguras
- reversión de versiones
- muchas herramientas de desarrollador asumen que Git está presente

Si aún no tienes un repositorio:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Identidad global opcional:

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Paso 4: Instala Node.js y npm

Verifica versiones existentes:

```powershell
node --version
npm --version
```

Si faltan, instala Node.js:

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Cierra y reabre PowerShell nuevamente.

Verifica:

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Paso 5: Instala Claude Code

Verifica si ya existe:

```powershell
where.exe claude
claude --version
```

Si no, instálalo globalmente:

```powershell
npm install -g @anthropic-ai/claude-code
```

Luego verifica:

```powershell
where.exe claude
claude --version
```

## Paso 6: Soluciona problemas de PATH en Windows

La queja más común de Windows es:

- npm install dice éxito
- pero `claude` aún no es reconocido

Error típico:

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### Primero encuentra el prefijo global de npm

```powershell
npm config get prefix
```

Normalmente esto apunta a un directorio cuya ubicación `bin` o ejecutable debería ser descubierta a través de PATH.

Verifica dónde npm instaló `claude`:

```powershell
npm list -g --depth=0
```

También intenta:

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Solución común: reabre el shell

Muchos problemas de PATH son solo sesiones antiguas. Cierra PowerShell completamente, abre una nueva y vuelve a intentar.

### Si PATH sigue siendo incorrecto

Inspecciona el PATH del usuario:

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

Y el PATH de la máquina:

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

Si falta la ubicación del ejecutable global de npm, puedes agregarlo a través de la interfaz de variables de entorno de Windows o con PowerShell.

Ten cuidado al editar PATH mediante programación. Crea una copia de seguridad primero.

## Paso 7: Establece las variables de entorno correctamente

Los usuarios de Windows a menudo establecen variables en un lugar y asumen que cada shell las verá. Eso no siempre es cierto.

### Variable de solo sesión en PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "your_key_here"
$env:OPENAI_API_KEY = "your_crazyrouter_key"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

Estas solo duran la sesión actual.

### Variables de entorno persistentes a nivel de usuario

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "your_key_here", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your_crazyrouter_key", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Luego cierra y reabre PowerShell.

Verifica:

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Paso 8: Entiende la diferencia entre PowerShell, cmd, Git Bash y WSL

Esto importa porque las variables y PATH pueden comportarse de manera diferente.

| Entorno | ¿Bueno para principiantes? | Notas |
|---------|---------------------------|-------|
| PowerShell | Sí | Mejor opción nativa de Windows |
| Símbolo del sistema | Bien | Menos conveniente que PowerShell |
| Git Bash | Mixto | Funciona, pero añade otra capa de shell |
| WSL | Bueno para desarrolladores | Lo mejor si deseas comportamiento similar a Linux |

Si instalaste Claude Code en PowerShell nativo de Windows, no lo pruebes primero en WSL y asumas que se aplica el mismo entorno.

WSL tiene su propio sistema de paquetes, rutas, archivos de shell y variables.

## Paso 9: Ruta WSL opcional

Si deseas el entorno de desarrollador más limpio a largo plazo en Windows, instala WSL.

Verifica WSL:

```powershell
wsl --status
```

Instala si es necesario:

```powershell
wsl --install
```

Luego reinicia si Windows lo pide.

Después de eso, abre Ubuntu y trátalo como una máquina Linux:

- instala Git dentro de WSL
- instala Node dentro de WSL
- instala Claude Code dentro de WSL
- establece variables de entorno dentro de archivos de shell de WSL

No asumas que tu instalación de Node del lado de Windows cubre automáticamente WSL.

## Paso 10: Verifica todo de extremo a extremo

Ejecuta estas verificaciones:

```powershell
git --version
node --version
npm --version
claude --version
where.exe git
where.exe node
where.exe npm
where.exe claude
```

Luego crea una carpeta de prueba segura:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

Y prueba el CLI con comandos no destructivos primero.

## Problemas comunes de Windows y soluciones

## 1. `claude` no es reconocido

Causa:
- el directorio ejecutable global de npm no está en PATH
- el shell no se reinició
- la instalación falló

Solución:
- reabre PowerShell
- comprueba `npm config get prefix`
- confirma que el paquete existe en la lista global de npm
- inspecciona PATH

## 2. Git se instala pero PowerShell aún no puede encontrarlo

Causa:
- sesión de terminal abierta antes de la instalación
- PATH no se actualizó

Solución:
- cierra y reabre completamente la terminal
- verifica con `where.exe git`

## 3. Node se instala, pero npm está ausente o roto

Causa:
- instalación incompleta
- versión de Node antigua conflictiva

Solución:
- desinstala versiones de Node conflictivas si es necesario
- reinstala LTS limpiamente
- verifica tanto `node --version` como `npm --version`

## 4. La variable de entorno se establece en PowerShell pero no en otra terminal

Causa:
- la variable era de solo sesión

Solución:
- usa variables de entorno persistentes a nivel de usuario
- reabre la terminal después de establecerlas

## 5. WSL funciona pero PowerShell no, o viceversa

Causa:
- configuraste dos entornos diferentes

Solución:
- decide si deseas Windows nativo o WSL como tu entorno principal de Claude Code
- completa la configuración dentro de ese entorno completamente

## 6. El proxy corporativo bloquea npm install

Puedes necesitar:

```powershell
npm config set proxy http://proxy.example.com:8080
npm config set https-proxy http://proxy.example.com:8080
```

Y posiblemente también variables de sesión.

## 7. El antivirus o software de seguridad interfiere

A veces las herramientas de seguridad interfieren con herramientas CLI recién instaladas o scripts.

Si los registros de instalación se ven normales pero los ejecutables no se comportan normalmente, prueba en una terminal limpia, confirma que el archivo existe y verifica Windows Security o el historial de protección de endpoints.

## Una configuración segura predeterminada de Windows

Si deseas la ruta más simple que sea más fácil de soportar, usa exactamente esta pila:

- Windows Terminal
- PowerShell
- winget
- Git for Windows
- Node.js LTS
- Claude Code vía instalación global de npm
- variables de entorno persistentes a nivel de usuario

Esa configuración es aburrida, y eso es exactamente por qué es buena.

## Preguntas frecuentes

### ¿Deberían los principiantes usar PowerShell o WSL para Claude Code?

Si eres nuevo, comienza con PowerShell. Si ya prefieres herramientas Linux o ya usas WSL diariamente, WSL puede ser más limpio a largo plazo.

### ¿Por qué Claude Code se instaló exitosamente pero aún no se ejecuta?

La mayoría de las veces: PATH antiguo, shell incorrecto, o npm instaló el paquete en una ubicación que tu terminal actual no está leyendo.

### ¿Necesito Git antes de usar Claude Code en Windows?

Para uso serio, sí. Incluso si el CLI se inicia sin Git, los flujos de trabajo de codificación normales son mucho más suaves con Git instalado y configurado.

### ¿Dónde debo almacenar variables de entorno de Claude Code en Windows?

Para persistencia, establécelas a nivel de entorno del usuario, no solo en la sesión de shell actual.

### ¿Es Git Bash un buen lugar para ejecutar Claude Code?

Puede funcionar, pero para principiantes añade más variables. PowerShell es más simple de documentar y soportar.

## Conclusión final

La historia de instalación de Windows no es difícil porque Claude Code en sí sea difícil. Es difícil porque Windows te ofrece muchos entornos superpuestos.

Si mantienes la configuración consistente — PowerShell, winget, Git, Node, npm, Claude Code, luego variables de entorno — la instalación se vuelve mucho más fácil de depurar y enseñar.
