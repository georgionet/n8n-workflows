# API Health Monitor

Monitorea endpoints HTTP cada 30 minutos y envia alertas por Telegram cuando un servicio cae.

![Workflow en n8n](01-api-health-monitor-flow.png)

## Flujo

```
[Cron 30min] → [HTTP Request] → [Enrich Data] → [IF: status == 200?]
                                                      ├── YES → [Log OK]
                                                      └── NO  → [Telegram Alert]
```

## Nodos utilizados

| Nodo | Tipo | Funcion |
|------|------|---------|
| Every 30 min | Schedule Trigger | Ejecuta cada 30 minutos |
| Check API | HTTP Request | GET al endpoint, con retry (3 intentos, 5s entre cada uno) y full response |
| Enrich Data | Set | Captura URL y status code del response |
| Is Healthy? | IF | Evalua si el status code es 200 |
| Send Alert | Telegram | Notifica via Telegram si el servicio esta down |
| Log OK | Set | Formatea datos del check exitoso |

## Features demostradas

- **Scheduling**: Ejecucion periodica con Schedule Trigger
- **HTTP Requests**: Con timeout, retry automatico (3 intentos) y full response para capturar status code
- **Data enrichment**: Set node para extraer y normalizar datos del HTTP response
- **Condicionales**: IF node para evaluar respuestas
- **Telegram integration**: Envio de mensajes con formato Markdown
- **Error handling**: Retry on fail en el nodo HTTP

## Credenciales necesarias

- **Telegram Bot API**: Token del bot de Telegram

## Como importar

1. Ir a n8n → Menu → Import from File
2. Seleccionar `01-api-health-monitor.json`
3. Configurar credenciales de Telegram
4. Activar el workflow
