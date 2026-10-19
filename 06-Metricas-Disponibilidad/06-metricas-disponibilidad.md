---
marp: true
math: katex
paginate: true
size: 16:9
style: |
  section { font-size: 27px; line-height: 1.35; }
  section.lead { text-align: center; }
  section.lead h1 { font-size: 2.1em; }
  h2, h3 { color: #12394f; }
  table { font-size: 0.78em; }
  .small { font-size: 0.82em; }
  section.compact { font-size: 24px; }
  .columns { display: grid; grid-template-columns: 1fr 1fr; gap: 32px; align-items: start; }
  .callout { background: #eef6fb; border-left: 6px solid #2f6f9f; border-radius: 6px; padding: 0.7em 0.9em; }
  .bridge { background: #f7f8fa; border-left: 6px solid #6b7280; border-radius: 6px; padding: 0.65em 0.9em; }
  .warn { background: #fff4df; border-left: 6px solid #b7791f; border-radius: 6px; padding: 0.65em 0.9em; }
  .example { background: #eef8f1; border-left: 6px solid #2f855a; border-radius: 6px; padding: 0.65em 0.9em; }
  img.diagram { display: block; margin: 0 auto; max-height: 285px; max-width: 100%; }
  img.diagram-small { display: block; margin: 0 auto; max-width: 100%; max-height: 220px; }
---
<!-- _class: lead -->

# TEL211
## Disponibilidad y Rendimiento de Sistemas TIC
### Métricas de confiabilidad, mantenibilidad y disponibilidad

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Por qué hacen falta distintas medidas

En servicios y sistemas TIC, las interrupciones pueden ser pocas y largas, o frecuentes y breves. También varía el tiempo que toma recuperar el servicio.

Una sola cifra no permite **distinguir** todas estas experiencias ni orientar por sí sola el diseño y el soporte.

---

## Métricas que veremos

| Nombre | Sigla o símbolo | Qué mide |
|---|---|---|
| Confiabilidad | $R(t)$ | Probabilidad de completar una misión sin fallar |
| Tiempo medio hasta la falla | MTTF | Vida media hasta la primera falla |
| Tiempo medio entre fallas | MTBF | Tiempo operativo medio entre fallas recurrentes |
| Tiempo medio de reparación o restauración | MTTR | Duración media del trabajo activo o del retorno completo |
| Tasa de ocurrencia de fallas | $\mathrm{ROCOF}(t)$ | Frecuencia de fallas recurrentes a lo largo del tiempo |
| Disponibilidad | $A(t)$, $A_{\mathrm{obs}}$, $\overline A(T)$, $A_\infty$, $A_i$, $A_a$, $A_o$ | Probabilidad o fracción de tiempo con servicio según el instante, la ventana y su frontera |
| Inactividad esperada o presupuesto | $E[T_D(T)]$, $T_{D,\max}$ | Horas esperadas sin servicio o límite acordado en una ventana |

Estas métricas cubren preguntas frecuentes, pero no agotan las que pueden usarse en confiabilidad, mantenibilidad y disponibilidad.

---

## Confiabilidad: $R(t)$

Para el tiempo de vida $T$, la confiabilidad mide la probabilidad de completar una misión de duración $t$ sin fallar. $F(t)$ es la probabilidad acumulada de falla:

$$
R(t)=P(T>t)=1-F(t).
$$

Retoma la distribución del tiempo de vida usada para estudiar unidades no reparables. Se utiliza cuando importa completar una misión sin interrupción.

Ejemplo: si $\lambda=2\times10^{-5}\ \mathrm h^{-1}$ y la misión dura $10\,000\ \mathrm h$, entonces $R(t)=e^{-0.2}\approx0.819$.

---

## MTTF: tiempo medio hasta la primera falla

MTTF abrevia *Mean Time To Failure*. Retoma la variable de vida $T$ y la confiabilidad $R(t)$. Su valor medio es el área bajo la curva de confiabilidad:

$$
\mathrm{MTTF}=E[T]=\int_0^\infty R(t)\,dt.
$$

Para $n$ vidas completas y comparables, se estima con $\widehat{\mathrm{MTTF}}=\frac{1}{n}\sum_{i=1}^{n}t_i$. Se usa para resumir la vida de unidades no reparables. No es una duración garantizada.

Ejemplo de sistema: si $R_{\mathrm{s}}(t)=e^{-\lambda_{\mathrm{s}}t}$ y $\lambda_{\mathrm{s}}=0.002\ \mathrm h^{-1}$, entonces $\mathrm{MTTF}_{\mathrm{s}}=1/\lambda_{\mathrm{s}}=500\ \mathrm h$.

---

## Ejemplo: ciclos de operación y restauración

El registro contiene tres ciclos completos. Solo se cuentan fallas que interrumpen el servicio definido.

| Ciclo | Operación $U_i$ | Restauración total $D_i$ | Trabajo correctivo activo |
|---|---:|---:|---:|
| 1 | $480$ h | $4$ h | $2$ h |
| 2 | $520$ h | $6$ h | $2$ h |
| 3 | $500$ h | $5$ h | $2$ h |

Cada ciclo contiene operación hasta la falla y restauración hasta el retorno al servicio. La ventana empieza después de una restauración.

---

## Leer el registro como una línea de tiempo

<img class="diagram" src="images/ciclo-operacion.png" alt="Tres ciclos de operación y restauración de un servicio">

Cada falla cierra un intervalo operativo. El tiempo hasta volver a operar es la restauración.

---

## MTBF: tiempo medio entre fallas

MTBF abrevia *Mean Time Between Failures* y representa el tiempo operativo medio entre fallas. Usaremos $B=E[U_i]=\mathrm{MTBF}$. La letra $B$ recuerda *between*, entre fallas.

Cada intervalo $U_i$ va desde el retorno al servicio hasta la siguiente falla. No incluye la restauración. Un ciclo completo dura $U_i+D_i$. Para $n$ intervalos completos, $T_U$ es el tiempo operativo total:

$$
\widehat{\mathrm{MTBF}}=\frac{T_U}{n}.
$$

Ejemplo: $T_U=2000\ \mathrm h$ en $n=4$ intervalos da $\widehat{\mathrm{MTBF}}=500\ \mathrm h$.

---

## MTTR: tiempo medio de reparación o restauración

MTTR abrevia *Mean Time To Repair*. Se debe declarar si mide solo trabajo activo o toda la restauración, desde la falla hasta el retorno al servicio.

Para $n$ restauraciones completas, $T_D$ es el tiempo total desde la falla hasta el retorno al servicio. La media observada es:

$$
\widehat{\mathrm{MTTR}}_{\mathrm{rest}}=\frac{T_D}{n}.
$$

Se usa para evaluar el proceso de recuperación. La espera por personal, repuestos, autorización y pruebas puede formar parte de $T_D$.

Ejemplo: $T_D=12\ \mathrm h$ para $n=4$ restauraciones da $\widehat{\mathrm{MTTR}}_{\mathrm{rest}}=3\ \mathrm h$.

---

## ROCOF: tasa de ocurrencia de fallas

ROCOF abrevia *Rate of Occurrence of Failures*. Si $N(t)$ cuenta fallas recurrentes y $M(t)=E[N(t)]$, la tasa general de ocurrencia es:

$$
\mathrm{ROCOF}(t)=\frac{dM(t)}{dt}.
$$

En un registro de duración $T$, la tasa media observada es:

$$
\widehat{\mathrm{ROCOF}}(T)=\frac{N(T)}{T}.
$$

Es el promedio realizado durante la ventana, no la tasa instantánea $\mathrm{ROCOF}(t)$. Estima una intensidad constante si el proceso se modela con tasa constante. Se debe indicar si $T$ es tiempo calendario u operativo.

Ejemplo: $3$ fallas en $1515\ \mathrm h$ calendario dan $\widehat{\mathrm{ROCOF}}_{\mathrm{cal}}(T)=3/1515\approx0.001980\ \mathrm h^{-1}$.

---

## Disponibilidad: tiempo con servicio

La fracción operativa observada en una ventana es:

$$
A_{\mathrm{obs}}=\frac{T_U}{T_U+T_D}.
$$

En cada ciclo, $U_i$ es el tiempo operativo y $D_i$ el tiempo de restauración. Si ambas duraciones tienen medias finitas, con $B=E[U_i]$ y $D=E[D_i]$, la disponibilidad de largo plazo es:

$$
A_\infty=\frac{E[U_i]}{E[U_i]+E[D_i]}=\frac{B}{B+D}.
$$

La primera resume un registro. La segunda describe el promedio de largo plazo del servicio reparable. La razón por ciclos no exige duraciones exponenciales.

Ejemplo: $10$ min sin servicio en una ventana de $720\ \mathrm h$ dan $A_{\mathrm{obs}}=(720-1/6)/720\approx0.99977$.

---

## De los tiempos al promedio

El registro entrega los tiempos acumulados:

$$
T_U=480+520+500=1500\ \mathrm h,
\qquad T_D=4+6+5=15\ \mathrm h.
$$

Con $n=3$ fallas e intervalos completos:

$$
\widehat{\mathrm{MTBF}}=\frac{1500}{3}=500\ \mathrm h,
\qquad
\widehat{\mathrm{MTTR}}_{\mathrm{rest}}=\frac{15}{3}=5\ \mathrm h.
$$

El trabajo activo medio es $2$ h. Las $3$ h restantes de cada restauración son esperas u otras actividades necesarias para recuperar el servicio.

---

## Contar fallas recurrentes

Sea $N(t)$ el número de interrupciones del servicio observadas hasta el tiempo $t$.

<img class="diagram" src="images/conteo-fallas.png" alt="Conteo escalonado de fallas que aumenta con cada interrupción">

En este ejemplo, las fallas ocurren en las horas $480$, $1004$ y $1510$. Al cerrar la ventana en $T=1515$ h, se tiene $N(T)=3$.

---

## El denominador cambia la interpretación

La tasa media observada en una ventana calendario es:

$$
\widehat{\mathrm{ROCOF}}_{\mathrm{cal}}(T)=\frac{N(T)}{T}.
$$

En este ejemplo pueden calcularse dos cocientes:

| Tiempo usado | Resultado | Interpretación |
|---|---:|---|
| Calendario | $\widehat{\mathrm{ROCOF}}_{\mathrm{cal}}(T)=3/1515\approx0.001980\ \mathrm h^{-1}$ | fallas por hora de observación |
| Operativo | $\widehat{\mathrm{ROCOF}}_{\mathrm{op}}=3/1500=0.002\ \mathrm h^{-1}$ | fallas por hora expuesta a operar |

El segundo cociente estima $\lambda$ solo si se adopta un modelo exponencial para los tiempos operativos.

---

## Conexión con el modelo de dos estados

Con operación y restauración exponenciales, una CTMC (*Continuous Time Markov Chain*), o cadena de Markov en tiempo continuo, representa el ciclo del servicio:

$$
B=\frac{1}{\lambda},
\qquad D=\frac{1}{\mu},
\qquad
A_\infty=\frac{\mu}{\lambda+\mu}=\frac{B}{B+D}.
$$

En régimen estacionario, el ROCOF calendario pondera la tasa de falla por la fracción del tiempo operativo:

$$
\mathrm{ROCOF}_\infty=\lambda A_\infty=\frac{1}{B+D}.
$$

$\lambda$ es una tasa condicionada a operar. Como $B=\mathrm{MTBF}$ y $D=\mathrm{MTTR}_{\mathrm{rest}}$, también $\mathrm{ROCOF}_\infty=1/(\mathrm{MTBF}+\mathrm{MTTR}_{\mathrm{rest}})$. Por eso no basta con invertir el MTBF.


---

## Restaurar incluye más que reparar

<img class="diagram" src="images/availability-flow.png" alt="Proceso desde falla hasta retorno al servicio con esperas, trabajo correctivo y pruebas">

El usuario experimenta inactividad desde la falla hasta el retorno al servicio. Durante una parte de ese intervalo puede no haber trabajo correctivo activo.

En este ejemplo, las $2$ horas de trabajo correctivo activo incluyen $1.5$ horas de reparación y $0.5$ horas de pruebas.

---

## Calcular la restauración efectiva

En la tercera restauración del ejemplo, las duraciones son $0.5$ h de espera administrativa, $2.5$ h de repuesto, $1.5$ h de disponibilidad de técnico, $1.5$ h de trabajo activo y $0.5$ h de pruebas.

Como repuesto y técnico se esperan en paralelo:

$$
D_3=0.5+\max(2.5,1.5)+1.5+0.5=5\ \mathrm h.
$$

Primero se calcula la duración efectiva de cada restauración. Después se promedian las restauraciones. En general, $E[\max(X,Y)]$ no coincide con $\max(E[X],E[Y])$.

---

## Ejemplo: disponibilidad observada

La ventana tiene $1500+15=1515$ h. Por tanto:

$$
A_{\mathrm{obs}}=\frac{1500}{1515}=\frac{100}{101}\approx0.990099.
$$

El servicio estuvo operativo cerca del $99.010\%$ de la ventana. Las $15$ h inactivas incluyen trabajo activo y esperas.

<div class="callout">

Este resultado resume el registro. Usarlo como una predicción requiere que las condiciones de la ventana sean representativas de la operación futura.

</div>

---

## El alcance define el tipo de disponibilidad

La disponibilidad inherente (*inherent*), lograda (*achieved*) y operacional (*operational*) se distinguen por las categorías de inactividad que incluyen.

<img class="diagram" src="images/tipos-disponibilidad.png" alt="Categorías de tiempo registradas y componentes incluidos en las disponibilidades inherente, lograda y operacional">

Las franjas coloreadas indican los tiempos incluidos y las grises, los excluidos.

---

## Ejemplo: tres tipos de disponibilidad

Con los tiempos del diagrama, las disponibilidades resultan:

$$
\begin{aligned}
A_i&=\frac{T_U}{T_U+T_C}=\frac{1000}{1005}=\frac{200}{201}\approx0.9950,\\
A_a&=\frac{T_U}{T_U+T_C+T_P}=\frac{1000}{1010}=\frac{100}{101}\approx0.9901,\\
A_o&=\frac{T_U}{T_U+T_C+T_P+T_E}=\frac{1000}{1020}=\frac{50}{51}\approx0.9804.
\end{aligned}
$$

El servicio y la ventana son los mismos. La cifra baja al incluir más causas de inactividad.

---

## Instante, ventana y régimen

| Magnitud | Significado |
|---|---|
| $A(t)$ | probabilidad de estar disponible en el instante $t$ |
| $A_{\mathrm{obs}}$ | fracción operativa observada en una ventana |
| $\overline A(T)$ | fracción operativa esperada durante una ventana |
| $A_\infty$ | disponibilidad de régimen, si existe |

$$
\overline A(T)=\frac{1}{T}\int_0^T A(t)\,dt.
$$

El valor al final de una ventana y su promedio pueden ser distintos, en especial después de una caída reciente.

---

## Del estado a horas esperadas

Si $A(t)$ es la probabilidad de operar, la inactividad esperada durante $[0,T]$ es:

$$
E[T_D(T)]=\int_0^T\left(1-A(t)\right)\,dt.
$$

Si la ventana inicia en régimen estacionario:

$$
E[T_D(T)]=(1-A_\infty)T.
$$

Esta expresión es un caso de recompensa acumulada, donde el estado sin servicio recibe recompensa uno y el estado operativo recibe recompensa cero.

---

## Traducir disponibilidad a tiempo

Para una ventana anual de $8760$ h:

| Disponibilidad | Inactividad media esperada |
|---|---:|
| $0.99$ | $87.6$ h |
| $0.999$ | $8.76$ h |
| $0.9999$ | $0.876$ h |

Una inactividad media esperada no es un máximo garantizado para cada año. Si importa el estado inicial, se utiliza la disponibilidad transiente y su promedio en la ventana.

---

## Acuerdo de nivel de servicio (SLA)

SLA abrevia *Service Level Agreement*, acuerdo de nivel de servicio. Debe establecer la función requerida, la ventana contabilizada y las reglas de registro de interrupciones.

| Aspecto | Pregunta que debe quedar resuelta |
|---|---|
| Frontera | ¿qué capacidad, conectividad o estados cuentan como servicio? |
| Ventana | ¿qué días y horarios se contabilizan? |
| Interrupción | ¿cuándo comienza y cuándo termina? |
| Exclusiones | ¿qué mantenimientos programados se descuentan, si corresponde? |

Sin estas definiciones, dos cifras de disponibilidad no son comparables.

---

## Un presupuesto de inactividad

El SLA fija una disponibilidad mínima $a_{\min}$ para una ventana de duración $T$. El presupuesto máximo de inactividad es:

$$
T_{D,\max}=(1-a_{\min})T.
$$

En $30$ días continuos, $T=720$ h. Un objetivo de $0.999$ permite:

$$
T_D\leq0.001(720\ \mathrm h)=0.72\ \mathrm h=43.2\ \mathrm{min}.
$$

El cumplimiento se verifica con las interrupciones registradas. Un modelo que predice menos de $43.2$ minutos en promedio no garantiza cada ventana individual.

---

## La misma disponibilidad puede ocultar experiencias distintas

| Sistema | $B$ | $D$ | $A_\infty$ | Frecuencia calendario |
|---|---:|---:|---:|---:|
| A | $900$ h | $100$ h | $0.9$ | $1/1000\ \mathrm h^{-1}$ |
| B | $450$ h | $50$ h | $0.9$ | $1/500\ \mathrm h^{-1}$ |

El sistema A interrumpe menos veces, pero sus recuperaciones son más largas. El sistema B requiere más intervenciones y recupera antes.

La decisión depende de cuánto afectan al servicio la frecuencia, la duración y el momento de las interrupciones.

---

## Comparar alternativas de mejora

El ejemplo se usa como referencia con $B=500$ h y $D=5$ h.

| Alternativa | $B$ | $D$ | $A_\infty$ | Inactividad anual media |
|---|---:|---:|---:|---:|
| Situación actual | $500$ h | $5$ h | $0.990099$ | $86.73$ h |
| Mejor diseño | $750$ h | $5$ h | $0.993377$ | $58.01$ h |
| Mejor soporte | $500$ h | $2$ h | $0.996016$ | $34.90$ h |

El cociente $B/(B+D)$ ayuda a comparar opciones, pero no reemplaza el análisis de costos ni la evidencia de que el cambio es realizable.

---

## Ejercicio: otro registro de operación

Un servicio acumula $1800$ h operativas y $6$ h inactivas en una ventana con $9$ ciclos completos.

1. Estime MTBF, MTTR de restauración y disponibilidad operacional.
2. Calcule la frecuencia de fallas por hora calendario y por hora operativa.
3. Proyecte la inactividad anual si el registro es representativo.
4. Indique una limitación de esa proyección.

No se separan los tiempos de reparación activa, por lo que el registro solo permite evaluar restauración completa.

---

## Ejercicio: solución

$$
\widehat{\mathrm{MTBF}}=\frac{1800}{9}=200\ \mathrm h,
\qquad
\widehat{\mathrm{MTTR}}_{\mathrm{rest}}=\frac{6}{9}=\frac{2}{3}\ \mathrm h.
$$

$$
A_{\mathrm{obs}}=\frac{1800}{1806}=\frac{300}{301}\approx0.996678.
$$

$$
\widehat{\mathrm{ROCOF}}_{\mathrm{cal}}=\frac{9}{1806}\approx0.004983\ \mathrm h^{-1},
\qquad
\widehat{\mathrm{ROCOF}}_{\mathrm{op}}=\frac{9}{1800}=0.005\ \mathrm h^{-1}.
$$

La proyección anual es $8760/301\approx29.10$ h inactivas. La ventana no demuestra que la frecuencia futura sea constante ni describe la variabilidad de las interrupciones.

---

## Del tiempo de servicio al tiempo de espera.

En esta unidad, los registros de falla, operación y restauración se tradujeron en métricas con límites y supuestos explícitos. A continuación, analizaremos cómo la llegada de solicitudes y la capacidad de atención determinan la espera, la ocupación de los servidores y el bloqueo.

---

## Referencias

1. L. A. Lizama, *Métricas de Confiabilidad y Disponibilidad*, material histórico TEL211, Universidad Técnica Federico Santa María.
2. J. M. Martínez, *Continuous Time Markov Chains*, material histórico TEL211, Universidad Técnica Federico Santa María. Métricas por estado y recompensas acumuladas.
3. K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017. Secciones 3.5 y 9.5.
4. K. S. Trivedi, *Probability and Statistics with Reliability, Queuing, and Computer Science Applications*, Wiley, 2002. Métricas de sistemas reparables.
