# Turismo Argentina: diagnóstico de recuperación regional (2017-2023)

Dashboard en Power BI que mide cómo se recuperó el turismo en las 7 regiones de Argentina tras la pandemia, qué patrones aparecen y qué acciones sugiere cada uno.

> **Contexto:** trabajo grupal del bootcamp de Análisis de Datos de Jupi Digital (G19). Es un mock-up con fines formativos, no un proyecto real de la Subsecretaría de Turismo.

## Pregunta de negocio

¿Qué regiones recuperaron su nivel prepandemia, cuáles quedaron rezagadas y qué indicadores explican la diferencia?

**Stakeholder implícito:** un área de planificación y promoción turística que necesita decidir en qué regiones priorizar acciones.

## Qué contiene el dashboard

| Hoja | Qué responde |
|---|---|
| Resumen Ejecutivo | Volumen total de viajeros, turistas, ocupación y gasto, con filtros por año, mes y región |
| Diagnóstico regional | Recuperación 2023 vs. promedio 2017-2019 por región y sector, y brecha entre ocupación hotelera y turismo interno |
| Turismo interno | Demanda nacional, pernoctaciones, estadía media y gasto por región |

![Resumen Ejecutivo](imagenes/01-resumen-ejecutivo.png)
![Diagnóstico regional](imagenes/02-diagnostico-regional.png)
![Turismo interno](imagenes/03-turismo-interno.png)

## Metodología

- **Período:** 2017-2023, con segmentación COVID / Pre-COVID / Recuperación.
- **Regiones:** Buenos Aires, CABA, Córdoba, Cuyo, Litoral, Norte y Patagonia.
- **Indicador de recuperación:** valor 2023 dividido por el promedio 2017-2019, en tres dimensiones: viajeros hospedados, turismo interno y conectividad aérea.
- **Brecha:** diferencia, en puntos porcentuales, entre la recuperación de la ocupación hotelera y la del turismo interno.
- **Clasificación regional:** al cruzar las tres dimensiones, las 7 regiones se agrupan en 3 patrones de recuperación claramente distintos.
- **Medidas DAX:** detalle en [`docs/medidas-dax.md`](docs/medidas_dax.md).

## Resultados: tres patrones de recuperación

La recuperación postpandemia no fue uniforme.

| Región | Viajeros hospedados | Turismo interno | Conectividad aérea |
|---|---|---|---|
| Buenos Aires | 104,7 % | 99,4 % | 82,2 % |
| CABA | 92,7 % | 84,7 % | 118,4 % |
| Córdoba | 98,7 % | 96,2 % | 90,8 % |
| Cuyo | 94,9 % | 86,2 % | 116,6 % |
| Litoral | 93,2 % | 98,9 % | 106,4 % |
| Norte | 94,8 % | 76,2 % | 122,6 % |
| Patagonia | 111,0 % | 108,0 % | 132,5 % |

*Recuperación = 2023 vs. promedio 2017-2019.*

### Nivel 1: Líderes (Patagonia, Buenos Aires)

Ambas superaron su nivel prepandemia, pero por caminos distintos: Patagonia se apoyó en la conectividad aérea y Buenos Aires es prácticamente independiente de ella. Son dos modelos de éxito, no uno solo.

Patagonia, además, tiene la estadía media más larga entre las regiones (7,15 noches en el período completo) y un gasto promedio por turista alto, un modelo de "menos volumen, más valor".

### Nivel 2: Estables sin despegue (Córdoba, Litoral)

Mantienen un consumo turístico interno saludable, cercano a niveles prepandemia. Pero en ambas hay una brecha consistente entre turistas internos y viajeros hospedados en 2023:

- **Litoral:** 4,7 millones de turistas internos vs. 2,9 millones de viajeros hospedados. Solo 6 de cada 10 turistas se aloja en el circuito hotelero formal.
- **Córdoba:** 3,6 millones vs. 2,4 millones, una proporción similar.

Buena parte de la demanda se resuelve por fuera del sistema hotelero medido: alojamiento no hotelero, casas de familia o excursionismo de un día.

### Nivel 3: Rezagadas (Norte, Cuyo, CABA)

Comparten el mismo síntoma: conectividad aérea recuperada o mejorada, pero turismo interno todavía por debajo de 2019 (Norte 76,2 %, CABA 84,7 %, Cuyo 86,2 %). El cuello de botella no está en el acceso al destino, sino en la demanda. Norte presenta la mayor brecha entre ocupación hotelera y turismo interno, cercana a los 19 puntos.

## De diagnóstico a acción: recomendaciones por región

| Grupo | Foco | Recomendación |
|---|---|---|
| Líderes | Consolidar y replicar | Documentar qué combinación de factores sostiene a Patagonia y Buenos Aires, para evaluar si algunos de esos mecanismos son transferibles a otras regiones. |
| Estables | Conversión | Córdoba y Litoral necesitan captar la demanda existente (formalización de alojamiento, incentivos a la oferta hotelera) más que promoción genérica. |
| Rezagadas | Demanda, no acceso | **Norte y Cuyo:** priorizar promoción en temporada baja y paquetes accesibles antes que nueva inversión en conectividad, que ya está sobrerecuperada. **CABA:** separar en la medición el turismo de ocio del tránsito y los negocios antes de diagnosticar la causa real de su bajo turismo interno. |

## Limitaciones y próximos pasos

El alojamiento no hotelero (alquileres temporarios, casas de familia, plataformas tipo Airbnb) no está capturado en los indicadores de ocupación hotelera utilizados. Esto pesa especialmente en Buenos Aires y Litoral, donde ya aparece una brecha entre turismo interno y viajeros hospedados.

El diagnóstico sigue siendo válido, pero la actividad turística real de esas regiones podría estar subestimada. Antes de diseñar políticas basadas solo en datos hoteleros, se recomienda relevar esta fuente adicional.

## Conclusión

La recuperación post-COVID fue heterogénea entre regiones y sectores. La distancia entre Patagonia (recuperación superior al 100 % en los tres indicadores) y regiones como Norte o Cuyo (turismo interno aún por debajo de niveles prepandemia) sugiere dinámicas estructurales que exceden el efecto pandemia.

Señales útiles para la gestión turística: regiones con alta conectividad y baja conversión hotelera, destinos con alta retención pero bajo volumen, y zonas con fuerte turismo interno y gasto susceptible de mejora.

## Fuentes de datos

**Hotelería**

- Hoteleria Covid Establecimientos
- Hoteleria Estadia Media Region
- Hoteleria Pernoctes Region
- Hoteleria Ocupacion Habitaciones
- Hoteleria Viajeros Region

**Turismo interno**

- Turismo Interno Turistas Region
- Turismo Interno Estadia Media Region
- Turismo Interno Gasto Total Region
- Turismo Interno Gasto Promedio Region
- Turismo Interno Pernoctaciones Region

## Estructura del repositorio

```
turismo-argentina/
├── README.md
├── dashboard/          archivo .pbix
├── imagenes/           capturas de cada hoja
├── docs/
│   ├── medidas-dax.md
│   └── diccionario-datos.md
└── data/               datos o muestra
```

## Herramientas

Power BI (modelado, medidas DAX, visualización).

## Equipo y rol

Trabajo grupal realizado en el bootcamp de Jupi Digital.

| Integrante | Hojas del dashboard | Datos y modelo | Medidas DAX |
|---|---|---|---|
| Manuel Martínez | Resumen Ejecutivo | FACT_Hoteleria (ocupación), DIM_Provincia_Coord, DIM_Provincia_Region | Latitud y longitud, diagnóstico |
| Lorena Eliana Tollo | Diagnóstico regional (brecha y oportunidades por región) | Limpieza de DIM_Categoria_Hotelera y FACT_Hoteleria_Demanda | Hotelería |
| Juan Ignacio Bosch | Turismo interno | Limpieza de FACT_TurismoInterno | Turismo interno |
| Leandro Sebastián Schonfeld | Diagnóstico regional (indicador de ranking regional, mapa regional de indicadores turísticos, recuperación por región y sector) | Limpieza de fuente de datos COVID | Indicador de ranking regional, indicador del mapa |

## Contacto

Manuel Martínez, Data Analyst
[LinkedIn](https://www.linkedin.com/in/manuelmartinez92)
