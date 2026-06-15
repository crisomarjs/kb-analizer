# 🛡️ Windows Update Audit Script

> Auditoría de Windows Update para Windows Server — **solo lectura, no instala nada**.

Desarrollado por **Cristian Omar Jiménez Sánchez** · [@crisomarjs](https://github.com/crisomarjs)

---

## 📋 Descripción

Script de PowerShell que permite auditar el estado de Windows Update en servidores Windows de manera segura y no invasiva. Consulta el último parche instalado, detecta si hay reinicios pendientes y lista las actualizaciones de seguridad disponibles, todo sin instalar ni modificar nada en el sistema.

Genera un log detallado con timestamp por cada ejecución, útil para reportes de cumplimiento, auditorías de parches y revisiones periódicas de infraestructura.

---

## ✅ Compatibilidad

| Sistema Operativo      | Build mínimo |
|------------------------|-------------|
| Windows Server 2016    | 14393        |
| Windows Server 2019    | 17763        |
| Windows Server 2022    | 20348        |
| Windows Server 2025    | 26100        |

> Requiere **PowerShell 5.1** o superior. No compatible con PowerShell 4.0 o anterior.

---

## 🚀 Uso

### Ejecución directa (recomendado)

Pega el contenido del script directamente en **PowerShell ISE** o una consola de PowerShell con privilegios de administrador y ejecútalo.

> ⚠️ Debe ejecutarse como **Administrador**.

### Ejecución desde archivo

```powershell
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process -Force
.\Get-WindowsUpdateAudit.ps1
```

---

## 📂 Estructura de archivos generados

```
C:\PSWindowsUpdate\
└── Logs\
    └── WU_Audit_NOMBRESERVIDOR_YYYYMMDD_HHmmss.log
```

Cada ejecución genera un log independiente con el nombre del servidor y el timestamp de ejecución.

---

## 🔍 ¿Qué audita?

### 1. Último parche (KB) instalado
- KB ID, tipo de actualización, fecha de instalación e instalador.
- Tabla con los últimos 5 parches aplicados.

### 2. Estado de reinicio pendiente
Verifica múltiples fuentes del registro de Windows:

| Fuente                          | Clave de registro                                                              |
|---------------------------------|-------------------------------------------------------------------------------|
| Component Based Servicing       | `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing`   |
| Windows Update Auto Update      | `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Auto Update`   |
| Archivos pendientes de renombrar| `HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager`                      |
| Instalación MSI en progreso     | `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\InProgress`        |
| Servicios WU pendientes         | `HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Services\Pending` |
| SCCM / Configuration Manager    | `HKLM:\SOFTWARE\Microsoft\SMS\Mobile Client\Reboot Management\RebootData`     |

### 3. Actualizaciones de seguridad pendientes
- Lista completa de actualizaciones disponibles vía `PSWindowsUpdate`.
- Filtrado automático de actualizaciones de seguridad (`Security`, `Cumulative`, `Rollup`, categoría `Security Updates`).
- Muestra KB, tamaño y título de cada actualización.

---

## 📦 Dependencias

El script gestiona automáticamente la instalación de la dependencia requerida si no está presente:

| Módulo            | Fuente         | Instalación       |
|-------------------|---------------|-------------------|
| `PSWindowsUpdate` | PSGallery      | Automática        |
| `NuGet` provider  | PowerShell     | Automática        |

> La primera ejecución puede tomar varios minutos si el módulo `PSWindowsUpdate` no está instalado. Se requiere acceso a Internet o a un repositorio interno configurado como PSGallery.

---

## 📄 Ejemplo de log

```
=========================================================================
  AUDITORIA WINDOWS UPDATE - SOLO CONSULTA (NO INSTALA)
=========================================================================
  Servidor   : SERVIDOR01
  IP(s)      : 192.168.1.10
  SO         : Windows Server 2019
  Build      : 17763
  Fecha/Hora : 2025-06-10 08:30:00
  Usuario    : DOMAIN\admin
  Log        : C:\PSWindowsUpdate\Logs\WU_Audit_SERVIDOR01_20250610_083000.log
=========================================================================

=================================================================
ULTIMO PARCHE (KB) INSTALADO
=================================================================
  KB           : KB5027222
  Tipo         : Security Update
  Instalado    : 2025-05-14
  Instalado por: NT AUTHORITY\SYSTEM

=================================================================
ESTADO DE REINICIO PENDIENTE
=================================================================
  REINICIO PENDIENTE: NO

=================================================================
ACTUALIZACIONES PENDIENTES (SOLO LECTURA)
=================================================================
  Total de actualizaciones disponibles: 3
  Actualizaciones de SEGURIDAD pendientes: 2
```

---

## ⚠️ Consideraciones

- El script **no instala, no modifica ni aplica** ninguna actualización.
- La consulta a Windows Update puede tomar **varios minutos** dependiendo de la conectividad con WSUS o Internet.
- Si el servidor usa **WSUS**, las actualizaciones listadas corresponderán a las aprobadas en ese servidor.
- Los logs se acumulan en `C:\PSWindowsUpdate\Logs\`. Se recomienda implementar una política de retención o limpieza periódica.

<p align="center">
  Desarrollado con ❤️ para equipos de infraestructura y operaciones Windows
</p>
