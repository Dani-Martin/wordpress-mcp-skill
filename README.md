# wordpress-mcp-skill

**Skill oficial para Claude Code** que conecta con cualquier WordPress donde esté instalado el plugin **[Claude MCP Connector](https://sistemasmg.es)**.

Gestión completa de WordPress desde lenguaje natural: contenido, plugins, temas, archivos, base de datos, usuarios y backups — sin salir de Claude Code.

---

## ¿Qué puedes hacer?

```
/wp-mcp mi-sitio

> Lista los plugins activos y dime cuáles llevan más de 6 meses sin actualizar
> Crea un snapshot del sitio antes de empezar
> Busca en los archivos del plugin "woocommerce" la función que calcula el precio
> Haz SELECT de los últimos 10 pedidos con estado pending
> Edita el archivo functions.php y añade este snippet: [...]
> Algo ha fallado, haz rollback al último backup
```

---

## Requisitos

- **Claude Code** (CLI o app)
- Plugin **Claude MCP Connector** instalado y activo en el sitio WordPress
- Archivo de configuración `~/Library/Application Support/Claude/wordpress-sites.json`

> El plugin Claude MCP Connector es de pago. **[Consíguelo en sistemasmg.es →](https://sistemasmg.es)**

---

## Instalación del skill

```bash
# Clonar en tu directorio de skills de Claude
git clone https://github.com/Dani-Martin/wordpress-mcp-skill ~/.claude/skills/wp-mcp
```

O copiar manualmente `SKILL.md` a `~/.claude/skills/wp-mcp/SKILL.md`.

### Configurar los sitios

Crea o edita `~/Library/Application Support/Claude/wordpress-sites.json`:

```json
[
  {
    "name": "Mi sitio",
    "url": "https://ejemplo.com/wp-json/claude-mcp/v1/mcp",
    "key": "cmcp_tu_api_key_aqui"
  }
]
```

La API key la encuentras en WordPress Admin → **Claude MCP → Configuración**.

---

## Uso

```
/wp-mcp [nombre-del-sitio]
```

Si tienes un solo sitio configurado, se conecta directamente. Si tienes varios, te muestra la lista para elegir.

---

## Herramientas incluidas (53)

| Categoría | Herramientas |
|---|---|
| Contenido | Listar, crear, editar y eliminar posts, páginas y CPTs |
| Plugins | Listar, activar, desactivar, instalar, eliminar y editar archivos |
| Temas | Listar, activar, instalar y editar archivos de tema |
| Sistema de archivos | Leer, escribir, buscar, mover y eliminar archivos |
| Base de datos | SELECT, INSERT, UPDATE, DELETE con backup automático |
| Opciones | Leer y escribir wp_options |
| Usuarios | Listar, crear y actualizar usuarios |
| Administración | Info del sitio, caché, cron, medios, permalinks |
| Backups & Rollback | Snapshots, backups automáticos, rollback, log de auditoría |
| Multi-sitio | Gestionar varios sitios desde un hub único |

---

## Seguridad

El plugin crea **backups automáticos** antes de cualquier operación de escritura. Si algo falla, la skill ejecuta rollback al instante. Todas las operaciones quedan registradas en el log de auditoría.

---

## Plugin Claude MCP Connector

Este skill es inútil sin el plugin. El plugin es lo que expone las 53 herramientas a través de un endpoint JSON-RPC 2.0 seguro en tu WordPress.

**Desarrollado por [Sistemas Digitales MG](https://sistemasmg.es)**

**[Comprar Claude MCP Connector →](https://sistemasmg.es)**

---

## Licencia

El skill (este repositorio) es de uso libre — MIT.
El plugin Claude MCP Connector es software de pago con licencia comercial.
