# wordpress-mcp-skill

**Skill oficial para Claude Code** que conecta con cualquier WordPress donde esté instalado el plugin **[MG Claude Connector](https://sistemasmg.es)**.

Gestión completa de WordPress desde lenguaje natural — sin paneles, sin clics, sin salir de Claude Code.

> **Requiere el plugin MG Claude Connector instalado en tu WordPress.**
> [Consíguelo en sistemasmg.es →](https://sistemasmg.es)

---

## Instalación del skill

```bash
git clone https://github.com/Dani-Martin/wordpress-mcp-skill ~/.claude/skills/wp-mcp
```

Eso es todo. El skill queda disponible como `/wp-mcp` en Claude Code.

---

## Primer uso — configurar un sitio

No necesitas editar ningún archivo a mano. Abre Claude Code y escribe:

```
/wp-mcp

Añade mi sitio: nombre "Mi WordPress", endpoint https://miweb.com/wp-json/claude-mcp/v1/mcp, key cmcp_xxxxxxxxxxxx
```

Claude crea la configuración automáticamente y verifica la conexión. Listo.

> La URL del endpoint y la API key las encuentras en tu WordPress Admin bajo **MG Claude Connector → Configuración**.

---

## Uso diario

```
/wp-mcp
/wp-mcp mi-sitio        (si tienes varios sitios configurados)
```

A partir de ahí hablas directamente:

```
Lista los plugins que llevan más de 6 meses sin actualizar

Muéstrame el contenido de functions.php del tema activo

Haz un snapshot antes de empezar a trabajar

Dame los últimos 10 pedidos de WooCommerce con estado pending

Edita el archivo mi-plugin.php y añade esta función: [...]

Algo ha fallado, haz rollback al último backup
```

---

## ¿Qué puedes hacer?

### Contenido
- Listar, buscar, crear, editar y eliminar posts, páginas y custom post types
- Gestionar categorías, etiquetas y taxonomías personalizadas

### Plugins y temas
- Ver estado, versión y descripción de todos los plugins
- Activar, desactivar, instalar y eliminar plugins
- Leer y editar archivos PHP/CSS/JS de plugins y temas directamente

### Sistema de archivos
- Leer cualquier archivo del sitio
- Crear y editar archivos con backup automático previo
- Buscar texto en todos los archivos del proyecto

### Base de datos
- Ejecutar consultas SELECT y ver resultados formateados
- Ejecutar INSERT, UPDATE, DELETE con backup automático de filas afectadas
- Ver estructura de tablas y listado completo de la base de datos

### Usuarios y opciones
- Listar, crear y actualizar usuarios con sus roles
- Leer y escribir cualquier opción de wp_options

### Administración
- Ver estado completo del sitio (versión WP, plugins activos, tema, config)
- Limpiar caché (WP Super Cache, W3TC, WP Rocket)
- Ver y gestionar eventos cron

### Backups y seguridad
- El plugin hace **backup automático** antes de cualquier operación de escritura
- Crear snapshots manuales antes de cambios importantes
- Ver el historial de backups y hacer rollback con un comando
- Log de auditoría completo de todo lo que ha hecho Claude

### Multi-sitio
- Gestionar varios sitios WordPress desde un mismo hub
- Añadir y eliminar sitios con un mensaje a Claude

---

## Comandos de configuración

| Qué quieres hacer | Qué le dices a Claude |
|---|---|
| Añadir un sitio nuevo | `Añade el sitio "Nombre", endpoint [url], key [key]` |
| Ver sitios configurados | `¿Qué sitios tengo configurados?` |
| Cambiar de sitio activo | `Cambia al sitio "Nombre"` |
| Eliminar un sitio | `Elimina el sitio "Nombre" de la configuración` |

---

## Requisitos

- **Claude Code** (CLI, app Mac/Windows o web)
- Plugin **MG Claude Connector** instalado y activo en el sitio WordPress
- WordPress 5.8 o superior, PHP 7.4 o superior

---

## Plugin MG Claude Connector

Este skill sin el plugin no hace nada. El plugin es lo que expone las 53 herramientas a través de un endpoint seguro en tu WordPress — backups automáticos, log de auditoría, multi-sitio y autenticación por bearer token incluidos.

**Desarrollado por [Sistemas Digitales MG](https://sistemasmg.es)**

**[Comprar MG Claude Connector →](https://sistemasmg.es)**

---

## Licencia

El skill (este repositorio) — MIT License, uso libre.
El plugin MG Claude Connector — Licencia comercial, [sistemasmg.es](https://sistemasmg.es).
