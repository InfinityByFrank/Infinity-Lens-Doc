# 🔍 Infinity Lens — App de gastos para iPhone que lee tickets

🌐 [English](README.md) · **Español**

Una app de iPhone que convierte la foto de un ticket en un gasto ordenado (**total, fecha, comercio, categoría**) y además lleva recibos por pagar, suscripciones, presupuestos y garantías. Todo ocurre en el dispositivo: **sin servidor, sin cuenta, sin anuncios, sin internet.**

🔗 Landing: [lens.infinitydev.agency](https://lens.infinitydev.agency)

<p align="center">
  <img src="assets/tienda-02-escanear.png" width="260" alt="Confirmar un ticket leído" />
  <img src="assets/tienda-01-lista.png" width="260" alt="Lista de gastos con filtros" />
</p>

---

## 🎯 Sobre el proyecto

Quería una app de gastos donde bastara fotografiar el ticket, con tres condiciones que se llevan mal entre sí:

1. **Los datos no salen del teléfono** — sin servidor, sin cuenta, sin red.
2. **No se equivoca en silencio** — un total mal leído es peor que uno vacío.
3. **Tenía que ser un producto, no un ejercicio** — idiomas, accesibilidad, modelo de negocio y publicación en App Store.

Venía de React y **no conocía Swift**, así que el proyecto también fue aprender una plataforma nueva.

> **Rol:** diseño, desarrollo, traducción y preparación para publicar, de principio a fin.

## ✨ Funciones

| | |
|---|---|
| 📷 **Escanear** | Cámara (varias hojas), foto, PDF o a mano; siempre confirmas antes de guardar |
| 🧾 **Recibos y recordatorios** | Lee la fecha límite, recordatorios locales, crea solo el siguiente periodo |
| 📅 **Calendario de pagos** | Vencido / por pagar / pagado de un vistazo |
| 🔁 **Suscripciones** | Gasto mensual y anual; las canceladas quedan en el historial |
| 💸 **Presupuestos** | Límite por categoría, aviso al 80 % |
| 🧰 **Trabajo y archivo** | Gastos por reembolsar, facturas (PDF/XML), garantías, 15 monedas, exportar CSV y PDF |
| 🔐 **Privacidad** | Respaldo cifrado, bloqueo opcional con Face ID |
| 🌍 **6 idiomas** | ES, EN, PT-BR, FR, DE, IT · claro/oscuro · letra grande · VoiceOver |

## 🖼️ Galería

<p align="center">
  <img src="assets/tienda-03-calendario.png" width="190" alt="Calendario de pagos" />
  <img src="assets/tienda-04-suscripciones.png" width="190" alt="Suscripciones" />
  <img src="assets/tienda-05-resumen.png" width="190" alt="Resumen con gráficas" />
  <img src="assets/tienda-06-presupuestos.png" width="190" alt="Presupuestos" />
</p>
<p align="center">
  <img src="assets/modo-oscuro.png" width="190" alt="Modo oscuro" />
  <img src="assets/confirmar-gasto.png" width="190" alt="Confirmar gasto" />
  <img src="assets/widget-proximos-pagos.png" width="190" alt="Widget de próximos pagos" />
  <img src="assets/pro-compra.png" width="190" alt="Pantalla de compra Pro" />
</p>

## 🛠️ Tecnologías

| Tecnología | Para qué |
|---|---|
| Swift 6 · SwiftUI | Interfaz nativa iOS (iOS 18+) |
| SwiftData | Persistencia local (campos nuevos siempre opcionales para migrar datos existentes) |
| Swift Charts | Dona por categoría y barras mensuales |
| Vision · VisionKit · PDFKit | OCR en el dispositivo, escáner de documentos, lectura de PDF |
| WidgetKit | Widgets de inicio y pantalla bloqueada |
| StoreKit 2 | Compra única Pro, sin servidor |
| LocalAuthentication | Bloqueo con Face ID |
| CryptoKit | Respaldos cifrados: PBKDF2-HMAC-SHA256 (600 mil vueltas) + AES-256-GCM |
| `TicketKit` (paquete Swift propio) | Toda la lógica fuera de la pantalla, 318 pruebas automáticas |
| Astro · React · Tailwind | Landing trilingüe en Cloudflare |

## 🧠 Decisiones técnicas clave

### Leer un ticket sin inteligencia artificial
El plan original usaba un LLM de 2B en el teléfono. Medí primero: Vision + reglas ya alcanzaba el criterio, así que el LLM quedó **diferido** (menos memoria, menos riesgo, sin descarga de 1.5 GB).

- **Tres formas independientes de leer el total** que se contrastan: la etiqueta "TOTAL", *efectivo − cambio* y el **importe con letra** ("OCHENTA Y UN PESOS 65/100"). Si dos coinciden, la app lo dice; si chocan, deja el campo vacío.
- **Regla de diseño: preferir vacío a equivocado.** Quité heurísticas que "adivinaban" aunque subieran el porcentaje.
- **Fechas ambiguas** (`10/05/2026`): usa el orden de la región del teléfono y avisa si estimó.
- **Herramienta propia `ocr-check`** (terminal): corre el OCR sobre una carpeta y lo compara con la verdad: total 78 %, fecha 93 %, comercio 100 %, categoría 84 %, **0 equivocados**.
- **Honestidad:** medido sobre ~20 fotos propias, por lo que las cifras están sesgadas. Lo dejé escrito en lugar de sobreajustar.

<p align="center">
  <img src="assets/precision-ocr.png" width="240" alt="Pantalla de precisión del OCR" />
  <img src="assets/ocr-pdf-escaneado-cfe.png" width="240" alt="Recibo de luz escaneado leído con OCR" />
</p>

### Lógica separada de la interfaz
Todo lo que no es pantalla vive en el paquete `TicketKit` (~6 800 líneas con pruebas): reglas de OCR, formatos de dinero, filtros, calendario, recordatorios, cifrado. **318 pruebas** corren en segundos sin simulador; la app (~9 700 líneas de SwiftUI) solo pinta. La migración se probó copiando a mano una base del esquema anterior.

### Privacidad como decisión de diseño
- La app **no hace conexiones propias**; la etiqueta de App Store "datos no recopilados" es cierta.
- **Respaldo cifrado** (contraseña de mínimo 8 caracteres, sal aleatoria, AES-256-GCM que además detecta alteraciones). Restaurar solo *agrega*, nunca reemplaza, y cargar dos veces el mismo respaldo no duplica.
- Regla del proyecto: si una función cambia lo que la app hace con los datos, se actualizan a la vez la pantalla de privacidad, la política pública, el manifiesto de Apple y las etiquetas de App Store Connect.

<p align="center">
  <img src="assets/respaldo-cifrado.png" width="240" alt="Respaldo cifrado" />
  <img src="assets/privacidad.png" width="240" alt="Pantalla de privacidad" />
</p>

### Producto: gratis vs. Pro
Cambié "gratis hasta 30 gastos" por una **compra única Pro**, con todo lo básico gratis y sin límite. La frontera es por **función** y **lo ya capturado nunca se bloquea**. También **rechacé el tipo de cambio automático por internet**: rompía la promesa "sin internet", el principal diferenciador.

### Accesibilidad que se mide
Escribí una herramienta que calcula el contraste WCAG del texto pequeño con los colores reales de iOS y subí todo a **4.5:1**. Las pantallas se revisaron hasta el tamaño de letra de accesibilidad más grande.

<p align="center">
  <img src="assets/contraste-antes.png" width="210" alt="Contraste antes" />
  <img src="assets/contraste-despues.png" width="210" alt="Contraste después" />
  <img src="assets/accesibilidad-letra-grande.png" width="210" alt="Letra grande" />
</p>

### Seis idiomas, no solo traducción
~500 textos con catálogos de Xcode; el OCR también entiende tickets en inglés y portugués (efectivo/cambio, *dinheiro/troco*, formatos locales de fecha y moneda). La ficha de App Store se genera con un **script para 11 variantes regionales** que valida los límites de Apple.

<p align="center">
  <img src="assets/idioma-ingles.png" width="210" alt="Interfaz en inglés" />
  <img src="assets/idioma-portugues.png" width="210" alt="Interfaz en portugués" />
  <img src="assets/icono-pantalla-inicio.png" width="210" alt="Ícono en la pantalla de inicio" />
</p>

## 🌱 Qué aprendí / qué mejoraría

- Medir primero: el LLM que parecía necesario no hizo falta.

## 📬 Contacto

**Infinity dev by Frank** · [GitHub](https://github.com/TheGeistGamer) · [Infinity](https://infinitydev.agency)
