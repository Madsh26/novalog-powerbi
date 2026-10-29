# NovaLog: ¿qué operador cumple, a qué costo y por qué se retrasa?

> Un modelo en estrella de **50.000 entregas** y un tablero de Power BI para vigilar el cumplimiento, el costo y los tiempos de una cadena de entrega, diseñados de principio a fin.

![Encabezado del tablero NovaLog](assets/banner.jpg)

## En una mirada

| | |
|---|---|
| **Pregunta de negocio** | ¿Qué operador cumple mejor, cuánto cuesta cada nivel de servicio y qué factores explican los retrasos? |
| **Qué construí** | Dataset sintético reproducible, modelo dimensional, relaciones, tablero interactivo de una página y diccionario de datos |
| **Herramientas** | Power BI, DAX, modelado dimensional, CSV |
| **Alcance** | 50.000 pedidos · 6.000 clientes · 300 productos · 6 operadores · 12 ciudades · 2024-2026 |

## Por qué existe este proyecto

La logística de última milla se gestiona con pocos indicadores bien construidos: cumplimiento a tiempo, costo por envío, tiempo real frente al prometido e incidencias. Quise practicar justo esa cadena completa, desde el diseño del modelo hasta el tablero, con un caso donde cada pregunta de negocio se pudiera medir y comprobar.

Usar datos sintéticos fue una decisión de método: me permitió **fijar de antemano los patrones** (temporada alta, operadores con desempeño distinto, servicios urgentes más costosos) y verificar que el modelo y las medidas los recuperan. Si el tablero no los mostrara, el error estaría en mi modelo, no en los datos.

## Metodología

1. **Definir el grano.** Una fila de `Fact_Entregas` es un pedido asociado a un SKU principal. Fijarlo primero evita el error más común del modelado: mezclar niveles de detalle.
2. **Diseñar los datos.** Generé el dataset con apoyo de IA y semilla reproducible (`20261003`), declarando los patrones a recuperar: más demanda en noviembre y diciembre, diferencias de desempeño entre transportadoras, costos ligados a peso, distancia, servicio e intentos de entrega, y mayor presión logística en temporada alta.
3. **Modelar en estrella.** Una tabla de hechos con claves sustitutas y nueve dimensiones, con relaciones 1:* documentadas en `datos/Modelo_Relaciones.csv`.
4. **Resolver el rol de las fechas.** La fecha del pedido es la relación activa con `Dim_Fecha`. La fecha de entrega queda como relación inactiva, que se activa en las medidas con `USERELATIONSHIP`. Así un solo calendario sirve para dos preguntas distintas.
5. **Separar lo vendido de lo realizado.** `IngresoNeto` es cero en operaciones no efectivas (devueltas, canceladas o fallidas) y `Dim_EstadoPedido` incluye la marca `EsEntregaEfectiva`, para que los ingresos y el cumplimiento no se mezclen.
6. **Validar antes de graficar.** `datos/Resumen_Validacion.csv` contrasta conteos de filas, claves y totales del modelo.
7. **Contar la historia en una página.** Cada visual responde una pregunta concreta (ver abajo).

## El tablero

Una página, diez elementos: tres segmentadores, una tarjeta de indicador, cuatro visuales analíticos, una matriz y el encabezado.

| Pregunta | Visual |
|---|---|
| ¿Qué factores se asocian a los retrasos? | Árbol de descomposición |
| ¿Quién cumple y en qué nivel de servicio? | Matriz de cumplimiento por operador y servicio |
| ¿Cómo se relacionan distancia, costo y volumen por ciudad? | Dispersión por ciudad |
| ¿Dónde se pierde el proceso de entrega? | Embudo de efectividad |
| ¿Qué tan estable es cada operador en el tiempo? | Líneas de cumplimiento por operador |

## Lo que muestran los datos

| Operador | Pedidos | A tiempo | Costo logístico promedio |
|---|---:|---:|---:|
| RápidoYa | 7.554 | 82,9 % | 14.734 |
| NovaFleet (flota propia) | 11.729 | 80,1 % | 12.350 |
| Andina Express | 9.097 | 75,8 % | 13.747 |
| Ruta360 | 8.581 | 64,5 % | 13.025 |
| EnvíoPlus | 5.517 | 62,3 % | 13.116 |
| LogiCol | 7.522 | 53,1 % | 12.625 |

- **El costo no compra cumplimiento.** LogiCol cumple menos y no es el más barato de forma clara; RápidoYa cumple más y es el más caro.
- **Pagar por urgencia no mejora el cumplimiento.** El servicio Mismo día cuesta en promedio un 56 % más que el Estándar (18.271 frente a 11.747), pero el cumplimiento ronda el 71 % en los cuatro servicios.
- **La temporada alta aprieta la operación.** Noviembre y diciembre concentran el 24,7 % de los pedidos del periodo.
- **Resultado global:** 71,03 % de los pedidos llegan a tiempo, 22,4 % con retraso y 6,6 % no se concretan (devueltos, cancelados o fallidos). El tiempo real promedio es de 41,5 horas.

## Modelo en estrella

| Dimensión | Filas | Contenido |
|---|---:|---|
| Dim_Fecha | 1.096 | Calendario 2024-2026, trimestre y temporada |
| Dim_Cliente | 6.000 | Clientes |
| Dim_Producto | 300 | Productos (SKU) |
| Dim_Geografia | 12 | Ciudades, regiones y zonas logísticas |
| Dim_Transportadora | 6 | Operador, tipo (flota propia o tercero) y clasificación |
| Dim_Canal | 5 | Digital, social commerce y B2B |
| Dim_TipoServicio | 4 | Estándar, Express, Mismo día y Programado |
| Dim_EstadoPedido | 5 | A tiempo, con retraso, devuelto, cancelado, fallido |
| Dim_Incidencia | 9 | Tipo y origen de la incidencia |

## Transparencia sobre los datos

Los datos son **sintéticos**: los generé con apoyo de IA para este ejercicio y no provienen de ninguna empresa. Los patrones se diseñaron de forma intencional, así que las cifras demuestran que el modelo y el tablero los recuperan; no son hallazgos sobre una operación real. El valor del proyecto está en el diseño del modelo y en la construcción de los indicadores.

## Contenido del repositorio

```text
datos/      Hechos, 9 dimensiones, diccionario, relaciones y validación (CSV)
powerbi/    NOVALOG.pbix y tema de colores (JSON)
assets/     Encabezado del tablero
```

## Cómo abrirlo

1. Abre `powerbi/NOVALOG.pbix` en Power BI Desktop.
2. Si pide la ruta de los datos, apunta a la carpeta `datos/`.
3. Para reconstruir el modelo, importa los CSV y crea las relaciones de `datos/Modelo_Relaciones.csv`.

## Autor

**Mauricio Serrano** · Ingeniería Industrial, Universidad del Magdalena · [Portafolio](https://madsh26.github.io) · [LinkedIn](https://www.linkedin.com/in/mauricio-david-serrano-hernandez/)
