# Documentación - Proyecto Consultas ANT Citaciones (Aseguradora del Sur)

Este documento describe la estructura, el código, la forma de ejecución y ejemplos de uso de cada script del proyecto de consultas masivas a la **Agencia Nacional de Tránsito (ANT)** para obtener citaciones de conductores.

---

## Índice

1. [consulta_multiple.py](#1-consulta_multiplepy)
2. [consulta_ANT.py](#2-consulta_antpy)
3. [consulta_ant_con_proxies.py](#3-consulta_ant_con_proxiespy)
4. [cedulas_ejemplo.csv](#4-cedulas_ejemplocsv)
5. [batch_processor.py](#5-batch_processorpy)
6. [batch_processor_citaciones.py](#6-batch_processor_citacionespy)
7. [Tabla comparativa de diferencias](#7-tabla-comparativa-de-diferencias)

---

## 1. consulta_multiple.py

### Descripción

Script que realiza consultas múltiples al spider **ANT Citaciones** de Scrapy. Lee cédulas desde un CSV, lanza el spider `ant_citaciones` por cada una mediante `subprocess`, y escribe resultados exitosos y errores en dos CSV.

### Estructura del código

| Componente | Función / Clase | Descripción |
|------------|-----------------|-------------|
| Entrada | `leer_cedulas_desde_csv(archivo_csv)` | Lee el CSV y detecta la columna de cédula (`cedula`, `cédula`, `ced`, `identificacion`, `id`). Solo acepta valores numéricos. |
| Consulta | `consultar_cedula(cedula)` | Crea un archivo temporal `.json`, ejecuta `scrapy crawl ant_citaciones -a cedula=X -o X.json -L WARNING`, lee el JSON y devuelve un dict con `status` ("success" o "error"). Timeout 300 s. |
| Salida | `escribir_resultados_csv(resultados, archivo_csv)` | Escribe un CSV con una fila por citación (o una por persona si no hay citaciones). Columnas: cedula, nombre_completo, puntos, licencias, numero_infraccion, entidad, etc. |
| Salida | `escribir_errores_csv(errores, archivo_csv)` | CSV con columnas: cedula, codigo_error, mensaje_error, fecha_intento. |
| Orquestación | `procesar_consultas(cedulas, archivo_resultados, archivo_errores, max_workers)` | Usa `ThreadPoolExecutor(max_workers)`; cada hilo llama a `consultar_cedula` y actualiza listas con un `Lock`. Al final escribe ambos CSV. |
| CLI | `main()` | `argparse`: `--input` (requerido), `--output`, `--errores`, `--threads` (default 4). |

**Comando interno que se ejecuta por cédula:**

```bash
scrapy crawl ant_citaciones -a cedula=1234567890 -o /tmp/xxx.json -L WARNING
```

### Dependencias

- **Python 3**
- **Scrapy** instalado y proyecto con spider `ant_citaciones` (ejecutar desde el directorio del proyecto Scrapy o tener `scrapy` en el PATH).
- Solo librería estándar además: `subprocess`, `csv`, `json`, `argparse`, `concurrent.futures`, `threading`, `tempfile`, `datetime`.

### Cómo ejecutar

Desde la carpeta donde está el script (o donde está el proyecto Scrapy):

```bash
python consulta_multiple.py --input <archivo.csv> --output <resultados.csv> --errores <errores.csv> [--threads N]
```

**Parámetros:**

| Parámetro | Forma corta | Requerido | Default | Descripción |
|-----------|-------------|-----------|---------|-------------|
| `--input` | `-i` | Sí | — | CSV de entrada con cédulas. |
| `--output` | `-o` | No | `resultados.csv` | CSV de resultados exitosos. |
| `--errores` | `-e` | No | `errores.csv` | CSV de consultas fallidas. |
| `--threads` | `-t` | No | `4` | Número máximo de hilos simultáneos. |

### Ejemplos de ejecución

```bash
# Uso mínimo (input obligatorio; output y errores por defecto)
python consulta_multiple.py -i cedulas_ejemplo.csv

# Especificar todos los archivos
python consulta_multiple.py --input cedulas_ejemplo.csv --output resultados.csv --errores errores.csv

# Con 8 hilos para más velocidad
python consulta_multiple.py -i cedulas_ejemplo.csv -o resultados.csv -e errores.csv --threads 8

# Mismo ejemplo con rutas absolutas (Windows)
python consulta_multiple.py -i C:\datos\cedulas.csv -o C:\datos\resultados.csv -e C:\datos\errores.csv -t 4
```

---

## 2. consulta_ANT.py

### Descripción

Consulta citaciones ANT **sin Scrapy**: usa HTTP directo con `urllib` contra los mismos endpoints que el spider (consulta persona → página grid → JSON de citaciones). Incluye procesamiento en lotes con pausas y métricas (incluyendo estimación para 1 millón de consultas).

### Estructura del código

| Componente | Función | Descripción |
|------------|---------|-------------|
| Configuración | Constantes `BASE_URL`, `CONSULTA_PERSONA`, `GRID_CITACIONES`, `JSON_CITACIONES` | URLs base de consultaweb.ant.gob.ec. |
| Parseo HTML | `_parse_licencias(html_text)`, `_parse_nombre(html_text)`, `_parse_puntos(html_text)`, `_parse_categorias_con_registros(html_text)` | Extraen licencias, nombre, puntos y categorías con registros del HTML. |
| HTTP | `_get(url, referer, timeout)`, `_post(url, timeout)` | GET/POST con `urllib.request.Request` y `urlopen`. Devuelven `(status_code, body)` o `(None, error)`. |
| Flujo por cédula | `_consultar_persona(cedula)` | POST a `clp_json_consulta_persona.jsp`; devuelve `persona_id` y nombre. |
| | `_obtener_grid_citaciones(cedula, tipo)` | GET a `clp_grid_citaciones.jsp`; parsea nombre, puntos, licencias, categorías. |
| | `_obtener_citaciones_json_pagina(...)` | GET a `clp_json_citaciones.jsp` con paginación; devuelve lista de citaciones. |
| | `consultar_cedula_ant(cedula)` | Orquesta los 3 pasos y devuelve dict con status, cedula, nombre_completo, puntos, licencias, citaciones, scraping_date. |
| CSV | `leer_cedulas_desde_csv`, `escribir_resultados_csv`, `escribir_errores_csv` | Misma idea que en consulta_multiple (columnas estándar). |
| Lotes y métricas | `calcular_metricas(inicio, consultas_realizadas)` | Calcula tiempo transcurrido, consultas/min, consultas/hora, estimación para 1M. |
| | `procesar_consultas(cedulas, archivo_resultados, archivo_errores, max_workers, pause_seconds)` | Procesa en chunks de `max_workers`; entre chunk y chunk: escribe CSV, muestra métricas, pausa `pause_seconds`. Cada 50 consultas hace pausa larga de 30 s. |

### Dependencias

- **Python 3** (solo librería estándar: `csv`, `json`, `re`, `argparse`, `time`, `datetime`, `concurrent.futures`, `threading`, `urllib`).

### Cómo ejecutar

```bash
python consulta_ANT.py --input <archivo.csv> [--output <resultados.csv>] [--errores <errores.csv>] [--threads N] [--pause N]
```

**Parámetros:**

| Parámetro | Forma corta | Requerido | Default | Descripción |
|-----------|-------------|-----------|---------|-------------|
| `--input` | `-i` | Sí | — | CSV de cédulas. |
| `--output` | `-o` | No | `resultados.csv` | CSV de resultados. |
| `--errores` | `-e` | No | `errores.csv` | CSV de errores. |
| `--threads` | `-t` | No | `20` | Consultas simultáneas por lote. |
| `--pause` | `-p` | No | `10` | Segundos de pausa entre lotes. |

### Ejemplos de ejecución

```bash
# Básico con archivo de ejemplo
python consulta_ANT.py -i cedulas_ejemplo.csv

# Salida y errores en archivos concretos
python consulta_ANT.py -i cedulas_ejemplo.csv -o resultados_ant.csv -e fallos_ant.csv

# 30 consultas por lote y 15 segundos de pausa entre lotes
python consulta_ANT.py -i cedulas_ejemplo.csv -o resultados.csv -e errores.csv --threads 30 --pause 15

# Menos paralelismo para reducir riesgo de bloqueo
python consulta_ANT.py -i cedulas_ejemplo.csv -t 5 -p 20
```

---

## 3. consulta_ant_con_proxies.py

### Descripción

Consultas masivas a ANT con **rotación de proxies**. Usa la librería `requests` para HTTP. Soporta: archivo de proxies, variables de entorno ESTELA, o configuración por defecto (ej. Rayobyte). Incluye delay aleatorio entre peticiones y User-Agent rotatorio.

### Estructura del código

| Componente | Clase / Función | Descripción |
|------------|-----------------|-------------|
| Endpoints | `ANTEndpoints` | Constantes: `CONSULTA_PERSONA`, `GRID_CITACIONES`, `JSON_CITACIONES`. |
| Proxies | `GestorProxies` | Rota proxy cada N consultas. Métodos: `obtener_proxy()`, `cargar_desde_archivo()`, `cargar_desde_env_estela()`, `cargar_desde_configuracion()`, `cargar_rayobyte_default()`, `verificar_conexion()`. |
| Parseo | `parse_licencias`, `parse_puntos`, `parse_nombre`, `parse_categorias_con_registros`, `get_estado_descripcion` | Mismo estilo que consulta_ANT (regex sobre HTML/JSON). |
| Cliente | `ClienteANT` | Recibe `GestorProxies` y logger. `consultar(cedula)`: consulta persona → HTML citaciones → JSON citaciones; usa delay aleatorio y proxy en cada request. Devuelve `ResultadoConsulta`. |
| Datos | `ResultadoConsulta` (dataclass) | cedula, exito, nombre_completo, puntos, licencias, citaciones, mensaje_error, codigo_error, fecha_consulta. |
| CSV | `leer_cedulas_csv`, `escribir_resultados_csv`, `escribir_errores_csv` | Lectura con detección de columna; escritura con columnas estándar (en errores: codigo_error, mensaje_error). |
| Orquestación | `procesar_consultas(cedulas, gestor_proxies, logger, num_hilos, ...)` | Con `num_hilos=1` es secuencial; si no, usa `ThreadPoolExecutor`. Cada hilo usa el mismo `ClienteANT` (el gestor de proxies es thread-safe). |

### Dependencias

- **Python 3.9+**
- **requests**: `pip install requests`

### Cómo ejecutar

```bash
python consulta_ant_con_proxies.py --input <archivo.csv> --output <resultados.csv> [opciones]
```

**Parámetros principales:**

| Parámetro | Forma corta | Requerido | Default | Descripción |
|-----------|-------------|-----------|---------|-------------|
| `--input` | `-i` | Sí | — | CSV de cédulas. |
| `--output` | `-o` | Sí | — | CSV de resultados. |
| `--errores` | `-e` | No | `errores.csv` | CSV de errores. |
| `--proxies` | `-p` | No | — | Archivo con un proxy por línea. |
| `--usar-env-proxy` | — | No | — | Usar ESTELA_PROXY_* del entorno. |
| `--rotacion` | `-r` | No | `10` | Rotar proxy cada N consultas. |
| `--hilos` | `-H` | No | `1` | Hilos paralelos. |
| `--timeout` | `-t` | No | `30` | Timeout por request (segundos). |
| `--verificar-proxy` | — | No | — | Comprobar proxy antes de empezar. |
| `--verbose` | `-v` | No | — | Log más detallado. |

### Ejemplos de ejecución

```bash
# Sin proxies (conexión directa)
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv -e errores.csv

# Con archivo de proxies y rotación cada 5 consultas
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv --proxies proxies.txt --rotacion 5

# Proxies desde variables de entorno ESTELA
set ESTELA_PROXIES_ENABLED=true
set ESTELA_PROXY_URL=proxy.ejemplo.com
set ESTELA_PROXY_PORT=8000
set ESTELA_PROXY_USER=usuario
set ESTELA_PROXY_PASS=clave
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv --usar-env-proxy

# 4 hilos, timeout 60 s, verificar proxy antes
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv -e errores.csv --hilos 4 -t 60 --verificar-proxy

# Configuración completa con delays personalizados
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv -e errores.csv --proxies proxies.txt -r 10 -H 2 --timeout 60 --delay-min 1 --delay-max 3 --verbose
```

---

## 4. cedulas_ejemplo.csv

### Descripción

Archivo de **datos de ejemplo** (no es un script). Contiene una columna `cedula` con números de identificación ecuatorianos de 10 dígitos para usar como entrada en todos los scripts de consulta.

### Estructura

- **Primera línea:** cabecera `cedula`.
- **Resto:** una cédula por línea (solo dígitos).
- **Encoding:** UTF-8.

Ejemplo de contenido:

```csv
cedula
1717823221
1705898797
0912473600
```

### Uso

Se usa como valor de `--input` / `-i` en cualquier script:

```bash
python consulta_multiple.py -i cedulas_ejemplo.csv -o resultados.csv
python consulta_ANT.py -i cedulas_ejemplo.csv -o resultados.csv
python consulta_ant_con_proxies.py -i cedulas_ejemplo.csv -o resultados.csv
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 5
```

Los scripts detectan automáticamente columnas como `cedula`, `cédula`, `identificacion`, `id`, etc.

---

## 5. batch_processor.py

### Descripción

Procesador **batch secuencial** que ejecuta el spider de Scrapy `ant_citaciones` con estrategias **anti-bloqueo**: delay aleatorio entre consultas, backoff exponencial con jitter ante HTTP 403, rotación de User-Agent y pausa larga cada N consultas. Pensado para evitar bloqueos por IP.

### Estructura del código

| Componente | Clase / Función | Descripción |
|------------|-----------------|-------------|
| Config | `USER_AGENTS`, `COLUMNAS_SALIDA` | Lista de User-Agent; columnas del CSV de salida (incluye estado_procesamiento, mensaje_error). |
| Enums | `EstadoProcesamiento`, `CodigoError` | PENDIENTE, EXITO, ERROR, SIN_DATOS; códigos como ERR_CONEXION, ERR_TIMEOUT, ERR_403_BLOQUEADO, etc. |
| I/O | `LectorCSV`, `EscritorCSV` | Lectura con detección de columna y validación 10/13 dígitos; escritura con append por resultado. |
| Ejecutor | `EjecutorSpider` | `ejecutar(cedula)`: aplica delay (y pausa cada 3 consultas), llama a `_ejecutar_spider` (subprocess a Scrapy), en 403 activa backoff y reintenta. Parsea JSON de salida y clasifica errores con `_clasificar_error`. |
| Batch | `ProcesadorBatch` | `procesar(reprocesar)`: si `reprocesar`, lee cédulas del archivo de errores; si no, del CSV de entrada. Procesa **secuencialmente** con `EjecutorSpider`, escribe cada resultado con `EscritorCSV`, muestra progreso cada 5 y resumen final. Maneja SIGINT para interrupción limpia. |

**Comando Scrapy que se lanza internamente (por cédula):**

```bash
scrapy crawl ant_citaciones -a cedula=X -o /tmp/xxx.json -L ERROR -s LOG_ENABLED=False -s USER_AGENT=... -s DOWNLOAD_DELAY=2 -s CONCURRENT_REQUESTS=1 ...
```

### Dependencias

- **Python 3**
- Proyecto **Scrapy** con spider `ant_citaciones` (busca `scrapy.cfg` en el directorio actual o padres).

### Cómo ejecutar

```bash
python batch_processor.py --input <archivo.csv> --output <resultados.csv> [opciones]
```

**Parámetros:**

| Parámetro | Forma corta | Requerido | Default | Descripción |
|-----------|-------------|-----------|---------|-------------|
| `--input` | `-i` | Sí | — | CSV de entrada. |
| `--output` | `-o` | Sí | — | CSV de salida. |
| `--errores` | `-e` | No | `<output>_errores.csv` | CSV de errores. |
| `--timeout` | `-t` | No | `120` | Timeout por consulta (s). |
| `--reintentos` | `-r` | No | `3` | Reintentos por fallo. |
| `--delay-min` | — | No | `8` | Delay mínimo entre consultas (s). |
| `--delay-max` | — | No | `15` | Delay máximo entre consultas (s). |
| `--directorio` | `-d` | No | Auto | Directorio del proyecto Scrapy. |
| `--reprocesar` | — | No | — | Reprocesar solo cédulas del archivo de errores. |
| `--verbose` | `-v` | No | — | Log detallado. |

### Ejemplos de ejecución

```bash
# Procesamiento estándar
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv

# Archivo de errores explícito
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv -e errores_batch.csv

# Reprocesar solo las cédulas que fallaron antes
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv --reprocesar

# Delays más largos para reducir 403
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv --delay-min 8 --delay-max 15

# Timeout 180 s, 5 reintentos, modo verbose
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv -t 180 -r 5 --verbose

# Indicar directorio del proyecto Scrapy
python batch_processor.py -i cedulas_ejemplo.csv -o resultados.csv -d C:\proyecto\scrapy_ant
```

---

## 6. batch_processor_citaciones.py

### Descripción

Procesador batch orientado a **grandes volúmenes** (p. ej. ~1.000.000 de registros). Divide la entrada en **lotes**, permite **varios hilos por lote**, **checkpoints** en JSON para reanudar y **reprocesamiento** solo de errores. Usa el mismo EjecutorSpider (Scrapy) que batch_processor, con escritura en disco optimizada por buffers.

### Estructura del código

| Componente | Clase / Función | Descripción |
|------------|-----------------|-------------|
| Lotes | `LectorCSV.leer_todas()`, `dividir_en_lotes(identificaciones, num_lotes)` | Lee todas las cédulas y las divide en N listas de tamaño similar. |
| Escritura | `EscritorCSV` | Buffer de salida y de errores (`buffer_size=100`); `escribir(resultado, lote_numero)`, `escribir_error(..., lote_numero, intentos)`; `flush()` para vaciar buffers. Columnas de salida incluyen `lote_numero`. |
| Checkpoint | `GestorCheckpoint` | `guardar(stats)`, `cargar()`, `eliminar()`. Guarda en JSON: lote_actual, total_lotes, registros_procesados, exitosos, errores, etc. |
| Ejecutor | `EjecutorSpider` | Igual concepto que en batch_processor (subprocess Scrapy, delays, backoff 403). Parámetros por defecto más agresivos (delays cortos) para alto volumen. |
| Batch | `ProcesadorBatchCitaciones` | `procesar(reprocesar, lote_inicial, lote_final)`: divide en lotes; por cada lote usa `ThreadPoolExecutor` con `num_hilos` (1–15); actualiza estadísticas globales; guarda checkpoint tras cada lote; opcionalmente solo procesa lotes desde/hasta un índice. |

### Dependencias

- **Python 3**
- Proyecto **Scrapy** con spider `ant_citaciones`.

### Cómo ejecutar

```bash
python batch_processor_citaciones.py --input <archivo.csv> --output <resultados.csv> [--lotes N] [--hilos N] [opciones]
```

**Parámetros principales:**

| Parámetro | Forma corta | Requerido | Default | Descripción |
|-----------|-------------|-----------|---------|-------------|
| `--input` | `-i` | Sí | — | CSV de entrada. |
| `--output` | `-o` | Sí | — | CSV de salida. |
| `--lotes` | `-l` | No | `10` | Número de lotes. |
| `--lote-inicial` | — | No | — | Primer lote a procesar (1-indexed). |
| `--lote-final` | — | No | — | Último lote a procesar (inclusive). |
| `--hilos` | `-H` | No | `1` (máx 15) | Hilos por lote. |
| `--errores` | `-e` | No | `<output>_errores.csv` | CSV de errores. |
| `--checkpoint` | `-c` | No | `<output>_checkpoint.json` | Archivo de checkpoint. |
| `--reprocesar` | — | No | — | Reprocesar solo registros del archivo de errores. |
| `--timeout`, `--reintentos`, `--delay-min`, `--delay-max`, `--verbose` | — | No | Varios | Igual que en batch_processor. |

### Ejemplos de ejecución

```bash
# 10 lotes, 1 hilo por lote (por defecto)
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 10

# 10 lotes con 3 hilos por lote
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 10 --hilos 3

# Procesar solo los lotes 5 a 8 (reanudar a mitad de proceso)
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 10 --lote-inicial 5 --lote-final 8 --hilos 2

# Reprocesar solo las cédulas que quedaron en errores
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --reprocesar

# 20 lotes, 5 hilos, delays más conservadores
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 20 --hilos 5 --delay-min 5 --delay-max 10

# Con archivo de log y checkpoint personalizado
python batch_processor_citaciones.py -i cedulas_ejemplo.csv -o resultados.csv --lotes 10 --hilos 4 --log proceso.log --checkpoint mi_checkpoint.json --verbose
```

---

## 7. Tabla comparativa de diferencias

| Aspecto | consulta_multiple.py | consulta_ANT.py | consulta_ant_con_proxies.py | batch_processor.py | batch_processor_citaciones.py |
|--------|----------------------|-----------------|------------------------------|--------------------|--------------------------------|
| **Motor de consulta** | Scrapy (subprocess) | HTTP urllib | HTTP requests | Scrapy (subprocess) | Scrapy (subprocess) |
| **Procesamiento** | Paralelo (hilos) | Lotes + pausa entre lotes | Paralelo opcional | Secuencial | Lotes + hilos por lote |
| **Proxies** | No | No | Sí (rotación) | No | No |
| **Dependencia Scrapy** | Sí | No | No | Sí | Sí |
| **Anti-bloqueo (403)** | No específico | Pausas entre lotes | Delay + proxies | Backoff + delays | Backoff + delays |
| **Salida CSV resultados** | Sí | Sí | Sí | Sí | Sí (+ lote_numero) |
| **Salida CSV errores** | Sí | Sí | Sí | Sí | Sí (+ lote, intentos) |
| **Checkpoint / reanudar** | No | No | No | No | Sí (JSON) |
| **Reprocesar solo errores** | No | No | No | Sí (--reprocesar) | Sí (--reprocesar) |
| **Lotes configurables** | No | Sí (implícito) | No | No | Sí (--lotes, --lote-inicial/final) |
| **Métricas / estimación** | Básico | Sí (1M consultas) | Resumen final | Progreso + resumen | Por lote + global + ETA |
| **Hilos por defecto** | 4 | 20 por lote | 1 | 1 (secuencial) | 1 (máx 15) |
| **Uso típico** | Pocas cédulas, ya tienes Scrapy | Muchas cédulas sin Scrapy | Muchas cédulas con riesgo de bloqueo IP | Volumen medio, evitar 403 | Volumen muy grande (ej. 1M), reanudable |

---

*Fin de la documentación.*
