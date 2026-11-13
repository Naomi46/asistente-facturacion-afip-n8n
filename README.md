# 🧾 Asistente Automatizado de Facturación Electrónica (ARCA/AFIP) con IA

Sistema de automatización desarrollado en **n8n** que permite emitir facturas electrónicas oficiales (A, B y C) desde **Telegram** mediante mensajes de **texto, notas de voz o fotografías**.

## 🎯 ¿Qué problema automatiza?
Elimina la carga manual en portales fiscales. El profesional envía un mensaje por Telegram con los datos de la venta y el flujo se encarga de interpretar la solicitud con IA, validar clientes, calcular impuestos, obtener el CAE oficial ante ARCA/AFIP y devolver la factura en PDF con código QR.

## 🏗️ Arquitectura del Sistema

```text
[Usuario en Telegram] ──(Texto / Audio / Foto)──▶ [02. Facturación.json]
        │                                                 │
  (Si es nuevo)                                   (Cola en Redis + Agente IA)
        ▼                                                 │
[01. Registrar Profesional.json]                          ├──▶ [Google Sheets (CRM y Registro)]
  (Formulario Web + Webhook)                              └──▶ [03. AFIP_Factura.json]
                                                                      │
                                                       (WSFE ARCA/AFIP + HTML/QR + PDF.co)
                                                                      ▼
                                                           [Factura PDF enviada al Chat]
```

## 📂 Descripción de los Workflows
- **`Registrar Profesional.json`:** Servidor web mediante Webhook que entrega un formulario HTML de alta fiscal, guarda los datos en Google Sheets y notifica por Telegram.
- **`Facturación.json`:** Orquestador principal. Usa **Redis** para agrupar mensajes seguidos, transcribe audios con *Whisper*, analiza imágenes con *GPT-4o-mini* y coordina herramientas de Google Sheets mediante un Agente IA.
- **`AFIP_Factura.json`:** Sub-workflow encargado de verificar los servidores de ARCA/AFIP, autenticar certificados, calcular neto e IVA (21%), solicitar el CAE, maquetar el comprobante con QR y convertirlo a PDF con *PDF.co*.

## 🛠️ Tecnologías Utilizadas
- **Orquestación:** n8n
- **Inteligencia Artificial:** OpenAI API (GPT-4o-mini & Whisper)
- **Mensajería & Web:** Telegram Bot API, HTML5, CSS3, JavaScript
- **Datos & Caché:** Google Sheets API, Redis
- **Facturación & Documentos:** Web Services ARCA/AFIP, PDF.co API

## 🚀 Cómo importarlo y usarlo en n8n
1. Descargar los tres archivos `.json` de este repositorio.
2. En **n8n**, ir a *Workflows > Import from File* e importar cada flujo.
3. Configurar las credenciales de **Telegram**, **OpenAI**, **Google Sheets**, **Redis** y **PDF.co**.
4. Activar los flujos `Registrar Profesional` y `Facturación`.
