# Architecture Decisions — SODAL Web 2.0

Registro de decisiones de arquitectura. Toda decisión con impacto estructural se documenta aquí antes de implementar.

---

## ADR-001: Tipo de ecommerce
**Estado:** Decidido — 17/03/2026
**Decisión:** Modelo B — Portal de clientes
**Detalle:** Login requerido para comprar. Precios por nivel de cliente (P1-P8, descuentos 2%-16%). Carrito real. Historial de pedidos. Base de datos de clientes SODAL como punto de partida.
**Por qué:** Cierra el gap de retención (47% actual vs >70% benchmark B2B industrial). Único modelo que convierte el sitio en canal de venta real, no catálogo con formulario.
**Implicación:** Requiere reestructuración comercial previa (estructura de precios, niveles de cliente, políticas de despacho). El portal no sale a producción hasta que eso esté resuelto.

---

## ADR-002: Precios públicos
**Estado:** Decidido — 17/03/2026
**Decisión:** Mock prices en Fase 1 (catálogo público). Precios reales solo con login en Fase 2.
**Por qué:** La estructura de precios P1-P8 no puede exponerse públicamente sin revelar la arquitectura comercial. Requiere rediseño completo antes de publicar.
**Fase 1:** Catálogo público sin precios reales → "Consultar precio" o precio de lista referencial.
**Fase 2:** Precios dinámicos por nivel de cliente (requiere ERP activo + nueva estructura comercial).

---

## ADR-003: ERP
**Estado:** Decidido (dirección) — 17/03/2026
**Decisión:** ERP propio independiente de Softland. UEN de ecommerce como entidad separada.
**ERP seleccionado:** Bsale (chileno, API REST, pensado para distribución/retail, precio accesible)
**Por qué:** No depender de Softland para la operación digital. Bsale tiene integración nativa con ecommerce, facturación electrónica SII, y API para conectar con la tienda.
**Pendiente:** Evaluar plan Bsale, onboarding, migración de catálogo y clientes.
**Implicación:** Bsale maneja inventario, precios por cliente, órdenes y facturación. La tienda web consulta Bsale vía API para stock y precios en tiempo real (Fase 2).

---

## ADR-004: Stack frontend
**Estado:** Decidido — 17/03/2026
**Decisión:** Next.js
**Por qué:** SSR para SEO del catálogo público (Fase 1) + SSG para páginas estáticas + capacidades de app dinámica para el portal (Fase 2). Mismo stack que Fenvo — consistencia en el ecosistema JM.
**Hosting:** Vercel (Fase 1) → evaluar si Railway o VPS es mejor para Fase 2 con portal activo.

---

## ADR-005: CMS de contenido
**Estado:** Decidido — 17/03/2026
**Decisión:** Sanity
**Por qué:** Sanity maneja contenido editorial (fichas técnicas, PDFs, imágenes, páginas institucionales). El equipo SODAL puede actualizar contenido sin tocar código. Schema estructurado ideal para catálogo técnico con múltiples atributos (terminaciones, normas, dimensiones, descargas PDF).
**Separación de responsabilidades:**
- Sanity → contenido (descripciones, specs, PDFs, imágenes)
- Bsale → comercial (precios, stock, órdenes, clientes)
- Next.js → presentación (frontend + portal)
**Free tier:** Suficiente para 200+ productos con media.

---

## ADR-006: Commerce engine
**Estado:** Decidido (dirección) — 17/03/2026
**Decisión:** Medusa.js (para Fase 2)
**Por qué:** Open source, self-hostable. Price lists por grupo de clientes nativamente (exactamente P1-P8). Carrito, órdenes, historial, portal de clientes. Se integra a Bsale vía API.
**Fase 1:** No se usa Medusa. El catálogo es solo Next.js + Sanity.
**Fase 2:** Medusa maneja la lógica de comercio. Bsale es la fuente de verdad de inventario/precios.

---

## Fases del proyecto

### Fase 1 — Catálogo moderno (puede construirse ahora)
- Next.js + Sanity
- Catálogo completo de productos con fichas técnicas
- Páginas institucionales migradas
- Mock prices o "consultar precio"
- SEO preservado (redirect map desde URLs WordPress)
- Deploy en Vercel
- **No requiere:** ERP, reestructuración comercial, Medusa

### Fase 2 — Portal de clientes (requiere ERP + trabajo comercial)
- Bsale activo con catálogo, precios y clientes migrados
- Medusa.js como commerce engine
- Login de clientes con precios por nivel
- Carrito + checkout + integración Bsale para órdenes
- Facturación electrónica automática (Bsale → SII)
- **Prerrequisitos:** Nueva estructura comercial P1-P8 definida, Bsale onboarding completo, migración de base de clientes

---

## Decisiones pendientes

| Decisión | Prioridad | Owner | Para cuándo |
|---|---|---|---|
| Dominio y hosting Vercel | Alta | JM | Antes de Fase 1 |
| Plan Bsale y onboarding | Alta | JM | Antes de Fase 2 |
| Nueva estructura precios P1-P8 | Alta | JM | Antes de Fase 2 |
| Schema Sanity: atributos de productos | Media | JM + Claude Code | Al arrancar Fase 1 |
| Redirect map URLs WordPress | Media | Claude Code | Al arrancar Fase 1 |
| Migración clientes a Bsale | Media | JM + Mati | Pre-Fase 2 |
| Extracción DB WordPress (SQL dump) | Baja | Mati | Cuando sea útil |

---

## Extracción del backup WordPress (.wpress)

El archivo `sodal-cl-20260317-120204-rtz369yp2ykr.wpress` (6.9 GB) usa el formato propietario de All-in-One WP Migration. Para extraer el SQL dump y los archivos del tema:

**Opción A (recomendada):** Docker con WordPress local + plugin AIOWPM
```bash
docker run -d -p 8080:80 --name sodal-wp wordpress
# Instalar All-in-One WP Migration plugin
# Restaurar .wpress vía el plugin
# Exportar lo que se necesite
```

**Opción B:** Script Python custom (parcialmente desarrollado en sesión 17/03/2026 — el formato usa headers de 4096 bytes con estructura name[255]+size[14]+mtime[12])

**Lo que ya se extrajo sin el plugin:**
- `reference/woocommerce-products.csv` — 171 productos con códigos, categorías (extraído directamente del binario)
- Todo el contenido en `content/` (scrapeado de sodal.cl el 17/03/2026)

**Owner extracción completa:** Mati
