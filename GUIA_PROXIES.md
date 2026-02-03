# Guía Completa de Proxies para Web Scraping

Esta guía proporciona información detallada sobre proveedores de proxies de pago y fuentes gratuitas para evitar bloqueos de IP en proyectos de scraping.

---

## Tabla de Contenidos

1. [¿Por qué usar Proxies?](#por-qué-usar-proxies)
2. [Tipos de Proxies](#tipos-de-proxies)
3. [Proveedores de Pago - Comparativa](#proveedores-de-pago---comparativa)
4. [Fuentes de Proxies Gratuitos](#fuentes-de-proxies-gratuitos)
5. [Recomendaciones según Caso de Uso](#recomendaciones-según-caso-de-uso)

---

## ¿Por qué usar Proxies?

| Problema | Solución con Proxies |
|----------|---------------------|
| Bloqueo de IP por muchas requests | Rotación automática de IPs |
| Rate limiting del servidor | Distribuir carga entre múltiples IPs |
| Restricciones geográficas | IPs de diferentes países |
| Detección de bots | IPs residenciales parecen usuarios reales |
| Baneos permanentes | Cambiar IP sin afectar tu conexión real |

---

## Tipos de Proxies

| Tipo | Descripción | Velocidad | Anonimato | Precio |
|------|-------------|-----------|-----------|--------|
| **Datacenter** | IPs de servidores en data centers | ⚡ Muy rápido | 🔶 Medio | 💰 Bajo |
| **Residencial** | IPs de usuarios reales (ISPs) | 🔶 Medio | ✅ Alto | 💰💰 Alto |
| **Residencial Rotativo** | Cambia IP automáticamente | 🔶 Medio | ✅ Alto | 💰💰 Alto |
| **ISP/Static Residential** | IP residencial fija | ⚡ Rápido | ✅ Alto | 💰💰💰 Muy alto |
| **Mobile** | IPs de redes móviles 4G/5G | 🔶 Variable | ✅ Muy alto | 💰💰💰 Muy alto |

---

## Proveedores de Pago - Comparativa

### Tabla Comparativa General

| Proveedor | Pool de IPs | Países | Precio Inicial | Tipo Principal | Calificación |
|-----------|-------------|--------|----------------|----------------|--------------|
| **Bright Data** | 72M+ | 195+ | $500/mes | Residencial | ⭐⭐⭐⭐⭐ |
| **Oxylabs** | 100M+ | 195+ | $300/mes | Residencial | ⭐⭐⭐⭐⭐ |
| **Smartproxy** | 55M+ | 195+ | $75/mes | Residencial | ⭐⭐⭐⭐ |
| **LumiProxy** | 90M+ | 195+ | $0.8/GB | Residencial | ⭐⭐⭐⭐ |
| **Rayobyte** | 300K+ | 27 | $45/mes | Datacenter | ⭐⭐⭐⭐ |
| **IPRoyal** | 8M+ | 195+ | $1.75/GB | Residencial | ⭐⭐⭐⭐ |
| **SOAX** | 8.5M+ | 150+ | $99/mes | Residencial | ⭐⭐⭐⭐ |
| **NetNut** | 85M+ | 150+ | $300/mes | Residencial | ⭐⭐⭐⭐ |
| **Webshare** | 30M+ | 50+ | $5.49/mes | Datacenter | ⭐⭐⭐ |
| **ProxyScrape** | Variable | 100+ | $3/mes | Mixto | ⭐⭐⭐ |

---

### Detalle por Proveedor

#### 1. Bright Data (antes Luminati)
| Característica | Detalle |
|----------------|---------|
| **URL** | [brightdata.com](https://brightdata.com) |
| **Pool de IPs** | 72+ millones |
| **Tipos** | Residencial, Datacenter, ISP, Mobile |
| **Países** | 195+ |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Tráfico | Características |
|------|--------|---------|-----------------|
| Pay As You Go | $8.40/GB | Variable | Sin compromiso |
| Growth | $500/mes | 69GB incluidos | Soporte prioritario |
| Business | $1,000/mes | 158GB incluidos | Manager dedicado |
| Enterprise | Personalizado | Ilimitado | SLA garantizado |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Mayor pool de IPs del mercado | Precio elevado |
| Excelente tasa de éxito (99.9%) | Curva de aprendizaje |
| API muy completa | Mínimo $500 para planes mensuales |
| Soporte 24/7 | Interfaz compleja |

---

#### 2. Oxylabs
| Característica | Detalle |
|----------------|---------|
| **URL** | [oxylabs.io](https://oxylabs.io) |
| **Pool de IPs** | 100+ millones |
| **Tipos** | Residencial, Datacenter, ISP |
| **Países** | 195+ |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Tráfico | Características |
|------|--------|---------|-----------------|
| Micro | $99/mes | 11GB | Básico |
| Starter | $300/mes | 44GB | + Geo-targeting |
| Advanced | $600/mes | 100GB | + Sticky sessions |
| Premium | Personalizado | Ilimitado | Todo incluido |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Pool masivo de IPs | Precio alto |
| Scraper API incluido | Requiere contrato anual para descuentos |
| 100% de uptime garantizado | Sin plan gratuito |
| Documentación excelente | - |

---

#### 3. Smartproxy
| Característica | Detalle |
|----------------|---------|
| **URL** | [smartproxy.com](https://smartproxy.com) |
| **Pool de IPs** | 55+ millones |
| **Tipos** | Residencial, Datacenter, ISP |
| **Países** | 195+ |
| **Protocolos** | HTTP, HTTPS |

| Plan | Precio | Tráfico | Características |
|------|--------|---------|-----------------|
| Micro | $75/mes | 5GB | Geo-targeting básico |
| Starter | $200/mes | 20GB | + API completa |
| Regular | $400/mes | 50GB | + Sticky sessions |
| Advanced | $800/mes | 100GB | + Soporte premium |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Buena relación precio/calidad | Pool menor que competidores |
| Extensión para Chrome | Sin SOCKS5 |
| 3 días de prueba | Soporte solo en inglés |
| Dashboard intuitivo | - |

---

#### 4. LumiProxy
| Característica | Detalle |
|----------------|---------|
| **URL** | [lumiproxy.com](https://lumiproxy.com) |
| **Pool de IPs** | 90+ millones |
| **Tipos** | Residencial, Datacenter, ISP, Ilimitado |
| **Países** | 195+ |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Tráfico | Características |
|------|--------|---------|-----------------|
| Residencial | $0.8/GB | Pay-as-you-go | Rotación automática |
| Ilimitado | $70/día | Sin límite | Uso intensivo |
| Static Residential | $3/IP | Por IP | IP fija residencial |
| Static Datacenter | $1.5/IP | Por IP | IP fija datacenter |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Plan ilimitado disponible | Empresa relativamente nueva |
| Soporte SOCKS5 | Documentación en desarrollo |
| Buen precio por GB | Menos integraciones |
| Sticky sessions 90 min | - |

---

#### 5. Rayobyte (antes Blazing SEO)
| Característica | Detalle |
|----------------|---------|
| **URL** | [rayobyte.com](https://rayobyte.com) |
| **Pool de IPs** | 300K+ datacenter, 25M+ residencial |
| **Tipos** | Residencial, Datacenter, ISP |
| **Países** | 27 |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Cantidad | Características |
|------|--------|----------|-----------------|
| Datacenter | $1.40/IP/mes | Desde 5 IPs | Semi-dedicado |
| Residencial | $15/GB | Pay-as-you-go | Rotación automática |
| ISP | $3/IP/mes | Desde 5 IPs | IP estática residencial |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Muy económico para datacenter | Menos países disponibles |
| Reemplazo de IPs gratis | Pool residencial limitado |
| Sin límite de bandwidth | Interfaz básica |
| Bueno para principiantes | - |

---

#### 6. IPRoyal
| Característica | Detalle |
|----------------|---------|
| **URL** | [iproyal.com](https://iproyal.com) |
| **Pool de IPs** | 8+ millones |
| **Tipos** | Residencial, Datacenter, ISP, Mobile |
| **Países** | 195+ |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Tráfico | Características |
|------|--------|---------|-----------------|
| Residencial | $1.75/GB | Pay-as-you-go | El tráfico no expira |
| Datacenter | $1.39/IP/mes | Ilimitado | Ancho de banda ilimitado |
| ISP | $2.40/IP/mes | Ilimitado | IP estática |
| Mobile | $6/GB | Pay-as-you-go | 4G/5G real |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| El tráfico nunca expira | Pool más pequeño |
| Precio competitivo | Menos features avanzados |
| Mobile proxies disponibles | Soporte básico |
| SOCKS5 incluido | - |

---

#### 7. Webshare
| Característica | Detalle |
|----------------|---------|
| **URL** | [webshare.io](https://webshare.io) |
| **Pool de IPs** | 30+ millones |
| **Tipos** | Datacenter, Residencial |
| **Países** | 50+ |
| **Protocolos** | HTTP, HTTPS, SOCKS5 |

| Plan | Precio | Proxies | Características |
|------|--------|---------|-----------------|
| Free | $0/mes | 10 proxies | Limitado |
| Proxy | $5.49/mes | 100 proxies | Datacenter |
| Starter | $29.99/mes | 500 proxies | + Residencial |
| Standard | $79.99/mes | 2,500 proxies | + API completa |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Plan gratuito disponible | Pool datacenter principalmente |
| Muy económico | Menos países |
| API simple | Menor tasa de éxito en sitios difíciles |
| Dashboard fácil | - |

---

## Fuentes de Proxies Gratuitos

> ⚠️ **Advertencia**: Los proxies gratuitos tienen alta tasa de fallo, son lentos y pueden ser inseguros. Úsalos solo para pruebas.

### Tabla de Fuentes Gratuitas

| Fuente | URL | Tipos | Actualización | Calificación |
|--------|-----|-------|---------------|--------------|
| **Free Proxy List** | [free-proxy-list.net](https://free-proxy-list.net) | HTTP/HTTPS | Cada 10 min | ⭐⭐⭐ |
| **ProxyScrape** | [proxyscrape.com/free-proxy-list](https://proxyscrape.com/free-proxy-list) | HTTP/SOCKS | Tiempo real | ⭐⭐⭐⭐ |
| **Geonode** | [geonode.com/free-proxy-list](https://geonode.com/free-proxy-list) | HTTP/HTTPS | Cada hora | ⭐⭐⭐ |
| **ProxyNova** | [proxynova.com](https://proxynova.com) | HTTP | Cada 10 min | ⭐⭐⭐ |
| **Spys.one** | [spys.one](https://spys.one/en/) | HTTP/SOCKS | Variable | ⭐⭐⭐ |
| **OpenProxy** | [openproxy.space](https://openproxy.space) | HTTP/SOCKS | Diario | ⭐⭐ |
| **SSL Proxies** | [sslproxies.org](https://sslproxies.org) | HTTPS | Cada 10 min | ⭐⭐⭐ |
| **US Proxy** | [us-proxy.org](https://us-proxy.org) | HTTP | Cada 10 min | ⭐⭐ |
| **Free Proxy CZ** | [free-proxy.cz](http://free-proxy.cz) | HTTP/HTTPS | Diario | ⭐⭐ |
| **PubProxy** | [pubproxy.com](http://pubproxy.com) | HTTP | API gratuita | ⭐⭐⭐ |

---

### Detalle de Fuentes Gratuitas

#### 1. ProxyScrape (Recomendado)
| Característica | Detalle |
|----------------|---------|
| **URL** | [proxyscrape.com/free-proxy-list](https://proxyscrape.com/free-proxy-list) |
| **Tipos** | HTTP, HTTPS, SOCKS4, SOCKS5 |
| **Formato** | Lista, API, TXT |
| **Filtros** | País, anonimato, protocolo, timeout |

```bash
# API directa para obtener proxies
curl "https://api.proxyscrape.com/v2/?request=getproxies&protocol=http&timeout=10000&country=all"
```

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| API gratuita disponible | Alta tasa de fallo (30-50%) |
| Actualización frecuente | Velocidad variable |
| Filtros avanzados | No hay garantía de uptime |
| Múltiples protocolos | Posibles IPs comprometidas |

---

#### 2. Free Proxy List
| Característica | Detalle |
|----------------|---------|
| **URL** | [free-proxy-list.net](https://free-proxy-list.net) |
| **Tipos** | HTTP, HTTPS |
| **Formato** | Tabla HTML, exportable |
| **Cantidad** | ~300 proxies |

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| Fácil de usar | Solo HTTP/HTTPS |
| Muestra anonimato | Muchos proxies muertos |
| Indica país | Sin API |
| Lista limpia | Actualización lenta |

---

#### 3. Geonode
| Característica | Detalle |
|----------------|---------|
| **URL** | [geonode.com/free-proxy-list](https://geonode.com/free-proxy-list) |
| **Tipos** | HTTP, HTTPS, SOCKS4, SOCKS5 |
| **Formato** | Lista, API |
| **Filtros** | Múltiples filtros avanzados |

```bash
# API gratuita
curl "https://proxylist.geonode.com/api/proxy-list?limit=50&page=1&sort_by=lastChecked&sort_type=desc"
```

| ✅ Ventajas | ❌ Desventajas |
|-------------|----------------|
| API REST gratuita | Límite de requests |
| Buenos filtros | Requiere registro para más |
| Verificación de velocidad | - |
| Exportación múltiple | - |

---

### Scripts para Obtener Proxies Gratuitos

#### Python - Obtener de ProxyScrape

```python
import requests

def obtener_proxies_gratuitos():
    """Obtiene proxies gratuitos de ProxyScrape."""
    url = "https://api.proxyscrape.com/v2/"
    params = {
        "request": "getproxies",
        "protocol": "http",
        "timeout": "10000",
        "country": "all",
        "ssl": "all",
        "anonymity": "all"
    }
    
    response = requests.get(url, params=params)
    proxies = response.text.strip().split('\n')
    
    return [f"http://{p}" for p in proxies if p]

# Uso
proxies = obtener_proxies_gratuitos()
print(f"Obtenidos {len(proxies)} proxies")
```

#### Python - Verificar Proxies

```python
import requests
from concurrent.futures import ThreadPoolExecutor

def verificar_proxy(proxy):
    """Verifica si un proxy funciona."""
    try:
        response = requests.get(
            "https://httpbin.org/ip",
            proxies={"http": proxy, "https": proxy},
            timeout=5
        )
        return proxy if response.status_code == 200 else None
    except:
        return None

def obtener_proxies_funcionando(proxies, max_workers=20):
    """Filtra solo los proxies que funcionan."""
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        resultados = list(executor.map(verificar_proxy, proxies))
    
    return [p for p in resultados if p]
```

---

## Comparativa: Gratuito vs Pago

| Aspecto | Proxies Gratuitos | Proxies de Pago |
|---------|-------------------|-----------------|
| **Costo** | $0 | $1 - $15/GB |
| **Tasa de éxito** | 20-50% | 95-99.9% |
| **Velocidad** | Lenta/Variable | Rápida/Estable |
| **Anonimato** | Bajo/Variable | Alto/Garantizado |
| **Seguridad** | ⚠️ Riesgo de MITM | ✅ Seguro |
| **Soporte** | Ninguno | 24/7 |
| **Uptime** | Variable | 99%+ garantizado |
| **Uso recomendado** | Pruebas, desarrollo | Producción |
| **Sitios difíciles** | ❌ No funciona | ✅ Alta tasa éxito |

---

## Recomendaciones según Caso de Uso

### Para este proyecto (Consultas ANT Ecuador)

| Escenario | Recomendación | Costo Estimado |
|-----------|---------------|----------------|
| **Pruebas/Desarrollo** | Sin proxy o ProxyScrape gratuito | $0 |
| **Bajo volumen (<100/día)** | Sin proxy (conexión directa) | $0 |
| **Medio volumen (100-1000/día)** | Rayobyte Datacenter o Webshare | $5-45/mes |
| **Alto volumen (1000+/día)** | LumiProxy o Smartproxy Residencial | $75-200/mes |
| **Producción crítica** | Bright Data o Oxylabs | $300+/mes |

### Configuración Recomendada

```bash
# Para volumen bajo-medio (económico)
python consulta_ant_con_proxies.py -i cedulas.csv -o resultados.csv \
    --proxy-url proxy.webshare.io \
    --proxy-port 80 \
    --proxy-user TU_USUARIO \
    --proxy-pass TU_PASSWORD \
    --rotacion 20 \
    --hilos 2

# Para volumen alto (mejor rendimiento)
python consulta_ant_con_proxies.py -i cedulas.csv -o resultados.csv \
    --proxy-url la.residential.rayobyte.com \
    --proxy-port 8000 \
    --proxy-user TU_USUARIO \
    --proxy-pass TU_PASSWORD \
    --rotacion 10 \
    --hilos 4
```

---

## Resumen de Precios

### Opción Más Económica por Categoría

| Categoría | Proveedor | Precio |
|-----------|-----------|--------|
| **Gratuito** | ProxyScrape | $0 |
| **Datacenter económico** | Webshare | $5.49/mes (100 proxies) |
| **Residencial económico** | IPRoyal | $1.75/GB |
| **Ilimitado** | LumiProxy | $70/día |
| **Enterprise** | Bright Data | $500+/mes |

### Mejor Relación Precio/Calidad

1. **Webshare** - Para empezar con presupuesto bajo
2. **IPRoyal** - El tráfico nunca expira
3. **Smartproxy** - Balance entre precio y features
4. **LumiProxy** - Plan ilimitado único en el mercado

---

## Enlaces Útiles

| Recurso | URL |
|---------|-----|
| Comparador de proxies | [proxyway.com](https://proxyway.com) |
| Reviews de proxies | [scrapehero.com/best-rotating-proxy](https://www.scrapehero.com/best-rotating-proxy-services/) |
| Verificador de proxies | [hidemy.name/proxy-checker](https://hidemy.name/en/proxy-checker/) |
| Lista actualizada | [proxyscrape.com](https://proxyscrape.com) |

---

**Última actualización:** Febrero 2026

> 💡 **Tip**: Para proyectos de producción, siempre invierte en proxies de pago. El tiempo perdido por proxies que fallan cuesta más que una suscripción mensual.
