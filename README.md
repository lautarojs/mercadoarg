# 🇦🇷 Monitor de Mercado AR

Dashboard integral para análisis de tendencias macroeconómicas en Argentina, desarrollado con Streamlit.

![Python](https://img.shields.io/badge/Python-3.9+-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-1.30+-red)
![License](https://img.shields.io/badge/License-MIT-green)

## 📊 Características

### Fuentes de Datos en Tiempo Real

| Fuente | Datos | API Key |
|--------|-------|---------|
| **ArgentinaDatos API** | Inflación, Dólar (Blue/Oficial/MEP/CCL), Riesgo País | ❌ No requiere |
| **CAME Scraping** | Ventas Minoristas Pyme por rubro | ❌ No requiere |
| **Jornalia API** | Noticias económicas de +50 medios argentinos | ✅ Gratuita |
| **Series de Tiempo** | Datos históricos oficiales (INDEC/BCRA) | ❌ No requiere |

### Paneles Disponibles

1. **📈 Tablero Macro**: Inflación, cotizaciones del dólar, riesgo país con gráficos interactivos
2. **🛒 Consumo y Ventas**: Índice de ventas minoristas CAME desglosado por rubro
3. **📰 Noticias**: Feed de noticias económicas locales
4. **📚 Histórico**: Evolución acumulada con exportación a CSV
5. **⚙️ Configuración**: Estado de fuentes y mantenimiento

### Persistencia de Datos

- Los datos se guardan automáticamente en archivos CSV locales
- Permite análisis histórico sin depender de que las fuentes tengan todo el histórico
- Fallback automático: si una fuente falla, muestra los últimos datos guardados

## 🚀 Instalación

### Prerrequisitos

- Python 3.9 o superior
- pip (gestor de paquetes de Python)

### Pasos

```bash
# 1. Clonar o descargar el proyecto
cd monitor_mercado

# 2. Crear entorno virtual (recomendado)
python -m venv venv

# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate

# 3. Instalar dependencias
pip install -r requirements.txt

# 4. Ejecutar la aplicación
streamlit run app.py
```

La aplicación se abrirá automáticamente en `http://localhost:8501`

## 🔑 Configuración de API Keys (Opcional)

### Jornalia - Noticias Argentinas

1. Visitá [jornalia.net](https://jornalia.net/)
2. Registrate con tu email (gratuito)
3. Copiá tu API key del panel de usuario
4. Pegala en la sección de configuración del dashboard

### NewsAPI - Alternativa Internacional

1. Visitá [newsapi.org/register](https://newsapi.org/register)
2. Registrate (100 requests/día gratis)
3. Usá el endpoint con `country=ar` para filtrar Argentina

## 📁 Estructura del Proyecto

```
monitor_mercado/
├── app.py                 # Aplicación principal
├── requirements.txt       # Dependencias
├── README.md             # Este archivo
└── data/                  # Datos persistidos (se crea automáticamente)
    ├── historico_inflacion.csv
    ├── historico_dolar.csv
    ├── historico_riesgo_pais.csv
    ├── historico_came_ventas.csv
    └── historico_noticias.csv
```

## 🛠️ Personalización

### Agregar Nuevas Fuentes

El código está modularizado para facilitar extensiones:

```python
@st.cache_data(ttl=3600)  # Cache de 1 hora
def fetch_nueva_fuente() -> dict:
    """
    Template para agregar una nueva fuente de datos.
    """
    result = {"status": "success", "data": [], "message": ""}
    
    try:
        response = requests.get("URL_DE_LA_API", timeout=15)
        response.raise_for_status()
        # Procesar datos...
        result["data"] = response.json()
        
        # Guardar en CSV para persistencia
        save_to_csv(df, CSV_PATHS["nueva_fuente"])
        
    except Exception as e:
        result["status"] = "error"
        result["message"] = str(e)
        # Fallback a datos locales
        df_local = load_from_csv(CSV_PATHS["nueva_fuente"])
        if not df_local.empty:
            result["status"] = "cached"
            result["data"] = df_local.to_dict('records')
    
    return result
```

### Modificar Estilos

Los estilos CSS están centralizados al inicio del archivo `app.py` dentro del bloque `st.markdown("""<style>...</style>""")`.

## 📈 APIs Utilizadas

### ArgentinaDatos API (Gratuita, sin registro)

```python
# Endpoints principales
BASE = "https://api.argentinadatos.com"

# Inflación mensual
GET /v1/finanzas/indices/inflacion

# Cotizaciones del dólar
GET /v1/cotizaciones/dolares

# Riesgo país
GET /v1/finanzas/indices/riesgo-pais
```

[Documentación completa](https://argentinadatos.com/docs/)

### API de Series de Tiempo (Gobierno Argentina)

```python
# Endpoint base
BASE = "https://apis.datos.gob.ar/series/api/series"

# Ejemplo: EMAE
GET ?ids=11.3_VMATC_2004_M_12&limit=24&format=json
```

[Explorador de Series](https://datos.gob.ar/series)

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Para cambios importantes:

1. Fork del repositorio
2. Crear una rama (`git checkout -b feature/nueva-funcionalidad`)
3. Commit de cambios (`git commit -am 'Agrega nueva funcionalidad'`)
4. Push a la rama (`git push origin feature/nueva-funcionalidad`)
5. Crear Pull Request

## 📄 Licencia

MIT License - Ver archivo LICENSE para más detalles.

## 🙏 Agradecimientos

- [ArgentinaDatos](https://argentinadatos.com/) - API pública de datos económicos
- [CAME](https://redcame.org.ar/) - Datos de ventas minoristas Pyme
- [Jornalia](https://jornalia.net/) - API de noticias argentinas
- [Datos Argentina](https://datos.gob.ar/) - Portal de datos abiertos del gobierno

---

Desarrollado con ❤️ en Argentina
