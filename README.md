# NovaLog: desempeño logístico y comercial en Power BI

Tablero de Power BI sobre un modelo en estrella de **50.000 entregas** (2024-2026) de una comercializadora ficticia, NovaLog Commerce S.A.S. Responde preguntas de operación logística: ¿qué operador cumple mejor?, ¿cuánto cuesta cada nivel de servicio?, ¿cómo cambia la presión operativa en temporada alta?

![Banner de NovaLog](assets/banner.jpg)

## Propósito

Diseñar de principio a fin un modelo dimensional (tabla de hechos, dimensiones, relaciones y medidas DAX) y mostrar cómo se vigila el desempeño de una cadena de entrega con indicadores (KPI) comparables entre operadores, servicios, canales y ciudades.

## Motivación

La logística de última milla se decide con pocos indicadores bien construidos: cumplimiento a tiempo, costo por envío, tiempo real frente al prometido e incidencias. Quise practicar justo esa cadena completa, desde el diseño del modelo hasta el tablero, con un caso donde cada pregunta de negocio se pudiera medir. Usar datos sintéticos permitió fijar de antemano patrones realistas (temporada alta, operadores con desempeño distinto, servicios urgentes más costosos) y comprobar que el modelo y las medidas los recuperan.

## Sobre los datos

- **Son sintéticos.** Los generé con apoyo de IA para este ejercicio, con semilla reproducible `20261003`. No provienen de ninguna empresa real, y las cifras no describen ninguna operación real.
- Los patrones (por ejemplo, más demanda en noviembre y diciembre, o operadores con distinto cumplimiento) se incluyeron de forma intencional. Los resultados muestran que el modelo los recupera, no hallazgos del mundo real.

## Modelo en estrella

Una tabla de hechos (`Fact_Entregas`, 1 fila por pedido) y 9 dimensiones:

| Dimensión | Filas | Contenido |
|---|---:|---|
| Dim_Fecha | 1.096 | Calendario 2024-2026, temporada, trimestre |
| Dim_Cliente | 6.000 | Clientes |
| Dim_Producto | 300 | Productos (SKU) |
| Dim_Geografia | 12 | Ciudades, regiones y zonas logísticas |
| Dim_Transportadora | 6 | Operador, tipo y clasificación |
| Dim_Canal | 5 | Canal de venta (digital, social commerce, B2B) |
| Dim_TipoServicio | 4 | Estándar, Express, Mismo día, Programado |
| Dim_EstadoPedido | 5 | A tiempo, con retraso, devuelto, cancelado, fallido |
| Dim_Incidencia | 9 | Tipo y origen de la incidencia |

La relación activa con fecha usa `FechaPedidoKey`. La de `FechaEntregaKey` queda inactiva y se activa en las medidas con `USERELATIONSHIP`. Las relaciones completas están en `datos/Modelo_Relaciones.csv` y el diccionario en `datos/Diccionario_Datos.csv`.

## Resultados de validación del dataset

| Indicador | Valor |
|---|---:|
| Pedidos | 50.000 |
| Entregados a tiempo | 35.515 (71,03 %) |
| Entregados con retraso | 11.192 |
| Devueltos, cancelados o fallidos | 3.293 |
| Tiempo real promedio | 41,54 horas |

## Lo que muestran los datos

- **Cumplimiento por operador:** va del 53,1 % (LogiCol) al 82,9 % (RápidoYa). NovaFleet, la flota propia, queda en 80,1 %.
- **Costo por servicio:** el costo logístico promedio de Mismo día (18.271) supera en un 56 % al de Estándar (11.747), sin que el cumplimiento a tiempo mejore: ronda el 71 % en los cuatro servicios.
- **Estacionalidad:** noviembre y diciembre concentran el 24,7 % de los pedidos del periodo.

## Contenido del repositorio

```text
datos/      Tabla de hechos, 9 dimensiones, diccionario, relaciones y validación (CSV)
powerbi/    NOVALOG.pbix y tema de colores (JSON)
assets/     Imagen del encabezado
```

## Cómo abrirlo

1. Abre `powerbi/NOVALOG.pbix` en Power BI Desktop.
2. Si Power BI pide la ruta de los datos, apunta a la carpeta `datos/`.
3. Para recrear el modelo desde cero, importa los CSV y crea las relaciones de `datos/Modelo_Relaciones.csv`.

## Límites

- Datos ficticios: no sirven para sacar conclusiones sobre operadores o servicios reales.
- Los patrones están diseñados, así que el ejercicio demuestra diseño de modelo y construcción de indicadores, no descubrimiento estadístico.
- Un solo SKU principal por pedido.

## Autor

Mauricio Serrano · Ingeniería Industrial, Universidad del Magdalena · [Portafolio](https://madsh26.github.io) · [LinkedIn](https://www.linkedin.com/in/mauricio-david-serrano-hernandez/)
