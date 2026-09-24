# proyecto1\_BI

Proyecto 1 de la clase de BI


\# Proyecto 1 — Business Intelligence

\## Análisis de "The Complete Journey" (dunnhumby)



\*\*Autores:\*\* Noel Sánchez, Hugo Mejía



\## Qué hicimos



Analizamos el dataset "The Complete Journey" de dunnhumby (2,500 hogares, dos años de

transacciones) para responder la pregunta del VP de Marketing: ¿dónde debería ir el

presupuesto de marketing del próximo año, y por qué?



Desarrollamos los 5 análisis obligatorios: concentración del gasto (curva tipo Pareto),

mix de categorías (ranking por venta y crecimiento, corrigiendo la rampa de

incorporación del panel en las primeras 15 semanas), el costo del descuento (desglose

por tipo, departamento, categoría y marca), efectividad de campañas (comparando

hogares con y sin campaña, antes y durante, controlando por sesgo de selección), y

cobertura demográfica (verificando qué tan representativa es la tabla de demografía

del resto de la base).



\*\*Recomendación final:\*\* Palanca C (Mecánica) — reasignar el presupuesto de descuento

en góndola desde categorías que pierden participación hacia las que ganan, y mantener

las campañas directas como herramienta de retención sin expandir su cobertura a más

hogares. El detalle completo, con la evidencia de cada uno de los tres análisis que la

sustentan, está en la última sección del notebook.



\## Cómo correrlo



1\. Clonar el repositorio:

&#x20;  ```

&#x20;  git clone https://github.com/xNoksS/proyecto1\_BI.git

&#x20;  cd proyecto1\_BI

&#x20;  ```

2\. Descargar los datos desde https://drive.google.com/file/d/1hDZZzZ\_F99-ggsV0Aa4K90HDYIfE7mzN/view

&#x20;  y colocar los 7 archivos CSV en la carpeta `data/` (no se incluyen en el

&#x20;  repositorio; ver `.gitignore`)

3\. Instalar el entorno:

&#x20;  ```

&#x20;  uv sync

&#x20;  ```

4\. Abrir el notebook:

&#x20;  ```

&#x20;  uv run jupyter notebook proyecto1\_sanchez\_mejia.ipynb

&#x20;  ```

5\. Ejecutar todo desde cero antes de revisar: Kernel → Restart Kernel and Run All Cells



Nota: `causal\_data.csv` (663 MB) no se utiliza en este proyecto — ninguno de los 5

análisis obligatorios lo requiere, y decidimos no incluirlo dado su tamaño.



\## División del trabajo



\- \*\*Noel Sánchez:\*\* configuración del entorno (uv, .gitignore, estructura del repo),

&#x20; perfilado de calidad de datos (Etapa 1) de las 7 tablas, Análisis 4 (campañas) y

&#x20; Análisis 5 (cobertura demográfica), con sus respectivos gráficos.

\- \*\*Hugo Mejía:\*\* construcción de la tabla analítica y merges (Etapa 2), Análisis 1

&#x20; (concentración del gasto), Análisis 2 (mix de categorías, incluyendo la corrección

&#x20; por rampa de incorporación del panel), Análisis 3 (costo del descuento) y Análisis

&#x20; 2×3 (cruce descuento-participación), con sus respectivos gráficos, y la elaboración

&#x20; de la presentación final.

\- Ambos: revisión cruzada del código del otro, redacción conjunta de la

&#x20; recomendación final.



\## Uso de IA



Ambos autores usamos Claude (Anthropic) como herramienta de aprendizaje durante el

proyecto, principalmente en modo de guía socrática: pedimos explícitamente que la IA

hiciera preguntas y diera pistas en vez de escribir las conclusiones o el código de

forma directa, y la usamos para pedir retroalimentación sobre el razonamiento y las

conclusiones ya redactadas, antes de darlas por definitivas. El código final y las

decisiones analíticas fueron escritos, ejecutados y verificados por nosotros.



\*\*Ejemplos representativos de cómo se usó (prompts reales de la conversación):\*\*



\- \*"En `campaign\_table`, la columna `CAMPAIGN` se repetirá miles de veces... ¿qué se

&#x20; necesita para identificar una fila de forma única?"\* → Verificar el razonamiento

&#x20; sobre cardinalidad de tablas (m:1, 1:m) antes de escribir merges, evitando el

&#x20; error de inflar filas al cruzar `coupon` (que cubre múltiples productos por

&#x20; cupón).



\- \*"¿Por qué crees que pasa esto?"\* (ante una discrepancia entre el `describe()` de

&#x20; pandas y el enunciado sobre valores negativos en `transaction\_data`) → Aprender a

&#x20; investigar contradicciones entre la documentación y los datos reales con código,

&#x20; en vez de asumir cuál fuente tenía razón.



\- \*"¿Qué error cometería si...?"\* (sobre comparar hogares con y sin campaña sin

&#x20; controlar por comportamiento previo) → Entender el concepto de sesgo de selección

&#x20; y causalidad inversa, aplicado tanto al Análisis 2×3 (descuento vs. participación)

&#x20; como al Análisis 4 (campañas).



\- Retroalimentación sobre conclusiones ya redactadas: la IA señaló varias veces

&#x20; cuando una conclusión afirmaba más de lo que el código realmente probaba (por

&#x20; ejemplo, llamar "financieramente inviable" a algo sin datos de costos, o dar por

&#x20; sentada una causalidad que la correlación no puede probar, o introducir el

&#x20; concepto de "Marca Propia" en la recomendación final sin respaldo en el análisis),

&#x20; lo que llevó a reformular esas conclusiones con mayor precisión.



\- Depuración de errores técnicos: identificar por qué el símbolo `$` rompía el

&#x20; renderizado de Markdown en Jupyter (interpretado como LaTeX), y por qué

&#x20; `select\_dtypes(include=\["object"])` generaba un warning de deprecación en pandas.



Herramienta: Claude Sonnet 5 (Anthropic), vía claude.ai.

```

## Lo que no pudimos responder

**1. Dirección de la causalidad entre descuento y participación.** La correlación
negativa (r = -0.383) es consistente tanto con "el descuento causa la caída" como con
"la caída causa el descuento" (respuesta defensiva). Con datos observacionales, sin
variación exógena en precios, ambas explicaciones son indistinguibles.
*Necesitaríamos:* un experimento controlado (A/B test de nivel de descuento por
tienda o región) para aislar el efecto causal.

**2. Si las campañas funcionarían en hogares de bajo gasto.** El lift medido
(+USD 0.14) proviene exclusivamente de hogares que ya eran 28 veces más valiosos
antes de recibir campaña — nunca se ha probado el estímulo en el segmento de bajo
gasto. *Necesitaríamos:* un piloto con asignación aleatoria de campaña en hogares de
bajo gasto histórico.

**3. El rol del display y el mailer en tienda.** No se utilizó `causal_data.csv`
(36M de filas, no obligatoria para los 5 análisis) por su tamaño y por estar fuera
del alcance definido. No sabemos si la exhibición física en tienda explica parte de
por qué ciertas categorías descontadas no logran crecer. *Necesitaríamos:* cruzar
`causal_data.csv` contra las categorías identificadas en el Análisis 2×3.

**4. Estacionalidad real del negocio.** El dataset solo provee `DAY` (1-711) y
`WEEK_NO` (1-102), sin año ni mes calendario, por anonimización. No podemos separar
tendencia genuina de estacionalidad (por ejemplo, si semanas de alto gasto coinciden
con fiestas reales). *Necesitaríamos:* el mapeo de `DAY` a fecha calendario real,
que dunnhumby no provee en este dataset.


