# SODAL Web 2.0 — Contexto para Claude Code

## Language

All user-facing copy is **Chilean Spanish** using the **tú** register (tienes, puedes, necesitas). Never use Argentinian *vos* forms.

## Qué es
Migración de sodal.cl desde WordPress legacy hacia web moderna con ecommerce B2B. SODAL: empresa chilena de extrusión y sistemas de aluminio arquitectónico (capacidad industrial real, no comercializadora). JM es sponsor estratégico, no socio.

## Objetivo final
Portal de clientes B2B con precios por nivel (P1-P8), carrito y pedidos reales integrados a Bsale (ERP). **Proyecto de 2 fases — leer `docs/architecture-decisions.md` antes de cualquier decisión técnica.**

## Stack decidido
- **Fase 1:** Astro + Markdown content collections (catálogo estático). Sin CMS hasta que haya owner de contenido.
- **Fase 2:** Next.js (portal B2B, subdominio separado) + Medusa.js (commerce engine) + Bsale (ERP)
- **Hosting Fase 1:** Vercel o Cloudflare Pages (gratuito para estático)

## Estructura del repo
```
legacy/       # Código WordPress extraído (solo referencia, NO modificar)
docs/         # Configuración, catálogo JSON, sitemap, architecture-decisions.md
content/      # Contenido en Markdown (fuente de verdad para páginas y productos)
  pages/      # 5 páginas institucionales
  products/   # 19 fichas de sistemas por categoría
reference/    # Assets del sitio actual (scrape completo + CSV WooCommerce)
src/          # Código fuente new web (arrancar aquí)
scripts/      # Utilidades de migración
```

## Contexto de negocio
- 201 productos en catálogo. ~4,500 clientes activos/año. Retención interanual: 47% (benchmark B2B: >70%).
- -9% revenue en 2025 por crisis construcción Chile. 85% RM.
- WooCommerce actual es catálogo técnico puro — el ecommerce no existe hoy.
- 8 niveles de descuento (P1-P8, 2%-16%) por cliente.

## Fases
**Fase 1 (ahora):** Astro + Markdown. Mock prices o "consultar precio". Cero CMS, cero runtime, hosting gratuito. No requiere ERP ni reestructuración comercial.
**Fase 2 (requiere ERP + trabajo comercial):** Next.js + Medusa.js + Bsale. Login, precios reales por nivel, carrito, facturación SII.

## Reglas
- No arrancar Fase 2 hasta que Bsale esté activo y estructura comercial P1-P8 definida
- Archivos en `legacy/` son de solo lectura
- `legacy/db/database.sql` contiene datos sensibles: NO commitear a repos públicos
- Toda decisión de arquitectura nueva → documentar en `docs/architecture-decisions.md` antes de implementar

## Datos clave en el repo
- `docs/product-catalog.json` — 77 productos catalogados
- `docs/sitemap-urls.json` — 201 URLs
- `reference/woocommerce-products.csv` — 171 productos con SKU
- `content/products/` — 19 fichas detalladas en Markdown

## Equipo
- JM — sponsor estratégico, decisiones de arquitectura
- Mati — infra, extracción DB, deploy
- Equipo ventas SODAL — validación de contenido
