# 🎵 Pipeline de Business Intelligence — Music Streaming (Spotify)
**Proyecto Final — Programación Avanzada 2026-I**

---

## 👥 Equipo

| Integrante | Rol | GitHub |
|---|---|---|
| Integrante 1 | Por definir | @usuario1 |
| Integrante 2 | Por definir | @usuario2 |
| Integrante 3 | Por definir | @usuario3 |

---

## 📌 Estado del Proyecto

| Paso | Descripción | Estado |
|---|---|---|
| 1 | Elección del dominio | ✅ Completado |
| 1.5 | Confirmación del dataset | ✅ Completado |
| 2 | Estructura del repositorio Git | ✅ Completado |
| 3 | Configuración de Databricks | ⏳ Pendiente |
| 4 | Capa Bronze — Ingesta con Kafka | ⏳ Pendiente |
| 5 | Capa Silver — Limpieza con Spark | ⏳ Pendiente |
| 6 | Capa Gold — Transformaciones con dbt | ⏳ Pendiente |
| 7 | Dashboards (Power BI / Tableau) | ⏳ Pendiente |
| 8 | Documento de arquitectura final | ⏳ Pendiente |
| 9 | Presentación y defensa | ⏳ Pendiente |

---

## 🎯 Dominio de Negocio

**Music Streaming — inspirado en el modelo de Spotify**

Una plataforma de música en streaming genera eventos de forma continua: cada reproducción, pausa, skip o búsqueda es un evento. Este dominio justifica el uso de procesamiento de datos masivos y en tiempo real de forma completamente natural.

### ¿Por qué este dominio?

- Kafka es natural: cada acción del usuario genera un evento en tiempo real
- El modelo dimensional es rico: canciones, artistas, usuarios, dispositivos, geografía, tiempo
- Los KPIs de negocio son claros y fáciles de comunicar
- El caso de negocio es comprensible para cualquier audiencia
- Es original — diferente a los dominios sugeridos en el enunciado

---

## 📦 Dataset

### Fuente 1 — Catálogo de canciones (datos estáticos)
- **Nombre:** Spotify Tracks Dataset
- **Autor:** MaharshiPandya
- **URL:** https://www.kaggle.com/datasets/maharshipandya/spotify-tracks-dataset
- **Archivo:** `dataset.csv`
- **Tamaño:** 20.12 MB
- **Filas:** ~114,000 canciones
- **Géneros:** 125 géneros diferentes
- **Usability Score:** 10.00
- **Licencia:** Open Database

#### Columnas principales

| Columna | Tipo | Descripción |
|---|---|---|
| track_id | string | ID único de la canción |
| track_name | string | Nombre de la canción |
| artists | string | Artista(s) |
| album_name | string | Álbum |
| track_genre | string | Género musical |
| popularity | int | Popularidad (0–100) |
| duration_ms | int | Duración en milisegundos |
| explicit | bool | Contenido explícito |
| danceability | float | Qué tan bailable es (0–1) |
| energy | float | Intensidad y actividad (0–1) |
| loudness | float | Volumen general en dB |
| tempo | float | Tempo en BPM |
| valence | float | Positividad musical (0–1) |

### Fuente 2 — Eventos de escucha (datos en streaming)
- **Tipo:** Sintéticos generados con Python
- **Productor:** Script Kafka Producer (`/ingestion/kafka/producer.py`)
- **Estructura del evento:**
```json
{
  "event_id": "uuid",
  "user_id": "uuid",
  "track_id": "string",
  "timestamp": "ISO8601",
  "device": "mobile | desktop | tablet | smart_speaker",
  "country": "string",
  "duration_listened_ms": "int",
  "event_type": "play | skip | pause | complete"
}
```

---

## 🏗️ Arquitectura — Medallion (Bronze → Silver → Gold)

```
[Kafka Producer] 
      ↓ eventos en tiempo real
[Bronze Layer] — datos crudos sin transformar (Delta Lake)
      ↓ Spark Structured Streaming
[Silver Layer] — datos limpios y enriquecidos (Delta Lake)
      ↓ dbt transformations
[Gold Layer] — modelo dimensional Star Schema (Delta Lake)
      ↓
[Dashboards] — Power BI / Tableau
```

### Star Schema (Gold Layer)

**Tabla de hechos:**
- `fact_listening_events` — un registro por evento de escucha

**Tablas de dimensiones:**
- `dim_tracks` — catálogo de canciones
- `dim_artists` — artistas
- `dim_users` — usuarios simulados
- `dim_date` — dimensión de tiempo
- `dim_device` — tipo de dispositivo
- `dim_country` — geografía

---

## 📊 Dashboards Planificados

| # | Dashboard | Audiencia | KPIs principales |
|---|---|---|---|
| 1 | Ejecutivo | Dirección | Top canciones, géneros más escuchados, usuarios activos |
| 2 | Operacional | Analistas | Escuchas por hora/día, país, dispositivo, duración promedio |
| 3 | Calidad de datos | Ingeniería | Eventos nulos, canciones sin género, usuarios sin país |

---

## 🛠️ Stack Tecnológico

| Tecnología | Uso |
|---|---|
| Apache Kafka | Ingesta de eventos en tiempo real |
| Apache Spark (Structured Streaming) | Procesamiento distribuido |
| Delta Lake | Almacenamiento en capas Bronze/Silver/Gold |
| Databricks | Plataforma de ejecución |
| dbt | Transformaciones SQL versionadas con tests |
| Power BI / Tableau | Visualización y dashboards |
| Git / GitHub | Control de versiones |
| Python | Scripts de generación de eventos y utilidades |

---

## 📁 Estructura del Repositorio

**URL:** https://github.com/JaiVane/spotify-bi-pipeline

```
spotify-bi-pipeline/
│
├── ingestion/                  # Bronze — Kafka
│   └── kafka/
│       ├── topics/
│       ├── producer.py         # Genera eventos de escucha
│       └── consumer.py         # Consume eventos de Kafka
│
├── processing/                 # Silver — Spark
│   └── notebooks/
│       ├── bronze.py           # Ingesta datos crudos
│       ├── silver.py           # Limpieza y enriquecimiento
│       └── utils.py            # Funciones compartidas
│
├── transformation/             # Gold — dbt
│   └── dbt_project/
│       ├── models/             # Modelos SQL (Star Schema)
│       ├── tests/              # Tests de calidad de datos
│       └── seeds/              # Datos de referencia
│
├── dashboards/                 # Visualización
│   ├── ejecutivo/
│   ├── operacional/
│   └── calidad_datos/
│
├── docs/                       # Documentación
│   ├── arquitectura/
│   └── decisiones/
│
├── README.md
├── AGENTS.md
├── requirements.txt
├── .env.example
├── docker-compose.yml
└── .gitignore
```

### 🌿 Estrategia de Ramas

| Rama | Responsable | Propósito |
|---|---|---|
| `main` | Todos | Código aprobado y funcional |
| `dev` | Todos | Integración antes de subir a main |
| `feature/ingestion` | Integrante 1 | Kafka producer y consumer |
| `feature/processing` | Integrante 2 | Spark notebooks Bronze y Silver |
| `feature/transformation` | Integrante 3 | dbt modelos Gold y dashboards |

### 📏 Reglas de Git

- Nunca trabajar directo en `main`
- Cada integrante trabaja en su rama `feature/`
- Al terminar una tarea → Pull Request a `dev`
- Cuando `dev` está estable → merge a `main`
- Mensajes de commit en formato: `feat:`, `fix:`, `docs:`, `refactor:`

---

## 📝 Bitácora de Decisiones

| Fecha | Decisión | Razón |
|---|---|---|
| 2026-05 | Dominio: Music Streaming | Natural para Kafka, KPIs claros, original |
| 2026-05 | Dataset: Spotify Tracks (Kaggle) | 114k canciones, 125 géneros, licencia abierta, score 10.0 |
| 2026-05 | Eventos sintéticos con Python | Simula streaming real sin depender de API de pago |
| 2026-05 | Estrategia de ramas: main/dev/feature | Evita conflictos entre los 3 integrantes |
| 2026-05 | IDE: Antigravity (Google) en Windows | Entorno disponible del equipo |
