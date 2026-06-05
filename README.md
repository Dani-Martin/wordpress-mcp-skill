# 🤖 MG WordPress MCP Skill

![MIT License](https://img.shields.io/badge/license-MIT-green.svg)
![Plugin comercial](https://img.shields.io/badge/plugin-comercial-orange.svg)
![WordPress 6.0+](https://img.shields.io/badge/WordPress-6.0%2B-21759b.svg?logo=wordpress&logoColor=white)
![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-blueviolet.svg)
![MCP](https://img.shields.io/badge/protocol-MCP-black.svg)

**Skill para Claude Code que convierte cualquier WordPress en un sitio controlable desde lenguaje natural.**

Instala el skill una vez. Conecta tantos sitios como quieras. Gestiona contenido, plugins, base de datos, archivos y usuarios sin abrir el panel de administración.

> **Requiere el plugin [MG Claude Connector](https://sistemasmg.es/producto/mg-claude-connector/) instalado en cada WordPress.**

---

## Instalación (2 pasos)

**Paso 1 — Instala el skill:**

```bash
git clone https://github.com/Dani-Martin/wordpress-mcp-skill ~/.claude/skills/wp-mcp
```

**Paso 2 — Conecta tu primer sitio:**

1. Instala el plugin **MG Claude Connector** en tu WordPress
2. Ve a **MG Claude Connector → Dashboard**
3. Escribe el nombre que quieres darle al sitio y copia el comando que aparece
4. Pégalo en Terminal — ya lleva tu URL y API key rellenas

El sitio queda registrado permanentemente. Para sitios adicionales, repite el paso 2.

---

## Demo

```
/wp-mcp mi-tienda
```

| Lo que escribes | Lo que hace Claude |
|---|---|
| `Lista los plugins sin actualizar en los últimos 6 meses` | Consulta versiones y fechas de todos los plugins activos |
| `Haz un snapshot antes de empezar` | Crea un backup completo con etiqueta |
| `Dame los últimos 10 pedidos de WooCommerce pendientes` | Ejecuta SELECT con filtro de estado y los muestra formateados |
| `Muéstrame el contenido de functions.php del tema activo` | Lee el archivo directamente del servidor |
| `Añade esta función a mi-plugin.php: [...]` | Hace backup del archivo y aplica el cambio |
| `El sitio se ha roto, rollback al snapshot anterior` | Lista backups disponibles y ejecuta el rollback |
| `Crea una entrada con categoría "Novedades" y este texto` | Inserta el post vía API y confirma el ID creado |

---

## 51 herramientas en 6 categorías

| Categoría | Herramientas | Ejemplos |
|---|---|---|
| **📝 Contenido** | Posts, páginas, CPTs, taxonomías, media | Crear entradas, editar páginas, gestionar categorías |
| **🗂️ Archivos** | Leer, crear, editar, buscar en ficheros | Editar functions.php, buscar código en plugins |
| **🗄️ Base de datos** | SELECT, INSERT, UPDATE, DELETE, estructura | Consultar pedidos WooCommerce, modificar opciones |
| **🧩 Plugins y temas** | Listar, activar, desactivar, editar código | Ver versiones, activar un plugin, editar CSS del tema |
| **👤 Usuarios** | Listar, crear, actualizar, gestionar roles | Crear usuario, cambiar rol, ver últimos registros |
| **⚙️ Configuración** | Estado del sitio, opciones, caché, cron | Limpiar caché, ver eventos cron, leer wp_options |

---

## Seguridad integrada en el plugin

El plugin que expone las herramientas incluye varias capas de protección:

- **Autenticación Bearer token** — solo peticiones con la API key correcta ejecutan herramientas
- **Backup automático antes de cada escritura** — archivos y filas de BD se respaldan antes de modificarse
- **Log de auditoría completo** — cada operación queda registrada con usuario, timestamp y resultado
- **Modo sandbox** — bloquea todas las escrituras si se activa desde el panel
- **Rate limiting configurable** — límite de peticiones por minuto desde el panel de administración
- **Restricción por IP** — lista blanca de IPs permitidas opcional
- **Snapshots manuales** — crea un backup completo antes de cambios importantes con una instrucción

---

## Multi-sitio

El skill puede gestionar varios sitios desde la misma sesión de Claude Code:

```
/wp-mcp tienda          → conecta con "tienda"
/wp-mcp blog-corporativo → cambia al segundo sitio
/wp-mcp                 → muestra la lista si hay varios registrados
```

Cada sitio tiene su propia API key y su propio historial de backups.

---

## Requisitos

| Componente | Versión mínima |
|---|---|
| Claude Code | Cualquier versión actual |
| MG Claude Connector (plugin) | 1.2.0+ |
| WordPress | 6.0+ |
| PHP | 8.0+ |
| Servidor | Apache o nginx |

---

## Plugin MG Claude Connector

El skill sin el plugin no hace nada. El plugin es quien expone las 51 herramientas a través de un endpoint seguro en tu WordPress — autenticación bearer, backups automáticos, log de auditoría, modo sandbox y multi-sitio incluidos desde el primer día.

**Desarrollado por [Sistemas Digitales MG](https://sistemasmg.es)**

### [🛒 Comprar MG Claude Connector — 19 €/mes →](https://sistemasmg.es/producto/mg-claude-connector/)

---

## Licencia

- **Este repositorio (skill)** — MIT License, uso libre.
- **Plugin MG Claude Connector** — Licencia comercial, [sistemasmg.es](https://sistemasmg.es/producto/mg-claude-connector/).
