# API Health Monitor

Monitorea endpoints HTTP cada 30 minutos y envia alertas por Telegram cuando un servicio cae.

## Flujo

```
[Cron 30min] → [HTTP Request] → [IF: status == 200?]
                                     ├── YES → [Log OK] → [Send to Logger]
                                     └── NO  → [Telegram Alert]
```

## Nodos utilizados

| Nodo | Tipo | Funcion |
|------|------|---------|
| Every 30 min | Schedule Trigger | Ejecuta cada 30 minutos |
| Check API | HTTP Request | GET al endpoint, con retry (3 intentos, 5s entre cada uno) |
| Is Healthy? | IF | Evalua si el status code es 200 |
| Send Alert | Telegram | Notifica via Telegram si el servicio esta down |
| Log OK | Set | Formatea datos del check exitoso |
| Send to Logger | HTTP Request | POST a webhook externo para centralizar logs |

## Features demostradas

- **Scheduling**: Ejecucion periodica con Schedule Trigger
- **HTTP Requests**: Con timeout, retry automatico (3 intentos) y espera entre reintentos
- **Condicionales**: IF node para evaluar respuestas
- **Telegram integration**: Envio de mensajes con formato Markdown
- **Data transformation**: Set node para formatear datos de log
- **Error handling**: Retry on fail en el nodo HTTP

## Variables de entorno necesarias

| Variable | Descripcion |
|----------|-------------|
| `TELEGRAM_CHAT_ID` | Chat ID de Telegram para alertas |
| `LOG_WEBHOOK_URL` | URL del webhook para centralizar logs |

## Credenciales necesarias

- **Telegram Bot API**: Token del bot de Telegram

## Como importar

1. Ir a n8n → Workflows → Import from File
2. Seleccionar `01-api-health-monitor.json`
3. Configurar credenciales de Telegram
4. Setear variables de entorno
5. Activar el workflow
