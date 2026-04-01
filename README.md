# GLPI_Implement
Guía completa y automatizada para instalar GLPI  en Windows Subsystem for Linux (WSL) con Ubuntu 24.04, - como MVP

# 🚀 Instalación de GLPI en WSL (Windows Subsystem for Linux)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GLPI Version](https://img.shields.io/badge/GLPI-10.0.15-blue)](https://github.com/glpi-project/glpi/releases/tag/10.0.15)
[![Ubuntu Version](https://img.shields.io/badge/Ubuntu-24.04-orange)](https://releases.ubuntu.com/24.04/)
[![PHP Version](https://img.shields.io/badge/PHP-8.3-purple)](https://www.php.net/releases/8.3/en.php)

## 📋 Tabla de Contenidos

- [Descripción](#-descripción)
- [Requisitos Previos](#-requisitos-previos)
- [Arquitectura de la Solución](#-arquitectura-de-la-solución)
- [Guía de Instalación Paso a Paso](#-guía-de-instalación-paso-a-paso)
  - [1. Preparación del Entorno](#1-preparación-del-entorno)
  - [2. Instalación de Apache2](#2-instalación-de-apache2)
  - [3. Instalación de PHP y Extensiones](#3-instalación-de-php-y-extensiones)
  - [4. Instalación de MariaDB](#4-instalación-de-mariadb)
  - [5. Creación de Base de Datos](#5-creación-de-base-de-datos)
  - [6. Descarga de GLPI](#6-descarga-de-glpi)
  - [7. Configuración de Permisos](#7-configuración-de-permisos)
  - [8. Configuración de Apache](#8-configuración-de-apache)
  - [9. Configuración Adicional](#9-configuración-adicional)
  - [10. Verificación Final](#10-verificación-final)
- [Post-Instalación](#-post-instalación)
- [Solución de Problemas](#-solución-de-problemas)
- [Mantenimiento y Respaldo](#-mantenimiento-y-respaldo)
- [Referencias](#-referencias)
- [Autor y Contribuciones](#-autor-y-contribuciones)

## 📖 Descripción

Este repositorio contiene una guía completa y documentada para la instalación de **GLPI** (Gestión de Biblioteca de Inventario de Recursos TI) en un entorno **WSL (Windows Subsystem for Linux)** con **Ubuntu 24.04**.

GLPI es una solución de código abierto para la gestión de servicios de TI (ITSM), que incluye:
- 📦 **Inventario de activos** (hardware, software, periféricos)
- 🎫 **Gestión de tickets** y mesa de ayuda
- 📊 **Reportes y dashboards**
- 📄 **Gestión de contratos y licencias**
- 🔄 **Seguimiento de cambios y problemas**

### 🎯 ¿Por qué usar este manual?

- ✅ **Paso a paso**: Cada comando está explicado y documentado
- ✅ **Probado en producción**: Los comandos fueron ejecutados y validados
- ✅ **Optimizado**: Configuración lista para entornos de desarrollo y producción pequeña
- ✅ **Estructura profesional**: Listo para ser usado como documentación en tu empresa

## 📌 Requisitos Previos

Antes de comenzar, asegúrate de tener:

| Requisito | Especificación |
|-----------|----------------|
| **Sistema Operativo** | Windows 10/11 con WSL2 habilitado |
| **Distribución Linux** | Ubuntu 24.04 LTS |
| **Memoria RAM** | Mínimo 4 GB (recomendado 8 GB) |
| **Espacio en Disco** | 10 GB libres |
| **Permisos** | Acceso `root` o usuario con `sudo` |

### 🔧 Verificar WSL

```bash
+ powershell
  # En PowerShell (Windows)
  wsl --list --verbose
```

## 🏗️ Arquitectura de la Solución

```bash
┌─────────────────────────────────────────┐
│           Windows 10/11                 │
│  ┌───────────────────────────────────┐  │
│  │          WSL2 (Ubuntu)            │  │
│  │  ┌─────────────────────────────┐  │  │
│  │  │       Apache2 (Puerto 8080)│  │  │
│  │  └─────────────┬───────────────┘  │  │
│  │  ┌─────────────▼───────────────┐  │  │
│  │  │           PHP 8.3           │  │  │
│  │  └─────────────┬───────────────┘  │  │
│  │  ┌─────────────▼───────────────┐  │  │
│  │  │        MariaDB 10.11        │  │  │
│  │  └─────────────────────────────┘  │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

# 🚀 Guía de Instalación Paso a Paso
1. Preparación del Entorno
Actualiza los repositorios y paquetes del sistema

```bash
* Actualizar lista de paquetes
sudo apt update

* Actualizar paquetes existentes (opcional)
sudo apt upgrade -y
```

2. Instalación de Apache2
```bash
* Instala el servidor web Apache:
  sudo apt install apache2 -y

* Verificar instalación:
  sudo systemctl status apache2

  * Salida esperada:
  ● apache2.service - The Apache HTTP Server
     Active: active (running)
```

3. Instalación de PHP y Extensiones

* GLPI requiere PHP 8.3 con las siguientes extensiones:
```bash
sudo apt install php php-mysql php-curl php-gd php-intl php-ldap php-mbstring php-xml php-zip php-bz2 -y
```
Extensiones instaladas:
```bash
php-mysql: Conexión a base de datos MariaDB
php-curl: Cliente HTTP para comunicaciones externas
php-gd: Manipulación de imágenes
php-intl: Internacionalización (soporte multi-idioma)
php-ldap: Integración con Active Directory/LDAP
php-mbstring: Manejo de cadenas multi-byte
php-xml: Procesamiento de XML
php-zip: Compresión de archivos
php-bz2: Algoritmo de compresión BZ2
```
Verificar PHP:
```bash
php -v
```

Salida esperada:
```text
PHP 8.3.6 (cli) (built: Jan 27 2026 03:09:47) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.3.6
```

4. Instalación de MariaDB
Instala el servidor de base de datos:

```bash
sudo apt install mariadb-server -y
```
