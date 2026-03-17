# Extracción del backup WordPress (.wpress)

**Owner: Mati**

El archivo `sodal-cl-20260317-120204-rtz369yp2ykr.wpress` (6.9 GB) está en:
`/Users/jmc/Documents/Cloude Co Work/SODAL - Claude Co Work/`

## Qué necesitamos extraer

| Archivo | Destino en repo | Para qué |
|---|---|---|
| `database.sql` | `legacy/db/database.sql` | Entender estructura WooCommerce, migrar clientes |
| `wp-content/themes/` | `legacy/themes/` | Referencia de diseño actual |
| `wp-content/plugins/` | `legacy/plugins/` | Inventario de funcionalidades actuales |
| `wp-content/uploads/` | **NO extraer** | ~5-6 GB de media — se migra aparte |

## Método recomendado: Docker

```bash
# 1. Levantar WordPress local
docker run -d \
  -p 8080:80 \
  -e WORDPRESS_DB_HOST=db \
  -e WORDPRESS_DB_NAME=sodal \
  -e WORDPRESS_DB_USER=root \
  -e WORDPRESS_DB_PASSWORD=root \
  --name sodal-wp wordpress

# 2. Instalar All-in-One WP Migration plugin
# (desde wp-admin o WP-CLI)

# 3. Restaurar el .wpress
# Subir via wp-admin > All-in-One WP Migration > Restore

# 4. Exportar solo lo que necesitamos
# Acceder al container y copiar:
docker cp sodal-wp:/var/www/html/wp-content/themes/ ./legacy/themes/
docker cp sodal-wp:/var/www/html/wp-content/plugins/ ./legacy/plugins/
# El SQL lo puedes exportar con mysqldump desde dentro del container
```

## Alternativa: WP-CLI sin Docker

```bash
# Si tienes PHP y MySQL local:
wp core download
wp config create --dbname=sodal --dbuser=root --dbpass=root
wp db create
wp package install wp-cli/export-command
# Luego importar el .wpress con el plugin AIOWPM
```

## Lo ya extraído sin plugin

En `reference/woocommerce-products.csv`: 171 productos con códigos y categorías, extraídos directamente del binario (formato WebToffee export embebido en el .wpress).

## Nota sobre el formato .wpress

Es un formato TAR propietario con headers de 4096 bytes (name[0-254] + size[255-268] + mtime[269-280] + padding[281-4095]). Contenido sigue en bloques de 4096 bytes. Parsing manual es posible pero complejo para 6.9 GB.
