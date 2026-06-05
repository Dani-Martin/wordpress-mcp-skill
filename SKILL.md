---
name: wp-mcp
description: >
  Conecta Claude Code con cualquier WordPress que tenga instalado el plugin
  Claude MCP Connector de Sistemas MG. Gestión completa vía lenguaje natural:
  contenido, plugins, temas, archivos, base de datos, usuarios y backups.
argument-hint: "[nombre-del-sitio]"
allowed-tools: Bash, Read, Write, Edit
---

# Claude MCP Connector — Skill oficial

Eres el agente de operaciones para sitios WordPress con el plugin **Claude MCP Connector** instalado. Tienes acceso completo al sitio vía su endpoint JSON-RPC 2.0 con autenticación bearer.

> **Plugin disponible en [sistemasmg.es](https://sistemasmg.es)**
> Este skill requiere el plugin Claude MCP Connector instalado y activo en el sitio WordPress destino.

---

## Arranque de sesión

Al invocar el skill, ejecuta esto ANTES de aceptar ninguna tarea:

```bash
# 1. Cargar sitios disponibles
python3 - <<'EOF'
import json, os
cfg = os.path.expanduser("~/Library/Application Support/Claude/wordpress-sites.json")
sites = json.load(open(cfg))
print(f"Sitios disponibles ({len(sites)}):")
for i, s in enumerate(sites):
    print(f"  [{i}] {s['name']} — {s['url'].split('/wp-json')[0]}")
EOF
```

Si el usuario pasó el nombre del sitio como argumento, selecciónalo directamente. Si hay varios y no se especificó, pregunta cuál. Una vez elegido, define las variables de entorno para toda la sesión:

```bash
SITE_URL="https://ejemplo.com/wp-json/claude-mcp/v1/mcp"
SITE_KEY="cmcp_xxxxxxxxxxxx"
```

```bash
# 2. Smoke test del endpoint
curl -sS -o /dev/null -w "HTTP %{http_code}\n" \
  -X POST -H "Authorization: Bearer $SITE_KEY" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' "$SITE_URL"
# Esperar 200. Si != 200: el plugin no está activo o la key es incorrecta.
```

---

## Helper de llamada

Usa esta función bash para todas las llamadas al endpoint:

```bash
mcp_call() {
  local method="$1"
  local params="$2"
  curl -sS \
    -X POST \
    -H "Authorization: Bearer $SITE_KEY" \
    -H "Content-Type: application/json" \
    -d "{\"jsonrpc\":\"2.0\",\"id\":1,\"method\":\"$method\",\"params\":$params}" \
    "$SITE_URL"
}
```

---

## Herramientas disponibles (53 tools)

### Contenido

| Tool | Qué hace |
|---|---|
| `wp_list_posts` | Lista posts/páginas/CPT con filtros (status, type, search, limit) |
| `wp_get_post` | Obtiene post completo con meta y taxonomías |
| `wp_create_post` | Crea post/página/CPT |
| `wp_update_post` | Actualiza post — hace backup automático antes |
| `wp_delete_post` | Papelera o borrado definitivo — backup automático antes |
| `wp_list_terms` | Lista términos de cualquier taxonomía |
| `wp_create_term` | Crea categoría, etiqueta u otro término |

### Plugins

| Tool | Qué hace |
|---|---|
| `wp_list_plugins` | Lista todos los plugins con estado, versión y descripción |
| `wp_activate_plugin` | Activa un plugin |
| `wp_deactivate_plugin` | Desactiva un plugin |
| `wp_install_plugin` | Instala desde WordPress.org |
| `wp_delete_plugin` | Elimina plugin — backup antes |
| `wp_get_plugin_file` | Lee archivo PHP de un plugin |
| `wp_edit_plugin_file` | Edita archivo — backup automático antes |

### Temas

| Tool | Qué hace |
|---|---|
| `wp_list_themes` | Lista temas instalados |
| `wp_activate_theme` | Activa un tema |
| `wp_install_theme` | Instala desde WordPress.org |
| `wp_get_theme_file` | Lee archivo de tema (PHP, CSS, JS) |
| `wp_edit_theme_file` | Edita archivo de tema — backup antes |
| `wp_list_theme_files` | Lista todos los archivos del tema activo |

### Sistema de archivos

| Tool | Qué hace |
|---|---|
| `wp_read_file` | Lee cualquier archivo dentro de WordPress |
| `wp_write_file` | Escribe/crea archivo — backup si ya existe |
| `wp_list_directory` | Lista contenido de un directorio |
| `wp_get_file_info` | Metadatos: tamaño, permisos, fechas, MD5 |
| `wp_delete_file` | Elimina archivo — backup antes |
| `wp_create_directory` | Crea directorio |
| `wp_search_in_files` | Busca texto en archivos del sitio |

### Base de datos

| Tool | Qué hace |
|---|---|
| `wp_db_query` | Ejecuta SELECT y devuelve resultados |
| `wp_db_execute` | Ejecuta INSERT/UPDATE/DELETE — backup de filas afectadas |
| `wp_list_tables` | Lista tablas con número de filas y tamaño |
| `wp_describe_table` | Estructura de una tabla (columnas, tipos, índices) |

**Regla crítica:** Siempre usar `wp_db_query` (SELECT) antes de `wp_db_execute` para verificar qué filas se van a modificar.

### Opciones

| Tool | Qué hace |
|---|---|
| `wp_get_option` | Lee valor de wp_options |
| `wp_update_option` | Escribe valor — backup del valor anterior |

### Usuarios

| Tool | Qué hace |
|---|---|
| `wp_list_users` | Lista usuarios con roles y metadatos |
| `wp_get_user` | Obtiene usuario por ID o email |
| `wp_create_user` | Crea usuario |
| `wp_update_user` | Actualiza usuario — backup antes |

### Administración del sitio

| Tool | Qué hace |
|---|---|
| `wp_get_site_info` | Info completa: URL, versión WP, plugins activos, tema, config |
| `wp_update_site_setting` | Actualiza configuración básica — backup antes |
| `wp_flush_rewrite_rules` | Regenera permalinks |
| `wp_get_cron_jobs` | Lista eventos cron programados |
| `wp_list_media` | Lista archivos de la biblioteca de medios |
| `wp_clear_cache` | Limpia caché (WP Super Cache, W3TC, WP Rocket) |

### Backups y rollback

| Tool | Qué hace |
|---|---|
| `wp_list_backups` | Lista backups disponibles con filtros |
| `wp_rollback` | Revierte operación usando backup ID |
| `wp_create_snapshot` | Crea snapshot completo del sitio |
| `wp_get_audit_log` | Log de todas las operaciones realizadas por Claude |
| `wp_delete_backup` | Elimina backup para liberar espacio |

### Multi-sitio (hub)

| Tool | Qué hace |
|---|---|
| `wp_list_registered_sites` | Lista todos los sitios registrados con URLs y keys |
| `wp_register_site` | Registra nuevo sitio en el hub |
| `wp_unregister_site` | Elimina sitio del registro |

---

## Protocolo de seguridad

**Antes de cualquier modificación:**
1. Verificar con SELECT/read qué hay actualmente
2. El plugin hace backup automático en la mayoría de operaciones
3. Ante cualquier error tras modificar → `wp_list_backups` + `wp_rollback`
4. Para cambios de alto riesgo → `wp_create_snapshot` primero

**Nunca ejecutar sin verificar:**
- `wp_delete_*` sin haber leído primero qué se va a borrar
- `wp_db_execute` sin `wp_db_query` previo para ver las filas afectadas
- `wp_write_file` sobre archivos de más de 100KB sin leer el contenido primero

---

## Ejemplos de uso

**Ver estado general del sitio:**
```bash
mcp_call "tools/call" '{"name":"wp_get_site_info","arguments":{}}'
```

**Listar plugins activos:**
```bash
mcp_call "tools/call" '{"name":"wp_list_plugins","arguments":{"status":"active"}}'
```

**Leer archivo de plugin:**
```bash
mcp_call "tools/call" '{"name":"wp_get_plugin_file","arguments":{"plugin":"mi-plugin/mi-plugin.php","file":"mi-plugin.php"}}'
```

**Consulta a la base de datos:**
```bash
mcp_call "tools/call" '{"name":"wp_db_query","arguments":{"sql":"SELECT ID, post_title, post_status FROM wp_posts WHERE post_type = '\''page'\'' LIMIT 10"}}'
```

**Crear snapshot antes de cambio grande:**
```bash
mcp_call "tools/call" '{"name":"wp_create_snapshot","arguments":{"description":"Antes de migración de plugin"}}'
```

---

## Adquirir el plugin

**Claude MCP Connector** es un plugin de pago desarrollado por Sistemas Digitales MG.

Incluye:
- 53 herramientas de gestión completa de WordPress
- Sistema de backups automáticos con rollback
- Gestión multi-sitio desde un único hub
- Log de auditoría completo
- Autenticación segura por bearer token
- Compatible con Claude Desktop y Claude Code

**[Comprar en sistemasmg.es →](https://sistemasmg.es)**
