# SODAL Web 2.0 — Contexto para Claude Code

## Qué es
Migración de sodal.cl desde WordPress legacy hacia una web moderna con ecommerce B2B. SODAL: empresa chilena de extrusión y sistemas de aluminio arquitectónico (no es comercializadora — tiene capacidad industrial real). JM es sponsor estratégico, no socio.

## Objetivo final
Portal de clientes B2B con precios por nivel (P1-P8), carrito y pedidos reales integrados a Bsale (ERP). **Este es un proyecto de 2 fases — leer `docs/architecture-decisions.md` antes de cualquier decisión técnica.**

## Stack decidido
- **Frontend:** Next.js (SSR + SSG + portal dinámico)
- **CMS contenido:** Sanity (fichas técnicas, PDFs, imágenes, páginas)
- **Commerce engine (Fase 2):** Medusa.js
- **ERP (Fase 2):** Bsale — precios por cliente, inventario, órdenes, facturación SII
- **Hosting:** Vercel (Fase 1), revisar para Fase 2

## Estructura del repo
```
legacy/       # Código WordPress extraído (solo referencia, NO modificar)
  db/         # SQL dump (pendiente extracción por Mati)
docs/         # Configuración, catálogo JSON, sitemap, architecture-decisions.md
content/      # Contenido en Markdown (fuente de verdad para páginas y productos)
  pages/      # 5 páginas institucionales
  products/   # 19 fichas de sistemas por categoría
reference/    # Assets del sitio actual (scrape completo + CSV WooCommerce)
src/          # Código fuente new web (vacío — arrancar aquí)
scripts/      # Utilidades de migración
```

## Contexto de negocio crítico
- **201 productos** en catálogo: 22 sistemas (ventanas/puertas/fachadas) + 55 perfiles estándar + ~80 quincallería + 40 accesorios inox + 12 maquinaria
- **~4,500 clientes activos/año**. Retención interanual: 47% (benchmark B2B: >70% — problema a resolver con el portal)
- **-9% revenue en 2025** por crisis construcción Chile. 85% RM.
- Segmentos: Fabricantes ventanas (~35%), Constructoras, Retail Técnico, Vidrieros, Industrial
- **WooCommerce actual sin carrito funcional** — es catálogo técnico puro. El ecommerce no existe hoy.
- Precios no están en el sitio actual. 8 niveles de descuento (P1-P8, 2%-16%) por cliente.

## Fases
**Fase 1 (ahora):** Catálogo moderno con Next.js + Sanity. Mock prices. No requiere ERP ni Medusa.
**Fase 2 (requiere ERP + reestructuración comercial):** Portal con login, precios reales, carrito, integración Bsale.

## Reglas
- No arrancar Fase 2 hasta que Bsale esté activo y la estructura comercial P1-P8 esté definida.
- Los archivos en `legacy/` son de solo lectura.
- `legacy/db/database.sql` contiene datos sensibles: NO commitear a repos públicos.
- El `.wpress` original (6.9GB) está en `/Users/jmc/Documents/Cloude Co Work/SODAL - Claude Co Work/`. No extraer uploads (~5-6GB de media).
- Extracción completa del .wpress es tarea de Mati (ver `docs/architecture-decisions.md`).
- Toda decisión de arquitectura nueva → documentar en `docs/architecture-decisions.md` antes de implementar.

## Datos clave en el repo
- `docs/product-catalog.json` — 77 productos catalogados con URLs
- `docs/sitemap-urls.json` — 201 URLs, jerarquía de categorías
- `reference/woocommerce-products.csv` — 171 productos con códigos SKU y categorías (extraído del .wpress)
- `content/products/` — 19 fichas detalladas en Markdown

## Equipo de ventas SODAL
- Federico Von Hausen — fh@sodal.cl — (+56) 9 9402 2461
- Miguel Vasquez — mvasquez@sodal.cl — (+56) 9 9543 7456
- Joel Nicolaz — jnicolaz@sodal.cl — (+56) 9 5814 6088
- Roberto Godoy (Productos Procesados) — rgodoy@sodal.cl — (+56) 9 9323 7312

## Contexto estratégico completo
`/Users/jmc/Documents/Cloude Co Work/Archivos de Contexto/SODAL.md`
`/Users/jmc/Documents/Cloude Co Work/SODAL - Claude Co Work/Auditoria_Web_SODAL_2026.docx`
