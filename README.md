# RomM-DS64

<p align="center">
  <img src="resources\romm.png" alt="RomM Logo" width="96" height="96" />
</p>

<p align="center">
  <strong>Plantilla y entorno de despliegue con Docker Compose para RomM (ROM Manager)</strong>
</p>

<p align="center">
  <a href="https://github.com/rommapp/romm"><img src="https://img.shields.io/badge/Powered%20By-RomM%20App-00c853?style=flat-square&logo=github" alt="RomM Project" /></a>
  <a href="https://www.docker.com/"><img src="https://img.shields.io/badge/Docker-Compose%20v3.8-2496ED?style=flat-square&logo=docker&logoColor=white" alt="Docker" /></a>
  <a href="https://mariadb.org/"><img src="https://img.shields.io/badge/Database-MariaDB%2010.11-C0765A?style=flat-square&logo=mariadb&logoColor=white" alt="MariaDB" /></a>
  <a href="https://www.igdb.com/api"><img src="https://img.shields.io/badge/Scraper-IGDB%20Twitch-9146FF?style=flat-square&logo=twitch&logoColor=white" alt="IGDB" /></a>
</p>

---

## Reconocimiento y Créditos

Este repositorio es una configuración de despliegue creada para facilitar la puesta en marcha de **RomM** en contenedores Docker.

Todo el desarrollo, diseño y código fuente original de la aplicación pertenecen a su autor original, **[zurdi15](https://github.com/zurdi15)**, y a la comunidad de colaboradores del proyecto oficial:

- Repositorio oficial de RomM: [https://github.com/rommapp/romm](https://github.com/rommapp/romm)
- Sitio web del proyecto: [https://romm.app](https://romm.app)

---

## Descripción del Proyecto

**RomM** (*ROM Manager*) es un gestor autoalojado de colecciones de videojuegos y emulación. Permite organizar bibliotecas por plataformas, escanear títulos, descargar carátulas y metadatos desde IGDB, y gestionar partidas guardadas.

**RomM-DS64** proporciona una arquitectura lista para desplegar compuesta por:
- Contenedor de la aplicación RomM (`rommapp/romm:latest`).
- Base de datos relacional MariaDB 10.11 (`mariadb:10.11`).
- Aislamiento de credenciales mediante variables de entorno en `.env`.
- Volúmenes persistentes para la base de datos, caché de Redis y recursos de la aplicación.

---

## Estructura del Repositorio

```plaintext
RomM-DS64/
│
├── .env.example          # Plantilla con las variables de entorno requeridas
├── .env                  # Archivo de credenciales locales (a crear, ignorado por Git)
├── .gitignore            # Exclusión de archivos sensibles
├── docker-compose.yml    # Definición del stack de servicios
└── README.md             # Documentación del despliegue
```

---

## Requisitos Previos

- Docker Engine (versión 20.10 o superior).
- Docker Compose (v2 o superior).
- Directorio local en el servidor o equipo con los archivos de ROMs.
- Opcional: Credenciales de Twitch Developer (Client ID y Client Secret) para el enriquecimiento automático de metadatos vía IGDB.

---

## Configuración y Despliegue

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/RomM-DS64.git
cd RomM-DS64
```

### 2. Crear y configurar el archivo de entorno

Copia la plantilla `.env.example` para generar tu archivo `.env`:

```bash
cp .env.example .env
```

Configura las variables dentro de `.env`:

```env
# Base de datos
DB_ROOT_PASSWORD=un_password_seguro_para_root
DB_USER=romm
DB_PASSWORD=un_password_seguro_para_romm
DB_NAME=romm

# Clave de cifrado de RomM (cadena aleatoria)
ROMM_AUTH_SECRET_KEY=clave_aleatoria_larga

# Credenciales de IGDB (opcional para descarga de metadatos)
IGDB_CLIENT_ID=tu_client_id
IGDB_CLIENT_SECRET=tu_client_secret
```

Nota: Para generar una clave aleatoria para `ROMM_AUTH_SECRET_KEY`, puedes ejecutar:
```bash
openssl rand -hex 32
```

### 3. Configurar la ruta de las ROMs

En `docker-compose.yml`, ajusta el volumen local del servicio `romm` apuntando a la ruta donde se encuentra tu colección:

```yaml
    volumes:
      - romm_resources:/romm/resources
      - romm_redis_data:/redis-data
      # Sustituye la ruta de la izquierda por tu ruta local:
      - /ruta/a/tus/roms:/romm/library
```

### 4. Lanzar el stack

Inicia los contenedores en segundo plano:

```bash
docker compose up -d
```

---

## Configuración de Metadatos (IGDB / Twitch)

Si vas a utilizar el scraper de metadatos de IGDB:

1. Accede a [Twitch Developer Console](https://dev.twitch.tv/console/apps).
2. Registra una aplicación (categoría *Website Integration*, con URL de redirección `http://localhost`).
3. Obtén el **Client ID** y genera un **Client Secret**.
4. Añade ambos valores a `IGDB_CLIENT_ID` e `IGDB_CLIENT_SECRET` en el archivo `.env`.

---

## Mantenimiento

### Consultar logs

```bash
# Ver logs de todo el stack
docker compose logs -f

# Ver logs del contenedor de RomM
docker compose logs -f romm

# Ver logs de la base de datos
docker compose logs -f romm-db
```

### Detener los servicios

```bash
docker compose down
```

### Reiniciar los servicios

```bash
docker compose restart
```

### Actualizar imágenes

```bash
docker compose pull
docker compose up -d
```

---

## Licencia y Atribución

- **RomM**: Licenciado bajo GNU General Public License v3.0 (GPLv3). Código fuente original y derechos pertenecientes al proyecto [rommapp](https://github.com/rommapp).
- **RomM-DS64**: Configuración de despliegue abierta a la comunidad.
