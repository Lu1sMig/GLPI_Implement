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

5. Creación de Base de Datos
* Accede a MariaDB y crea la base de datos para GLPI:
```bash
sudo mysql -u root -p
```
Si no tienes contraseña, presiona Enter.

Dentro de la consola de MariaDB, ejecuta:

```sql
-- Crear base de datos con soporte UTF-8
CREATE DATABASE glpidb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Crear usuario específico para GLPI
CREATE USER 'glpiuser'@'localhost' IDENTIFIED BY 'glpi123';

-- Otorgar permisos
GRANT ALL PRIVILEGES ON glpidb.* TO 'glpiuser'@'localhost';

-- Aplicar cambios
FLUSH PRIVILEGES;

-- Salir
EXIT;
```

6. Descarga de GLPI
Descarga la versión estable más reciente:
```bash
# Ir al directorio web
cd /var/www

# Descargar GLPI versión 10.0.15
sudo wget https://github.com/glpi-project/glpi/releases/download/10.0.15/glpi-10.0.15.tgz

# Descomprimir
sudo tar -xzvf glpi-10.0.15.tgz

# Eliminar archivo comprimido (opcional)
sudo rm glpi-10.0.15.tgz
```

7. Configuración de Permisos
Establece los permisos correctos para que Apache pueda leer y escribir:
```bash
# Cambiar propietario al usuario de Apache
sudo chown -R www-data:www-data glpi

# Establecer permisos
sudo chmod -R 755 glpi
📌 Nota: Los directorios que requieren escritura son:

glpi/files/ - Archivos subidos, logs, caché

glpi/config/ - Configuración de la instancia

glpi/marketplace/ - Plugins instalados
```
8. Configuración de Apache
Edita la configuración del sitio principal:
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
Modifica el archivo para que quede así:

apache
<VirtualHost *:8080>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/glpi/public

    <Directory /var/www/glpi/public>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
Habilitar módulos y reiniciar:

bash
# Habilitar módulo de reescritura
sudo a2enmod rewrite

# Reiniciar Apache
sudo systemctl restart apache2
```
9. Configuración Adicional
Para acceder a GLPI desde la raíz web por defecto:
```bash
# Limpiar la raíz web por defecto
sudo rm -rf /var/www/html/*

# Crear enlace simbólico
sudo ln -s /var/www/glpi /var/www/html/glpi

# Reiniciar Apache
sudo systemctl restart apache2
```
10. Verificación Final
Comprueba que Apache esté funcionando:
```bash
sudo systemctl status apache2
Salida esperada:

text
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled)
     Active: active (running)
Verifica la configuración de sitios:
```
```bash
sudo apachectl -S
Salida esperada:
```
```text
VirtualHost configuration:
*:8080                 LAPTOP-DIFRES8C.localdomain (/etc/apache2/sites-enabled/000-default.conf:1)
```

## 🎯 Post-Instalación
Después de completar la instalación técnica, sigue estos pasos:

1. Acceder a la Interfaz Web
Abre tu navegador y accede a:

* http://localhost:8080 (si usas WSL con Windows)
* http://<IP-de-tu-WSL>:8080 (desde otro dispositivo en la red)
* http://localhost/glpi (si usas el enlace simbólico)

2. Configuración Inicial
El asistente web te guiará en:

+ Selección de idioma: Elige Español
+ Aceptar licencia: Licencia GNU GPL v2
+ Verificación de requisitos: Confirma que todo esté en verde

+ Configuración de base de datos:
Servidor: localhost
Usuario: glpiuser
Contraseña: glpi123
Base de datos: glpidb

Configuración administrativa:
Usuario admin: glpi

Contraseña: glpi (cámbiala inmediatamente)

3. Cambios Recomendados Inmediatos
```sql
-- Conéctate a MariaDB
sudo mysql -u root -p

-- Cambiar contraseña del usuario de GLPI
ALTER USER 'glpiuser'@'localhost' IDENTIFIED BY 'nueva_contraseña_segura';

-- Cambiar contraseña del root de MariaDB
ALTER USER 'root'@'localhost' IDENTIFIED BY 'nueva_root_password';
```
## 🔧 Solución de Problemas
❌ Error: "No se puede conectar a la base de datos"
Causa: Credenciales incorrectas o servicio detenido

Solución:
```bash
# Verificar servicio
sudo systemctl status mariadb

# Iniciar si está detenido
sudo systemctl start mariadb

# Verificar credenciales
sudo mysql -u glpiuser -p
```
❌ Error: "Permiso denegado" al guardar archivos
Causa: Permisos incorrectos en el directorio

Solución:
```bash
sudo chown -R www-data:www-data /var/www/glpi
sudo chmod -R 755 /var/www/glpi
sudo chmod -R 775 /var/www/glpi/files
```

❌ Error: "Puerto 8080 ya está en uso"
Causa: Otro servicio usando el puerto
Solución:
```bash
# Verificar qué usa el puerto
sudo netstat -tulpn | grep 8080

# Cambiar puerto en /etc/apache2/ports.conf y /etc/apache2/sites-available/000-default.conf
```
❌ Error 403 Forbidden
Causa: Configuración de Apache incorrecta
Solución:
```bash
# Verificar permisos de directorio
ls -la /var/www/glpi

# Verificar configuración de Apache
sudo apachectl configtest

# Reiniciar Apache
sudo systemctl restart apache2
```
💾 Mantenimiento y Respaldo
Script de Respaldo Automático
Crea un script de respaldo:

```bash
# Crear directorio de respaldos
sudo mkdir -p /backups/glpi

# Crear script
sudo nano /usr/local/bin/backup_glpi.sh
```
Contenido del script:
```bash
#!/bin/bash
# Script de respaldo de GLPI
# Autor: [Tu Nombre]
# Fecha: Marzo 2026

BACKUP_DIR="/backups/glpi"
DATE=$(date +"%Y%m%d_%H%M%S")
DB_NAME="glpidb"
DB_USER="glpiuser"
DB_PASS="glpi123"

# Crear directorio de respaldo
mkdir -p $BACKUP_DIR

# Respaldo de base de datos
mysqldump -u $DB_USER -p$DB_PASS $DB_NAME > $BACKUP_DIR/glpi_db_$DATE.sql

# Respaldo de archivos
tar -czf $BACKUP_DIR/glpi_files_$DATE.tar.gz /var/www/glpi

# Eliminar respaldos mayores a 30 días
find $BACKUP_DIR -type f -mtime +30 -delete

echo "Respaldo completado: $DATE"
```
Hacer ejecutable el script:

```bash
sudo chmod +x /usr/local/bin/backup_glpi.sh
```
Programar respaldos automáticos (cron)
```bash
# Editar crontab del usuario
sudo crontab -e

# Agregar línea para ejecutar a las 2 AM todos los días
0 2 * * * /usr/local/bin/backup_glpi.sh
```
Restauración desde Respaldo
```bash
# Restaurar base de datos
sudo mysql -u root -p glpidb < /backups/glpi/glpi_db_YYYYMMDD_HHMMSS.sql

# Restaurar archivos
sudo tar -xzf /backups/glpi/glpi_files_YYYYMMDD_HHMMSS.tar.gz -C /

# Restaurar permisos
sudo chown -R www-data:www-data /var/www/glpi
sudo chmod -R 755 /var/www/glpi
```
## 🔒 Recomendaciones de Seguridad
Cambiar contraseñas por defecto:

Usuario glpi (admin) en GLPI

Usuario glpiuser en MariaDB

Usuario root en MariaDB

Configurar firewall:

bash
# En Windows (PowerShell como administrador)
New-NetFirewallRule -DisplayName "WSL GLPI 8080" -Direction Inbound -Protocol TCP -LocalPort 8080 -Action Allow
Habilitar HTTPS:

bash
# Instalar Let's Encrypt
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d tudominio.com
Limitar acceso por IP:

apache
# En la configuración de Apache
<Directory /var/www/glpi/public>
    Require ip 192.168.0.0/16
    Require local
</Directory>
📚 Referencias
Sitio oficial de GLPI

Documentación GLPI

Repositorio GLPI en GitHub

Documentación WSL de Microsoft

PHP 8.3 Manual

MariaDB Knowledge Base

👤 Autor y Contribuciones
Autor: [Tu Nombre]
Empresa: [Nombre de tu Empresa]
Contacto: [tu.email@empresa.com]

📝 Contribuciones
Las contribuciones son bienvenidas. Por favor:

Fork el repositorio

Crea una rama para tu feature (git checkout -b feature/AmazingFeature)

Commit tus cambios (git commit -m 'Add some AmazingFeature')

Push a la rama (git push origin feature/AmazingFeature)

Abre un Pull Request

📄 Licencia
Distribuido bajo la Licencia MIT. Ver LICENSE para más información.

⚡ Comandos Rápidos (Resumen)
bash
# Reiniciar servicios
sudo systemctl restart apache2
sudo systemctl restart mariadb

# Verificar logs
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/mysql/error.log

# Verificar configuración
sudo apachectl configtest
sudo mysqlcheck -u root -p --all-databases

# Conectarse a MariaDB
sudo mysql -u root -p

# Cambiar permisos (si hay problemas)
sudo chown -R www-data:www-data /var/www/glpi
sudo chmod -R 755 /var/www/glpi
🎉 ¡GLPI está listo para usar!

Para cualquier problema o consulta, revisa la sección de Solución de Problemas o abre un Issue en este repositorio.

text

---

## 📦 Contenido Adicional para tu Repositorio

### 1. Script de Instalación Automática (`scripts/install_glpi.sh`)

```bash
#!/bin/bash
# Instalación automática de GLPI en WSL
# Uso: chmod +x install_glpi.sh && sudo ./install_glpi.sh

set -e

echo "🚀 Iniciando instalación de GLPI..."

# Variables
DB_NAME="glpidb"
DB_USER="glpiuser"
DB_PASS="glpi123"
GLPI_VERSION="10.0.15"

# Actualizar sistema
echo "📦 Actualizando sistema..."
apt update && apt upgrade -y

# Instalar Apache
echo "🌐 Instalando Apache..."
apt install apache2 -y

# Instalar PHP y extensiones
echo "🐘 Instalando PHP y extensiones..."
apt install php php-mysql php-curl php-gd php-intl php-ldap php-mbstring php-xml php-zip php-bz2 -y

# Instalar MariaDB
echo "🗄️ Instalando MariaDB..."
apt install mariadb-server -y

# Configurar base de datos
echo "💾 Configurando base de datos..."
mysql -u root -e "CREATE DATABASE $DB_NAME CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
mysql -u root -e "CREATE USER '$DB_USER'@'localhost' IDENTIFIED BY '$DB_PASS';"
mysql -u root -e "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'localhost';"
mysql -u root -e "FLUSH PRIVILEGES;"
```
# Descargar GLPI

```bash
echo "📥 Descargando GLPI..."
cd /var/www
wget https://github.com/glpi-project/glpi/releases/download/$GLPI_VERSION/glpi-$GLPI_VERSION.tgz
tar -xzvf glpi-$GLPI_VERSION.tgz
rm glpi-$GLPI_VERSION.tgz
```

# Configurar permisos
```bash
echo "🔧 Configurando permisos..."
chown -R www-data:www-data glpi
chmod -R 755 glpi

# Configurar Apache
echo "⚙️ Configurando Apache..."
cat > /etc/apache2/sites-available/000-default.conf << EOF
<VirtualHost *:8080>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/glpi/public
    <Directory /var/www/glpi/public>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOF
```
# Habilitar módulos y reiniciar
```bash
a2enmod rewrite
systemctl restart apache2

# Crear enlace simbólico
rm -rf /var/www/html/*
ln -s /var/www/glpi /var/www/html/glpi

echo "✅ Instalación completada!"
echo "🌐 Accede a GLPI en: http://localhost/glpi o http://localhost:8080"
echo "📊 Datos de base de datos:"
echo "   - Base: $DB_NAME"
echo "   - Usuario: $DB_USER"
echo "   - Contraseña: $DB_PASS"
2. Archivo .gitignore
gitignore
# Archivos de sistema
.DS_Store
Thumbs.db

# Archivos de backup
*.sql
*.tar.gz
*.tgz
/backups/

# Archivos de configuración con datos sensibles
config/glpi.conf
config/my.cnf

# Logs
*.log

# Archivos temporales
*.tmp
*.swp
*~

# Directorios de IDE
.vscode/
.idea/
3. Archivo LICENSE
txt
MIT License

Copyright (c) 2026 [Tu Nombre]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

🚀 Pasos para Subir a GitHub

Crear el repositorio en GitHub:

Ve a github.com/new

Nombre: glpi-wsl-installation

Descripción: "Instalación automatizada de GLPI en WSL para entornos empresariales"

Público o Privado (según prefieras)

Inicializar el repositorio local:

```bash

mkdir glpi-wsl-installation
cd glpi-wsl-installation
git init
Crear la estructura de directorios:
```
```bash

mkdir docs scripts config assets
Agregar todos los archivos:
```
```bash 
# Copiar los archivos creados a sus respectivas carpetas
# Luego:
git add .
git commit -m "feat: Manual completo de instalación GLPI en WSL"
Conectar con GitHub y subir:
```
```bash
git remote add origin https://github.com/tu-usuario/glpi-wsl-installation.git
git branch -M main
git push -u origin main
```
📊 Ideas para Expandir tu Proyecto
Video tutorial: Graba una demostración de la instalación

Docker support: Agrega un docker-compose.yml para despliegue con contenedores

Ansible playbook: Automatización avanzada

CI/CD Pipeline: Pruebas automáticas de la instalación

Configuraciones avanzadas:

Integración con Active Directory

Configuración de correo SMTP

Plugins recomendados (FusionInventory, PDF Export, etc.)

Estadísticas: Dashboard con uso de GLPI

Traducciones: Versión del manual en inglés

Este repositorio no solo documenta la instalación que realizaste, sino que se convierte en un activo técnico valioso para tu empresa y para la comunidad. ¡Excelente trabajo! 🎉

