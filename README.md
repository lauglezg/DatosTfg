# Recomendador de itinerarios turísticos en transporte público

Este repositorio contiene los datos y el cuaderno principal desarrollados para el Trabajo de Fin de Grado **“Recomendador de itinerarios turísticos en transporte público”**.

El objetivo del proyecto es generar itinerarios turísticos realistas en Santa Cruz de Tenerife y San Cristóbal de La Laguna utilizando transporte público, concretamente guaguas de TITSA y tranvía de Metrotenerife. El sistema combina datos abiertos en formato GTFS con un conjunto de puntos de interés turístico y aplica técnicas metaheurísticas, principalmente GRASP y VNS, para construir y mejorar las rutas.

## Contenido del repositorio

```text
.
├── TFG_LauraGonzalez2026.ipynb
├── Pois_38 - S_C_LL_poi_info.csv
├── Pois_38 - S_C_LL_time_open.csv
├── Pois_22 - S_C_poi_info.csv
├── Pois_22 - S_C_time_open.csv
├── Pois_25 - poi_info.csv
├── Pois_25 - time_open.csv
├── fichero-zip-de-google-transit-tranvia.zip   (Descargado el día 3/06/2026)
├── fichero-zip-de-google-transit.zip
└── Experimentos/
    ├── exp1_convergencia.csv
    ├── exp2_estrategias_sc.csv
    ├── exp2_estrategias_ll.csv
    ├── exp3_estrategias_ambos.csv
    └── exp4_escenarios.csv
```

## Cuaderno principal

El fichero `TFG_LauraGonzalez2026.ipynb` contiene la implementación completa del prototipo. Incluye:

- carga y preparación de datos GTFS de TITSA y Metrotenerife;
- carga y normalización de los puntos de interés turístico;
- combinación de la red de guaguas y tranvía;
- cálculo de paradas cercanas a cada POI;
- construcción de la matriz de tiempos dependiente de la franja horaria;
- algoritmo constructivo GRASP;
- estrategias de mejora VNS básico y VNS general;
- patrón `Strategy` para intercambiar estrategias de mejora;
- patrón `Facade` mediante la clase `RouteRecommender`;
- generación de rutas de un día y de varios días;
- exclusión e inclusión de POIs en una ruta;
- visualización de itinerarios sobre mapas interactivos con Folium;
- experimentos de convergencia, comparación de estrategias y escenarios de uso.

## Datos turísticos

Los datos turísticos se organizan en dos tipos de CSV:

- `*_poi_info.csv`: información descriptiva de los puntos de interés.
- `*_time_open.csv`: horarios de apertura de los puntos de interés.

La versión principal del prototipo utiliza:

- `Pois_38 - S_C_LL_poi_info.csv`
- `Pois_38 - S_C_LL_time_open.csv`

Estos ficheros contienen 38 puntos de interés:

- 22 en Santa Cruz de Tenerife;
- 16 en San Cristóbal de La Laguna.

## Datos de transporte

El proyecto utiliza datos GTFS de dos operadores:

- **TITSA**, para la red de guaguas.
- **Metrotenerife**, para la red de tranvía.

El cuaderno descarga el GTFS de TITSA desde una release del propio repositorio:

```python
ZIP_URL = "https://github.com/lauglezg/DatosTfg/releases/download/gtfs/fichero-zip-de-google-transit.3.zip"
```

El GTFS del tranvía se carga desde:

```python
ZIP_URL2 = "https://github.com/lauglezg/DatosTfg/raw/main/fichero-zip-de-google-transit-tranvia.zip"
```

> Nota: si se ejecuta el cuaderno fuera de Google Colab, conviene comprobar que los ficheros GTFS están disponibles localmente o que las URL siguen activas.

## Requisitos

El proyecto está preparado para ejecutarse en Google Colaboratory. Las principales bibliotecas utilizadas son:

```python
folium
matplotlib
numpy
pandas
requests
```

En un entorno local pueden instalarse con:

```bash
pip install folium matplotlib numpy pandas requests
```

## Ejecución

La forma recomendada de ejecutar el proyecto es abrir el cuaderno:

```text
TFG_LauraGonzalez2026.ipynb
```

Después, ejecutar las celdas en orden. El flujo general es:

1. Importar librerías.
2. Descargar o cargar los datos GTFS.
3. Cargar los CSV de puntos de interés y horarios.
4. Preparar y combinar los datos de transporte.
5. Calcular paradas cercanas y matriz de tiempos.
6. Generar rutas con `RouteRecommender`.
7. Visualizar los itinerarios en mapas Folium.
8. Ejecutar los experimentos, si se desea reproducir la evaluación.

Ejemplo básico de uso:

```python
reco = RouteRecommender()

res = reco.generate(
    dia="16072026",
    horas=7,
    municipio="Santa Cruz",
    hotel="Hotel Barcelo Contemporaneo (Santa Cruz)"
)
```

También se puede cambiar la estrategia de mejora:

```python
reco.set_strategy(VNSStrategy())
```

Y replanificar una ruta excluyendo un punto de interés:

```python
res_modificado = reco.replan_without(
    res,
    ["Auditorio de Tenerife Adan Martin"]
)
```

## Experimentos

La carpeta `Experimentos/` contiene los CSV generados durante la evaluación experimental:

- `exp1_convergencia.csv`: análisis del número de construcciones GRASP.
- `exp2_estrategias_sc.csv`: comparación de estrategias en Santa Cruz.
- `exp2_estrategias_ll.csv`: comparación de estrategias en La Laguna.
- `exp3_estrategias_ambos.csv`: comparación de estrategias considerando ambos municipios.
- `exp4_escenarios.csv`: evaluación de distintos escenarios de uso.

Estos resultados se utilizan para generar las tablas y gráficas incluidas en la memoria del TFG.

## Funcionalidades principales

El prototipo permite:

- generar itinerarios turísticos de un día;
- planificar estancias de varios días sin repetir POIs;
- utilizar guaguas, tranvía y desplazamientos a pie;
- respetar horarios de apertura de los POIs;
- limitar el número de visitas por categoría;
- excluir puntos de interés no deseados;
- forzar la inclusión de un POI concreto;
- replanificar rutas ya generadas;
- representar las rutas sobre mapas interactivos.

## Limitaciones

El prototipo se ha desarrollado con un conjunto acotado de puntos de interés y con datos turísticos preparados de forma semiautomática. Para ampliar el sistema a más zonas de Tenerife sería necesario incorporar nuevos POIs, revisar sus horarios y completar atributos como el tiempo estimado de visita y la puntuación turística.

Además, los datos GTFS empleados corresponden a una fecha concreta de descarga. Para reproducir los resultados con exactitud, es recomendable utilizar la misma versión de los datos de transporte.

## Autora

**Laura González González**  
Trabajo de Fin de Grado en Ingeniería Informática  
Universidad de La Laguna
