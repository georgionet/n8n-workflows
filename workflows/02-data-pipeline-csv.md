# Data Pipeline - CSV to Report

Recibe un CSV via Webhook, transforma y limpia los datos, filtra registros validos, genera un reporte limpio y lo devuelve como descarga. Notifica a Slack con el resumen.

## Flujo

```
[Webhook POST] → [Parse CSV] → [Transform (Code)] → [Filter Valid]
                                                         │
                   [Notify Slack] ← [Generate CSV] ← [Clean Fields] → [Return File]
```

## Nodos utilizados

| Nodo | Tipo | Funcion |
|------|------|---------|
| Webhook Input | Webhook | Recibe POST con archivo CSV binario |
| Parse CSV | Spreadsheet File | Parsea CSV a objetos JSON |
| Transform Data | Code | Normaliza campos, valida emails, calcula totales |
| Filter Valid | IF | Filtra solo registros con email valido y cantidad > 0 |
| Clean Fields | Set | Selecciona y renombra campos finales |
| Generate Report | Spreadsheet File | Convierte JSON de vuelta a CSV |
| Return File | Respond to Webhook | Devuelve archivo como descarga |
| Notify Slack | HTTP Request | POST a Slack webhook con resumen |

## Features demostradas

- **Webhook con respuesta**: Recibe archivo y devuelve resultado sincronamente
- **Code node**: Transformacion compleja de datos con JavaScript
  - Normalizacion de campos (acepta nombre/name, cantidad/qty, etc.)
  - Validacion de formato email con regex
  - Calculo de totales
  - Limpieza de strings (trim, upper/lower)
- **File handling**: Parseo y generacion de CSV/Spreadsheet
- **Multi-output**: Un nodo alimenta dos destinos (retorno + notificacion)
- **Data filtering**: IF con condiciones compuestas (AND)

## Formato de entrada esperado

CSV con columnas (acepta variaciones):

```
nombre,email,producto,cantidad,precio,fecha
Juan Perez,juan@email.com,Laptop,2,500000,2026-04-15
Maria Lopez,maria@correo.com,Mouse,5,25000,2026-04-15
```

También acepta: `name`, `correo`, `item`, `qty`, `price` como nombres alternativos.

## Formato de salida

CSV limpio con columnas: `nombre, email, producto, cantidad, precio_unitario, total, fecha`

## Como probar

```bash
curl -X POST https://tu-n8n.com/webhook/csv-upload \
  -F "data=@datos.csv"
```

Devuelve el CSV procesado como descarga.

## Variables de entorno necesarias

| Variable | Descripcion |
|----------|-------------|
| `SLACK_WEBHOOK_URL` | URL del webhook de Slack para notificaciones |
