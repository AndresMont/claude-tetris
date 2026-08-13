---
name: clima
description: Consulta el clima actual de una ciudad. Si no se especifica ciudad, usa Quito, Ecuador por defecto. Trigger, "clima", "weather", "que clima hace en", "temperatura en".
---

# Clima

Consulta clima actual via WebFetch, sin API key.

## Ciudad

- Args trae ciudad -> usar esa.
- Sin ciudad en args -> default: `Quito, Ecuador`.

## Pasos

1. Codificar ciudad para URL (espacios -> `+`, ej "Quito,Ecuador" -> "Quito,EC" o "Quito+Ecuador").
2. WebFetch a: `https://wttr.in/<ciudad>?format=%l:+%C+%t+(sensacion+%f)+humedad+%h+viento+%w`
   - Ejemplo: `https://wttr.in/Quito,Ecuador?format=%l:+%C+%t+(sensacion+%f)+humedad+%h+viento+%w`
3. Si falla wttr.in, fallback: Open-Meteo (sin key):
   - Geocoding: `https://geocoding-api.open-meteo.com/v1/search?name=<ciudad>&count=1`
   - Forecast con lat/lon: `https://api.open-meteo.com/v1/forecast?latitude=<lat>&longitude=<lon>&current=temperature_2m,relative_humidity_2m,wind_speed_10m,weather_code`
4. Responder al usuario en espanol, formato corto: ciudad, condicion, temperatura, sensacion termica, humedad, viento.

## Notas

- No inventar datos si ambas fuentes fallan; informar error.
- Ciudad ambigua (nombre repetido en varios paises) -> pedir aclaracion o usar primer resultado de geocoding.
