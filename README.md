# Manual de Soporte Remoto para Sistemas POS

> **Objetivo:** proporcionar una guía práctica para diagnosticar, solucionar y documentar incidentes en terminales POS con Windows mediante CMD, PowerShell y herramientas nativas del sistema.
>
> **Enfoque:** diagnóstico → identificación → solución → validación → documentación.

---

# Índice

1. [Principios de soporte remoto](#1-principios-de-soporte-remoto)
2. [Sesión 1 — Identificación del equipo](#2-sesión-1--identificación-del-equipo)
3. [Sesión 2 — Diagnóstico de red](#3-sesión-2--diagnóstico-de-red)
4. [Sesión 3 — Diagnóstico de Internet y DNS](#4-sesión-3--diagnóstico-de-internet-y-dns)
5. [Sesión 4 — Procesos y aplicaciones](#5-sesión-4--procesos-y-aplicaciones)
6. [Sesión 5 — Servicios de Windows](#6-sesión-5--servicios-de-windows)
7. [Sesión 6 — Puertos y conexiones](#7-sesión-6--puertos-y-conexiones)
8. [Sesión 7 — Impresoras y periféricos](#8-sesión-7--impresoras-y-periféricos)
9. [Sesión 8 — Disco y sistema operativo](#9-sesión-8--disco-y-sistema-operativo)
10. [Sesión 9 — Diagnóstico avanzado](#10-sesión-9--diagnóstico-avanzado)
11. [Kit 1 — Diagnóstico inicial](#11-kit-1--diagnóstico-inicial)
12. [Kit 2 — POS no abre](#12-kit-2--pos-no-abre)
13. [Kit 3 — POS lento](#13-kit-3--pos-lento)
14. [Kit 4 — POS sin Internet](#14-kit-4--pos-sin-internet)
15. [Kit 5 — Fallo de datáfono](#15-kit-5--fallo-de-datáfono)
16. [Kit 6 — Fallo de impresora](#16-kit-6--fallo-de-impresora)
17. [Kit 7 — Fallo de servidor](#17-kit-7--fallo-de-servidor)
18. [Kit 8 — Fallo de base de datos](#18-kit-8--fallo-de-base-de-datos)
19. [Kit 9 — Fallo de servicios](#19-kit-9--fallo-de-servicios)
20. [Kit 10 — Problemas de Windows](#20-kit-10--problemas-de-windows)
21. [Kit 11 — POS completamente bloqueado](#21-kit-11--pos-completamente-bloqueado)
22. [Kit 12 — Diagnóstico de red local](#22-kit-12--diagnóstico-de-red-local)
23. [Kit 13 — Problemas de autenticación](#23-kit-13--problemas-de-autenticación)
24. [Kit 14 — Recolección de evidencias](#24-kit-14--recolección-de-evidencias)
25. [Kit 15 — Cierre del incidente](#25-kit-15--cierre-del-incidente)
26. [Comandos de referencia rápida](#26-comandos-de-referencia-rápida)

---

# 1. Principios de soporte remoto

El objetivo del soporte no es ejecutar comandos aleatoriamente.

La metodología recomendada es:

```text
REPORTE
   ↓
IDENTIFICACIÓN
   ↓
DIAGNÓSTICO
   ↓
AISLAMIENTO DEL PROBLEMA
   ↓
SOLUCIÓN
   ↓
PRUEBA
   ↓
DOCUMENTACIÓN
```

## 1.1 Regla principal

Antes de modificar algo, primero determinar:

- ¿El equipo está encendido?
- ¿El usuario puede iniciar sesión?
- ¿El POS abre?
- ¿Existe conexión de red?
- ¿Existe conexión a Internet?
- ¿El servidor responde?
- ¿Los servicios están funcionando?
- ¿Los periféricos responden?
- ¿Hay errores visibles?
- ¿El problema afecta solamente a ese equipo?

---

# 2. Sesión 1 — Identificación del equipo

## Objetivo

Aprender a identificar rápidamente el equipo remoto.

---

## 2.1 `hostname`

```cmd
hostname
```

Muestra el nombre del equipo.

Ejemplo:

```text
POS-CAJA-03
```

### ¿Para qué sirve?

Permite saber exactamente con qué equipo estamos trabajando.

Es especialmente importante cuando una empresa tiene:

```text
POS-CAJA-01
POS-CAJA-02
POS-CAJA-03
POS-CAJA-04
```

---

## 2.2 `whoami`

```cmd
whoami
```

Muestra el usuario actualmente autenticado.

Ejemplo:

```text
empresa\cajero01
```

Puede ayudar a determinar:

- usuario actual;
- dominio;
- cuenta utilizada;
- permisos disponibles.

---

## 2.3 `systeminfo`

```cmd
systeminfo
```

Muestra información detallada del equipo:

- versión de Windows;
- arquitectura;
- memoria;
- procesador;
- nombre del equipo;
- dominio;
- fecha de instalación;
- actualizaciones.

Puede tardar algunos segundos.

---

## 2.4 `ver`

```cmd
ver
```

Muestra rápidamente la versión de Windows.

---

## 2.5 Información rápida recomendada

Para comenzar una sesión:

```cmd
hostname
whoami
ver
ipconfig /all
```

Con estos comandos obtenemos una fotografía inicial del equipo.

---

# 3. Sesión 2 — Diagnóstico de red

## Objetivo

Determinar si el POS tiene conexión con la red local.

---

# 3.1 `ipconfig`

```cmd
ipconfig
```

Muestra:

- IPv4;
- máscara;
- gateway.

Ejemplo:

```text
IPv4 Address : 192.168.1.25
Subnet Mask  : 255.255.255.0
Default Gateway : 192.168.1.1
```

---

# 3.2 `ipconfig /all`

```cmd
ipconfig /all
```

Es uno de los comandos más importantes para soporte.

Permite conocer:

- dirección MAC;
- IPv4;
- IPv6;
- DHCP;
- DNS;
- gateway;
- adaptador de red;
- servidor DHCP.

### Debemos revisar especialmente:

```text
IPv4 Address
Default Gateway
DHCP Server
DNS Servers
Physical Address
```

---

# 3.3 `ipconfig /release`

```cmd
ipconfig /release
```

Libera la dirección IP obtenida mediante DHCP.

### Precaución

No debe ejecutarse indiscriminadamente en un POS que esté trabajando normalmente.

---

# 3.4 `ipconfig /renew`

```cmd
ipconfig /renew
```

Solicita una nueva configuración IP al servidor DHCP.

Puede ayudar cuando:

- cambió la red;
- existe conflicto de IP;
- el equipo perdió DHCP;
- la conexión quedó en un estado incorrecto.

---

# 3.5 `ipconfig /flushdns`

```cmd
ipconfig /flushdns
```

Limpia la caché DNS.

Útil cuando:

```text
Internet funciona por IP
pero no funcionan los nombres de dominio.
```

---

# 4. Sesión 3 — Diagnóstico de Internet y DNS

## Objetivo

Determinar exactamente dónde se rompe la comunicación.

---

# 4.1 `ping`

```cmd
ping 127.0.0.1
```

Prueba la pila TCP/IP local.

---

## 4.2 Probar el gateway

```cmd
ping 192.168.1.1
```

La dirección debe reemplazarse por el gateway real del equipo.

Si responde:

```text
Reply from 192.168.1.1
```

la comunicación con el router está funcionando.

---

# 4.3 Probar Internet por IP

```cmd
ping 8.8.8.8
```

Si responde, existe conectividad IP hacia Internet.

---

# 4.4 Probar Internet por dominio

```cmd
ping google.com
```

Este comando prueba:

- conectividad;
- resolución DNS.

---

# 4.5 Interpretación

### Caso A

```text
ping gateway → OK
ping 8.8.8.8 → OK
ping google.com → OK
```

La conexión básica funciona.

---

### Caso B

```text
gateway → OK
8.8.8.8 → OK
google.com → ERROR
```

Probablemente existe un problema de DNS.

Probar:

```cmd
nslookup google.com
```

---

### Caso C

```text
gateway → ERROR
```

Investigar:

- cable;
- Wi-Fi;
- switch;
- router;
- VLAN;
- adaptador;
- configuración IP.

---

# 4.6 `nslookup`

```cmd
nslookup google.com
```

Permite comprobar la resolución DNS.

Si devuelve una dirección IP, el DNS está resolviendo correctamente.

---

# 4.7 `tracert`

```cmd
tracert google.com
```

Muestra los saltos realizados hasta el destino.

Útil para determinar dónde puede existir un problema de conectividad.

---

# 4.8 `pathping`

```cmd
pathping google.com
```

Combina características de:

```text
ping + tracert
```

Puede ayudar a detectar:

- pérdida de paquetes;
- problemas intermedios;
- latencia.

---

# 5. Sesión 4 — Procesos y aplicaciones

## Objetivo

Determinar si el software POS está funcionando correctamente.

---

# 5.1 `tasklist`

```cmd
tasklist
```

Lista los procesos activos.

---

## 5.2 Buscar un proceso

```cmd
tasklist | findstr POS
```

También podemos buscar por nombre:

```cmd
tasklist | findstr sistema
```

---

# 5.3 Identificar un PID

Ejemplo:

```text
pos.exe    4520
```

El número:

```text
4520
```

es el PID.

---

# 5.4 Finalizar proceso

```cmd
taskkill /PID 4520
```

Forzar:

```cmd
taskkill /PID 4520 /F
```

También:

```cmd
taskkill /IM pos.exe /F
```

### ⚠️ Precaución

No finalizar procesos críticos sin saber qué hacen.

En un POS puede existir:

- aplicación de ventas;
- agente de impresión;
- servicio de pagos;
- servicio de sincronización;
- antivirus;
- agente remoto;
- cliente de base de datos.

---

# 6. Sesión 5 — Servicios de Windows

## Objetivo

Diagnosticar servicios necesarios para el funcionamiento del POS.

---

# 6.1 Abrir servicios

```cmd
services.msc
```

Se abrirá la consola gráfica.

---

# 6.2 `sc query`

```cmd
sc query
```

Lista servicios.

---

# 6.3 Consultar servicio

```cmd
sc query "NombreServicio"
```

Ejemplo:

```cmd
sc query Spooler
```

---

# 6.4 Reiniciar servicio

```cmd
net stop "NombreServicio"
net start "NombreServicio"
```

Ejemplo:

```cmd
net stop Spooler
net start Spooler
```

Esto reinicia el servicio de impresión de Windows.

---

# 6.5 Regla

Antes de reiniciar un servicio:

1. identificarlo;
2. confirmar qué aplicación depende de él;
3. verificar si existe una operación activa;
4. reiniciar;
5. probar nuevamente.

---

# 7. Sesión 6 — Puertos y conexiones

## Objetivo

Determinar si el POS se está comunicando con servidores y servicios.

---

# 7.1 `netstat`

```cmd
netstat -ano
```

Muestra:

- conexiones;
- puertos;
- IP remotas;
- estado;
- PID.

---

# 7.2 Buscar un puerto

```cmd
netstat -ano | findstr :443
```

Ejemplo:

```cmd
netstat -ano | findstr :1433
```

---

# 7.3 Buscar procesos asociados

Si obtenemos:

```text
TCP 192.168.1.25:5000 ... 4520
```

el PID es:

```text
4520
```

Podemos buscarlo:

```cmd
tasklist | findstr 4520
```

---

# 8. Sesión 7 — Impresoras y periféricos

## Objetivo

Diagnosticar impresoras y dispositivos conectados al POS.

---

# 8.1 Listar impresoras

En CMD:

```cmd
wmic printer get name,status
```

En PowerShell:

```powershell
Get-Printer
```

---

# 8.2 Comprobar impresora de red

```cmd
ping 192.168.1.50
```

Si responde:

```text
Reply from...
```

existe comunicación IP.

Si no responde, investigar:

- IP;
- cable;
- switch;
- Wi-Fi;
- impresora;
- configuración de red.

---

# 8.3 Reiniciar spooler

```cmd
net stop spooler
net start spooler
```

Después:

```text
imprimir página de prueba
```

---

# 9. Sesión 8 — Disco y sistema operativo

## 9.1 Espacio disponible

```cmd
wmic logicaldisk get name,size,freespace
```

En PowerShell:

```powershell
Get-PSDrive
```

---

# 9.2 `chkdsk`

```cmd
chkdsk
```

Comprueba el sistema de archivos.

No ejecutar reparaciones agresivas sin autorización.

---

# 9.3 `sfc`

```cmd
sfc /scannow
```

Comprueba archivos protegidos de Windows.

Puede solucionar archivos del sistema dañados.

---

# 9.4 DISM

```cmd
DISM /Online /Cleanup-Image /RestoreHealth
```

Repara componentes de Windows.

### Importante

Estos comandos pueden tardar bastante y no deben ser el primer paso ante un incidente de POS.

---

# 10. Sesión 9 — Diagnóstico avanzado

Cuando los comandos básicos no son suficientes, podemos investigar:

```text
Event Viewer
Servicios
Procesos
Puertos
Logs
Base de datos
Red
Firewall
Periféricos
Aplicación
```

Abrir visor de eventos:

```cmd
eventvwr
```

Abrir administrador de dispositivos:

```cmd
devmgmt.msc
```

Abrir conexiones de red:

```cmd
ncpa.cpl
```

Abrir configuración de servicios:

```cmd
services.msc
```

---

# 11. KIT 1 — Diagnóstico inicial

## Cuándo utilizarlo

Ante cualquier reporte:

> "El POS no funciona."

Primero NO modificar nada.

---

## Paso 1 — Identificación

```cmd
hostname
whoami
ver
```

---

## Paso 2 — Red

```cmd
ipconfig /all
```

---

## Paso 3 — Gateway

```cmd
ping [GATEWAY]
```

---

## Paso 4 — Internet

```cmd
ping 8.8.8.8
```

---

## Paso 5 — DNS

```cmd
nslookup google.com
```

---

## Paso 6 — Procesos

```cmd
tasklist
```

---

## Paso 7 — Servicios

```cmd
sc query
```

---

## Paso 8 — Conexiones

```cmd
netstat -ano
```

---

## Resultado

Clasificar el problema:

```text
[ ] Equipo
[ ] Windows
[ ] Red local
[ ] Internet
[ ] DNS
[ ] Aplicación POS
[ ] Servicio
[ ] Servidor
[ ] Base de datos
[ ] Impresora
[ ] Datáfono
[ ] Otro periférico
```

---

# 12. KIT 2 — POS no abre

## Síntoma

El usuario dice:

> "Le doy al POS y no abre."

---

## Paso 1

Verificar proceso:

```cmd
tasklist | findstr /I "pos"
```

---

## Paso 2

Verificar si existe el ejecutable.

Si conocemos la ruta:

```cmd
dir "C:\Ruta\Del\POS"
```

---

## Paso 3

Revisar errores de Windows:

```cmd
eventvwr
```

Buscar:

```text
Windows Logs
→ Application
```

---

## Paso 4

Revisar servicios relacionados:

```cmd
sc query
```

---

## Paso 5

Si el proceso quedó bloqueado

Primero identificarlo:

```cmd
tasklist | findstr /I "pos"
```

Luego, solamente si está autorizado:

```cmd
taskkill /IM pos.exe /F
```

Abrir nuevamente el POS.

---

## Validación

Confirmar:

```text
¿Abre?
¿Permite iniciar sesión?
¿Carga productos?
¿Conecta con servidor?
¿Permite una operación de prueba?
```

---

# 13. KIT 3 — POS lento

## Síntoma

> "El POS está demasiado lento."

---

## Paso 1 — Procesos

```cmd
tasklist
```

Si tenemos PowerShell:

```powershell
Get-Process | Sort-Object CPU -Descending
```

---

## Paso 2 — Memoria

```cmd
systeminfo
```

---

## Paso 3 — Disco

```cmd
wmic logicaldisk get name,size,freespace
```

---

## Paso 4 — Red

```cmd
ping [SERVIDOR]
```

Después:

```cmd
ping [GATEWAY]
```

---

## Paso 5 — Conexiones

```cmd
netstat -ano
```

---

## Posibles causas

```text
CPU alta
RAM insuficiente
Disco lleno
Disco con problemas
Red lenta
Servidor lento
Base de datos lenta
Aplicación bloqueada
Antivirus
Actualizaciones
Procesos innecesarios
```

---

# 14. KIT 4 — POS sin Internet

## Paso 1

```cmd
ipconfig /all
```

---

## Paso 2

```cmd
ping 127.0.0.1
```

---

## Paso 3

```cmd
ping [GATEWAY]
```

---

## Paso 4

```cmd
ping 8.8.8.8
```

---

## Paso 5

```cmd
nslookup google.com
```

---

## Paso 6

Si DHCP parece incorrecto:

```cmd
ipconfig /release
ipconfig /renew
```

---

## Paso 7

Limpiar DNS:

```cmd
ipconfig /flushdns
```

---

## Validación

```cmd
ping google.com
```

---

# 15. KIT 5 — Fallo de datáfono

## Síntoma

> "El datáfono no funciona."

---

## ⚠️ Primero determinar qué tipo de conexión utiliza

Puede ser:

```text
USB
Ethernet
Wi-Fi
Bluetooth
Puerto serial
Integración directa con POS
```

No asumir que todos funcionan igual.

---

## Paso 1 — ¿El equipo lo detecta?

Si es USB:

```cmd
devmgmt.msc
```

Revisar:

```text
Controladoras USB
Puertos COM
Dispositivos
Dispositivos desconocidos
```

---

## Paso 2 — Si tiene IP

```cmd
ping [IP_DATÁFONO]
```

---

## Paso 3 — Revisar conexiones

```cmd
netstat -ano
```

Si conocemos el puerto:

```cmd
netstat -ano | findstr :[PUERTO]
```

---

## Paso 4 — Revisar aplicación POS

Verificar:

```text
¿POS detecta el datáfono?
¿El datáfono está encendido?
¿Tiene conexión?
¿Aparece error?
¿La transacción llega al datáfono?
¿El datáfono responde?
```

---

## Paso 5 — Reinicio controlado

Si el procedimiento de la empresa lo permite:

```text
1. Cancelar operación activa.
2. Confirmar que no existe una transacción pendiente.
3. Reiniciar el datáfono.
4. Reiniciar el componente POS necesario.
5. Realizar prueba autorizada.
```

### ⚠️ Nunca reiniciar un datáfono durante una transacción sin verificar primero el estado de la operación.

---

# 16. KIT 6 — Fallo de impresora

## Paso 1

```cmd
wmic printer get name,status
```

---

## Paso 2

Abrir:

```cmd
control printers
```

---

## Paso 3

Comprobar impresora de red

```cmd
ping [IP_IMPRESORA]
```

---

## Paso 4

Reiniciar spooler

```cmd
net stop spooler
net start spooler
```

---

## Paso 5

Imprimir página de prueba.

---

## Diagnóstico

```text
Impresora apagada
        ↓
Red
        ↓
Windows
        ↓
Spooler
        ↓
Driver
        ↓
POS
```

---

# 17. KIT 7 — Fallo de servidor

## Síntoma

Todos o varios POS presentan problemas.

---

## Paso 1

Desde el POS:

```cmd
ping [SERVIDOR]
```

---

## Paso 2

Comprobar resolución:

```cmd
nslookup [SERVIDOR]
```

---

## Paso 3

Comprobar puerto:

```cmd
netstat -ano
```

---

## Paso 4

Si conocemos el puerto:

```cmd
netstat -ano | findstr :[PUERTO]
```

---

## Indicador importante

Si:

```text
POS 1 falla
POS 2 funciona
POS 3 funciona
```

probablemente el problema está en:

```text
POS 1
```

Pero si:

```text
POS 1 falla
POS 2 falla
POS 3 falla
POS 4 falla
```

investigar:

```text
Servidor
Red
Base de datos
Servicio central
Internet
```

---

# 18. KIT 8 — Fallo de base de datos

## Síntomas

```text
No carga productos
No guarda ventas
Error de conexión
POS queda cargando
No sincroniza
```

---

## Paso 1

Comprobar servidor:

```cmd
ping [SERVIDOR]
```

---

## Paso 2

Comprobar puerto.

Ejemplo SQL Server:

```cmd
netstat -ano | findstr :1433
```

---

## Paso 3

Revisar servicios.

```cmd
sc query
```

Buscar servicios relacionados con la base de datos.

---

## Paso 4

Revisar logs del POS.

Buscar errores como:

```text
Connection timeout
Connection refused
Login failed
Database unavailable
Network error
```

---

# 19. KIT 9 — Fallo de servicios

## Síntoma

El POS abre, pero una función concreta no funciona.

Ejemplos:

```text
No imprime
No sincroniza
No conecta
No actualiza
No procesa pagos
```

---

## Paso 1

```cmd
sc query
```

---

## Paso 2

Buscar servicio específico:

```cmd
sc query "NombreServicio"
```

---

## Paso 3

Abrir:

```cmd
services.msc
```

---

## Paso 4

Si está detenido y existe autorización:

```cmd
net start "NombreServicio"
```

---

## Paso 5

Validar la función.

---

# 20. KIT 10 — Problemas de Windows

## Síntomas

```text
Windows presenta errores
Aplicaciones fallan
Archivos del sistema dañados
Comportamiento extraño
```

---

## Paso 1

```cmd
sfc /scannow
```

---

## Paso 2

Si corresponde:

```cmd
DISM /Online /Cleanup-Image /RestoreHealth
```

---

## Paso 3

Revisar eventos:

```cmd
eventvwr
```

---

## Paso 4

Reiniciar solamente cuando:

- la operación del POS esté cerrada;
- no exista una venta activa;
- no exista transacción pendiente;
- el usuario esté informado.

---

# 21. KIT 11 — POS completamente bloqueado

## Situación

```text
No responde
No permite operar
Ventanas congeladas
```

---

## Escalamiento

### Nivel 1

Intentar cerrar la aplicación normalmente.

### Nivel 2

Si responde CMD:

```cmd
tasklist
```

### Nivel 3

Identificar proceso POS.

### Nivel 4

Finalizar proceso solamente con autorización:

```cmd
taskkill /PID [PID] /F
```

### Nivel 5

Si Windows completo no responde:

```text
Solicitar reinicio físico/controlado.
```

---

# 22. KIT 12 — Diagnóstico de red local

## Objetivo

Determinar si el problema está dentro de la LAN.

---

## Comandos

```cmd
ipconfig /all
```

```cmd
ping [GATEWAY]
```

```cmd
ping [OTRO_POS]
```

```cmd
ping [SERVIDOR]
```

```cmd
arp -a
```

---

## `arp -a`

```cmd
arp -a
```

Muestra la tabla ARP.

Puede ayudar a verificar qué dispositivos de la red local conoce el equipo.

---

# 23. KIT 13 — Problemas de autenticación

## Síntoma

> "El usuario no puede entrar al POS."

---

## Revisar

```cmd
whoami
```

---

## Ver equipo/dominio

```cmd
systeminfo
```

---

## Comprobar conectividad con infraestructura

```cmd
ping [SERVIDOR]
```

---

## Comprobar DNS

```cmd
nslookup [SERVIDOR]
```

---

## Posibles causas

```text
Usuario incorrecto
Contraseña incorrecta
Cuenta bloqueada
Servidor inaccesible
DNS
Dominio
Aplicación POS
Base de datos
```

No modificar credenciales ni desbloquear cuentas sin autorización.

---

# 24. KIT 14 — Recolección de evidencias

Antes de escalar un incidente, recopilar:

## Equipo

```cmd
hostname
whoami
systeminfo
```

## Red

```cmd
ipconfig /all
```

## Conectividad

```cmd
ping [GATEWAY]
ping [SERVIDOR]
```

## DNS

```cmd
nslookup [SERVIDOR]
```

## Procesos

```cmd
tasklist
```

## Conexiones

```cmd
netstat -ano
```

## Servicios

```cmd
sc query
```

---

## Información que debe quedar registrada

```text
Fecha:
Hora:
Sucursal:
Equipo:
Usuario:
Problema reportado:
Error mostrado:
Hora en que comenzó:
Comandos ejecutados:
Resultados:
Cambios realizados:
Resultado final:
Escalado a:
```

---

# 25. KIT 15 — Cierre del incidente

Nunca terminar simplemente diciendo:

> "Ya funciona."

Debe verificarse.

---

## Checklist

```text
[ ] POS abre
[ ] Usuario puede ingresar
[ ] Productos cargan
[ ] Base de datos responde
[ ] Internet funciona
[ ] Servidor responde
[ ] Impresora funciona
[ ] Datáfono funciona
[ ] Sincronización funciona
[ ] Operación de prueba exitosa
```

---

# 26. Comandos de referencia rápida

## Información

```cmd
hostname
whoami
systeminfo
ver
```

## Red

```cmd
ipconfig
ipconfig /all
ipconfig /flushdns
ipconfig /release
ipconfig /renew
```

## Conectividad

```cmd
ping
tracert
pathping
nslookup
```

## Procesos

```cmd
tasklist
taskkill
```

## Servicios

```cmd
sc query
net start
net stop
net start
```

## Conexiones

```cmd
netstat -ano
```

## Impresoras

```cmd
wmic printer get name,status
```

```cmd
control printers
```

## Sistema

```cmd
sfc /scannow
```

```cmd
DISM /Online /Cleanup-Image /RestoreHealth
```

## Herramientas gráficas

```cmd
services.msc
eventvwr
devmgmt.msc
ncpa.cpl
control printers
```

---

# 27. Árbol general de diagnóstico

```text
                  ┌─────────────────┐
                  │  POS NO FUNCIONA │
                  └────────┬────────┘
                           │
                           ▼
                  ¿Windows responde?
                    /            \
                  NO              SÍ
                  │                │
                  ▼                ▼
            Escalamiento      ¿POS abre?
                                  /   \
                                NO     SÍ
                                │       │
                                ▼       ▼
                            Proceso   ¿Función falla?
                            Servicio      /     \
                            Logs         NO      SÍ
                                         │        │
                                         ▼        ▼
                                      Revisar   Identificar
                                      operación componente
                                                   │
                              ┌────────────────────┼─────────────────┐
                              │                    │                 │
                              ▼                    ▼                 ▼
                            RED                 SERVICIO          PERIFÉRICO
                              │                    │                 │
                              ▼                    ▼                 ▼
                           ping                 sc query        impresora
                           DNS                  logs            datáfono
                           gateway              puertos         lector
                           servidor
```

---

# 28. Metodología profesional

## Regla 1 — No adivinar

No ejecutar comandos porque sí.

Primero:

```text
¿Qué está fallando?
```

---

## Regla 2 — Cambiar una cosa a la vez

Si ejecutamos diez cambios simultáneamente, no sabremos cuál solucionó el problema.

---

## Regla 3 — Registrar antes de modificar

Siempre que sea posible:

```text
Estado inicial
↓
Diagnóstico
↓
Cambio
↓
Prueba
↓
Resultado
```

---

## Regla 4 — No reiniciar durante una operación crítica

Especialmente:

```text
Venta
Pago
Datáfono
Sincronización
Cierre de caja
Actualización
```

---

## Regla 5 — Seguridad

Nunca solicitar al usuario:

- contraseñas;
- claves de tarjetas;
- PIN;
- códigos de autenticación;
- credenciales bancarias.

---

# 29. Chuleta final

Ante cualquier incidente:

```cmd
hostname
```

```cmd
ipconfig /all
```

```cmd
ping [GATEWAY]
```

```cmd
ping [SERVIDOR]
```

```cmd
nslookup [SERVIDOR]
```

```cmd
tasklist
```

```cmd
netstat -ano
```

```cmd
sc query
```

Después:

```text
¿Es equipo?
¿Es Windows?
¿Es red?
¿Es servidor?
¿Es aplicación?
¿Es servicio?
¿Es base de datos?
¿Es impresora?
¿Es datáfono?
```

Y finalmente:

```text
SOLUCIONAR
   ↓
PROBAR
   ↓
DOCUMENTAR
   ↓
CERRAR
```

---

# 30. Niveles de soporte recomendados

## Nivel 1 — Básico

```text
Ping
IP
DNS
Procesos
Servicios
Impresoras
Reinicio de aplicación
```

## Nivel 2 — Técnico

```text
Puertos
Servidor
Base de datos
Logs
Servicios específicos
Configuración de red
```

## Nivel 3 — Especializado

```text
Base de datos
Infraestructura
Firewall
VLAN
Integraciones
Datáfonos
APIs
Aplicación POS
```

---

# Conclusión

El objetivo del soporte remoto no es conocer cientos de comandos.

Es saber responder:

> **¿Dónde está fallando la cadena?**

La cadena típica de un POS puede representarse así:

```text
USUARIO
   ↓
WINDOWS
   ↓
APLICACIÓN POS
   ↓
RED LOCAL
   ↓
SERVIDOR
   ↓
BASE DE DATOS
   ↓
SERVICIOS
   ↓
PERIFÉRICOS
   ↓
DATÁFONO / IMPRESORA / LECTOR
```

Si se identifica correctamente el punto donde se rompe la cadena, el diagnóstico se vuelve mucho más rápido y ordenado.
