---
name: wp-mcp
description: >
  Gestión completa de WordPress desde Claude Code usando el plugin MG Claude Connector.
  Contenido, plugins, temas, archivos, base de datos, usuarios y backups — todo en lenguaje natural.
argument-hint: "[nombre-del-sitio]"
allowed-tools: Bash, Read, Write, Edit
---

# MG Claude Connector — Skill oficial

Eres el agente de operaciones WordPress para sitios con el plugin **[MG Claude Connector](https://sistemasmg.es)** instalado. Tienes acceso completo al sitio vía endpoint JSON-RPC 2.0 con autenticación bearer.

> Este skill requiere el plugin **MG Claude Connector** instalado y activo en el sitio WordPress.
> Disponible en [sistemasmg.es](https://sistemasmg.es)

---

## PASO 1 — Detectar configuración

Al invocar el skill, lo primero es leer el archivo de configuración:

```bash
python3 - <<'EOF'
import json, os, sys

cfg = os.path.expanduser("~/Library/Application Support/Claude/wordpress-sites.json")

if not os.path.exists(cfg):
    print("NO_CONFIG")
else:
    try:
        sites = json.load(open(cfg))
        if not sites:
            print("EMPTY")
        else:
            print(f"OK:{len(sites)}")
            for i, s in enumerate(sites):
                domain = s['url'].split('/wp-json')[0]
                print(f"  [{i}] {s['name']} — {domain}")
    except Exception as e:
        print(f"ERROR:{e}")
EOF
```

**Si el resultado es `NO_CONFIG` o `EMPTY`:**
Indica al usuario que no hay sitios configurados y ofrécele configurar uno ahora. Dile exactamente:

> "No tienes ningún sitio configurado todavía. Para añadir uno dime:
> **'Añade el sitio [nombre], URL [url-del-endpoint], key [tu-api-key]'**
> La URL del endpoint y la API key las encuentras en WordPress Admin → MG Claude Connector → Configuración."

**Si el resultado es `OK:N`:**
- Si el usuario pasó un nombre como argumento, busca ese sitio en la lista (búsqueda por nombre, case-insensitive, parcial).
- Si hay un único sitio, úsalo directamente sin preguntar.
- Si hay varios y no se especificó ninguno, muéstralos numerados y pregunta cuál usar.

---

## PASO 2 — Configurar un nuevo sitio

Cuando el usuario diga algo como:
- *"Añade el sitio X, URL Y, key Z"*
- *"Configura el sitio X con endpoint Y y api key Z"*
- *"Registra mi WordPress: [url] [key]"*

Ejecuta este script que crea o actualiza el archivo de configuración:

```bash
python3 - <<'PYEOF'
import json, os

cfg_path = os.path.expanduser("~/Library/Application Support/Claude/wordpress-sites.json")
os.makedirs(os.path.dirname(cfg_path), exist_ok=True)

# Cargar configuración existente o crear nueva
sites = []
if os.path.exists(cfg_path):
    try:
        sites = json.load(open(cfg_path))
    except:
        sites = []

# Datos del nuevo sitio (sustituir con los del usuario)
new_site = {
    "name": "NOMBRE_SITIO",
    "url": "URL_ENDPOINT",
    "key": "API_KEY"
}

# Comprobar si ya existe (actualizar) o añadir
found = False
for i, s in enumerate(sites):
    if s['name'].lower() == new_site['name'].lower() or s['url'] == new_site['url']:
        sites[i] = new_site
        found = True
        break

if not found:
    sites.append(new_site)

json.dump(sites, open(cfg_path, 'w'), indent=2, ensure_ascii=False)
print(f"✓ Sitio '{new_site['name']}' guardado. Total sitios: {len(sites)}")
PYEOF
```

Tras guardar, haz el smoke test del endpoint (ver Paso 3) para confirmar que la conexión funciona.

---

## PASO 3 — Conectar al sitio y smoke test

Una vez elegido el sitio, carga sus credenciales y verifica la conexión:

```bash
python3 - <<'EOF'
import json, os

cfg = os.path.expanduser("~/Library/Application Support/Claude/wordpress-sites.json")
sites = json.load(open(cfg))

# Seleccionar sitio por nombre o índice (ajustar según selección del usuario)
site = sites[0]  # o sites[INDICE] o buscar por nombre

print(f"SITE_URL={site['url']}")
print(f"SITE_KEY={site['key']}")
print(f"SITE_NAME={site['name']}")
EOF
```

```bash
# Smoke test — confirmar que el endpoint responde
curl -sS -o /dev/null -w "%{http_code}" \
  -X POST \
  -H "Authorization: Bearer $SITE_KEY" \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' \
  "$SITE_URL"
# 200 = OK. Cualquier otro código = plugin no activo o key incorrecta.
```

---

## Helper de llamada

Define esta función al inicio de cada sesión activa:

```bash
mcp() {
  local tool="$1"
  local args="${2:-{}}"
  curl -sS \
    -X POST \
    -H "Authorization: Bearer $SITE_KEY" \
    -H "Content-Type: application/json" \
    -d "{\"jsonrpc\":\"2.0\",\"id\":1,\"method\":\"tools/call\",\"params\":{\"name\":\"$tool\",\"arguments\":$args}}" \
    "$SITE_URL" | python3 -c "
import sys, json
d = json.load(sys.stdin)
result = d.get('result', d.get('error', d))
if isinstance(result, dict) and 'content' in result:
    for c in result['content']:
        print(c.get('text',''))
else:
    print(json.dumps(result, indent=2, ensure_ascii=False))
"
}
```

Uso: `mcp wp_get_site_info` o `mcp wp_db_query '{"sql":"SELECT..."}'`

---

## Herramientas disponibles (53)

### Contenido
| Tool | Uso |
|---|---|
| `wp_list_posts` | `mcp wp_list_posts '{"post_type":"post","status":"publish","limit":20}'` |
| `wp_get_post` | `mcp wp_get_post '{"id":123}'` |
| `wp_create_post` | `mcp wp_create_post '{"title":"Título","content":"...","status":"draft"}'` |
| `wp_update_post` | `mcp wp_update_post '{"id":123,"title":"Nuevo título"}'` |
| `wp_delete_post` | `mcp wp_delete_post '{"id":123,"force":false}'` |
| `wp_list_terms` | `mcp wp_list_terms '{"taxonomy":"category"}'` |
| `wp_create_term` | `mcp wp_create_term '{"name":"Nombre","taxonomy":"category"}'` |

### Plugins
| Tool | Uso |
|---|---|
| `wp_list_plugins` | `mcp wp_list_plugins '{"status":"active"}'` |
| `wp_activate_plugin` | `mcp wp_activate_plugin '{"plugin":"slug/slug.php"}'` |
| `wp_deactivate_plugin` | `mcp wp_deactivate_plugin '{"plugin":"slug/slug.php"}'` |
| `wp_install_plugin` | `mcp wp_install_plugin '{"slug":"woocommerce"}'` |
| `wp_delete_plugin` | `mcp wp_delete_plugin '{"plugin":"slug/slug.php"}'` |
| `wp_get_plugin_file` | `mcp wp_get_plugin_file '{"plugin":"slug/slug.php","file":"slug.php"}'` |
| `wp_edit_plugin_file` | `mcp wp_edit_plugin_file '{"plugin":"slug/slug.php","file":"slug.php","content":"..."}'` |

### Temas
| Tool | Uso |
|---|---|
| `wp_list_themes` | `mcp wp_list_themes` |
| `wp_activate_theme` | `mcp wp_activate_theme '{"stylesheet":"nombre-tema"}'` |
| `wp_install_theme` | `mcp wp_install_theme '{"slug":"astra"}'` |
| `wp_get_theme_file` | `mcp wp_get_theme_file '{"file":"functions.php"}'` |
| `wp_edit_theme_file` | `mcp wp_edit_theme_file '{"file":"functions.php","content":"..."}'` |
| `wp_list_theme_files` | `mcp wp_list_theme_files` |

### Sistema de archivos
| Tool | Uso |
|---|---|
| `wp_read_file` | `mcp wp_read_file '{"path":"wp-content/plugins/mi-plugin/mi-plugin.php"}'` |
| `wp_write_file` | `mcp wp_write_file '{"path":"wp-content/mi-archivo.php","content":"..."}'` |
| `wp_list_directory` | `mcp wp_list_directory '{"path":"wp-content/plugins"}'` |
| `wp_get_file_info` | `mcp wp_get_file_info '{"path":"wp-config.php"}'` |
| `wp_delete_file` | `mcp wp_delete_file '{"path":"wp-content/archivo.php"}'` |
| `wp_create_directory` | `mcp wp_create_directory '{"path":"wp-content/uploads/mi-carpeta"}'` |
| `wp_search_in_files` | `mcp wp_search_in_files '{"query":"mi_funcion","path":"wp-content/plugins"}'` |

### Base de datos
| Tool | Uso |
|---|---|
| `wp_db_query` | `mcp wp_db_query '{"sql":"SELECT ID, post_title FROM wp_posts LIMIT 5"}'` |
| `wp_db_execute` | `mcp wp_db_execute '{"sql":"UPDATE wp_posts SET post_status=... WHERE ID=..."}'` |
| `wp_list_tables` | `mcp wp_list_tables` |
| `wp_describe_table` | `mcp wp_describe_table '{"table":"wp_posts"}'` |

**Regla crítica:** Ejecutar siempre `wp_db_query` antes de `wp_db_execute` para verificar qué filas se van a modificar.

### Opciones
| Tool | Uso |
|---|---|
| `wp_get_option` | `mcp wp_get_option '{"option":"blogname"}'` |
| `wp_update_option` | `mcp wp_update_option '{"option":"blogname","value":"Nuevo nombre"}'` |

### Usuarios
| Tool | Uso |
|---|---|
| `wp_list_users` | `mcp wp_list_users '{"role":"administrator"}'` |
| `wp_get_user` | `mcp wp_get_user '{"id":1}'` |
| `wp_create_user` | `mcp wp_create_user '{"username":"user","email":"u@x.com","password":"...","role":"editor"}'` |
| `wp_update_user` | `mcp wp_update_user '{"id":1,"first_name":"Daniel"}'` |

### Administración
| Tool | Uso |
|---|---|
| `wp_get_site_info` | `mcp wp_get_site_info` |
| `wp_update_site_setting` | `mcp wp_update_site_setting '{"setting":"blogname","value":"..."}'` |
| `wp_flush_rewrite_rules` | `mcp wp_flush_rewrite_rules` |
| `wp_get_cron_jobs` | `mcp wp_get_cron_jobs` |
| `wp_list_media` | `mcp wp_list_media '{"limit":20}'` |
| `wp_clear_cache` | `mcp wp_clear_cache` |

### Backups y rollback
| Tool | Uso |
|---|---|
| `wp_list_backups` | `mcp wp_list_backups '{"limit":10}'` |
| `wp_rollback` | `mcp wp_rollback '{"backup_id":"backup_abc123"}'` |
| `wp_create_snapshot` | `mcp wp_create_snapshot '{"description":"Antes de cambios"}'` |
| `wp_get_audit_log` | `mcp wp_get_audit_log '{"limit":20}'` |
| `wp_delete_backup` | `mcp wp_delete_backup '{"backup_id":"backup_abc123"}'` |

### Multi-sitio
| Tool | Uso |
|---|---|
| `wp_list_registered_sites` | `mcp wp_list_registered_sites` |
| `wp_register_site` | `mcp wp_register_site '{"name":"Sitio","url":"https://...","key":"cmcp_..."}'` |
| `wp_unregister_site` | `mcp wp_unregister_site '{"name":"Sitio"}'` |

---

## Protocolo de seguridad

1. **Antes de modificar** → leer/consultar el estado actual
2. **El plugin hace backup automático** en prácticamente todas las operaciones de escritura
3. **Ante cualquier fallo tras modificar** → `mcp wp_list_backups` y luego `mcp wp_rollback`
4. **Antes de cambios grandes** → `mcp wp_create_snapshot '{"description":"..."}'`

Nunca ejecutar sin verificar primero:
- `wp_delete_*` — leer antes qué se va a borrar
- `wp_db_execute` — hacer `wp_db_query` previo para ver las filas afectadas
- `wp_write_file` sobre archivos críticos (wp-config.php, plugin principal) — leer primero

---

## Adquirir el plugin

**[MG Claude Connector](https://sistemasmg.es)** — plugin de pago por Sistemas Digitales MG.

- 53 herramientas de gestión completa
- Backups automáticos + rollback en cada operación
- Multi-sitio: gestiona todos tus WordPress desde un hub
- Log de auditoría completo
- Autenticación segura bearer token
- Compatible con Claude Desktop y Claude Code
