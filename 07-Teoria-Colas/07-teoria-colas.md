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
## Disponibilidad y Rendimiento de Sistemas de Tecnologías de Información y Comunicaciones (TIC)
### Teoría de filas: de la carga al tiempo de respuesta

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Un servicio puede estar disponible y responder tarde

En hora punta llegan $18\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$ a una plataforma de reservas. Un servidor tarda, en promedio, $2.5\ \mathrm s$ en atender cada solicitud.

<img class="diagram-small" src="images/ejemplo-reservas.png" alt="Las solicitudes llegan a una fila para un servidor de reservas. Llegan 18 por minuto y el servicio medio dura 2.5 segundos">

Si el servidor está ocupado, la nueva solicitud espera. El servicio sigue disponible, pero la disponibilidad no indica cuánto demora cada respuesta.

---

## ¿Qué estudia la teoría de filas?

La teoría de filas estudia sistemas donde solicitudes llegan, esperan si los servidores están ocupados, reciben servicio y salen. Si no queda espacio, se rechazan.

Un modelo describe las llegadas, el espacio de espera, la capacidad de servicio y el orden de atención. Así permite estimar tiempos de respuesta, solicitudes acumuladas, utilización y bloqueo.

---

## Qué observaremos bajo carga

| Pregunta | Medida |
|---|---|
| ¿Cuánto tarda una solicitud, en promedio? | $W$ (*waiting*): desde la admisión hasta la salida. $W_q$: solo la espera. |
| ¿Cuántas solicitudes hay, en promedio? | $L$ (*line*): en todo el sistema. $L_q$: solo en espera. |
| ¿Qué fracción del tiempo está ocupado el servidor? | Utilización $\rho$ |
| ¿Qué probabilidad hay de rechazar una llegada? | Bloqueo $P_{\mathrm B}$ y tasa efectiva $\lambda_{\mathrm e}$ |
| ¿Cuántos servidores satisfacen el objetivo? | Capacidad y latencia para un SLA |

---

## Magnitudes y frontera

| Magnitud | Significado | Unidad típica |
|---|---|---|
| $\lambda$ | tasa de llegada ofrecida | $\mathrm{solicitudes}\,\mathrm{s}^{-1}$ |
| $\lambda_{\mathrm e}$ | tasa efectiva admitida | $\mathrm{solicitudes}\,\mathrm{s}^{-1}$ |
| $\mu$ | tasa de servicio por servidor | $\mathrm{solicitudes}\,\mathrm{s}^{-1}$ |
| $L,L_q$ | número medio en sistema y en espera | solicitudes |
| $W,W_q$ | tiempo medio en sistema y en espera | $\mathrm{s}$ |
| $\rho$ | utilización: fracción de capacidad ocupada | adimensional |
| $P_{\mathrm B}$ | probabilidad de bloqueo | adimensional |

Un acuerdo de nivel de servicio (SLA) fija el objetivo de latencia, bloqueo u otra métrica que se debe satisfacer.

---

## Anatomía de una fila

<img class="diagram" src="images/mm1-model.png" alt="Modelo de fila con proceso de llegadas, buffer, servidor y salidas">

La frontera del sistema incluye a quienes esperan y a quienes están en servicio. Una llegada puede entrar o ser rechazada si no queda espacio.

El tiempo de respuesta va desde la admisión hasta la salida. El tiempo de espera termina cuando comienza el servicio.

---

## Llegadas y servicio en Kendall

$A$ viene de *Arrival* y describe los tiempos entre llegadas. $S$ viene de *Service* y describe los tiempos de servicio. Cada campo puede tomar uno de estos valores:

| Valor | Significado |
|---|---|
| $M$ | *Markovian*. La exponencial no tiene memoria. En $A$ corresponde a llegadas Poisson y en $S$ a servicio exponencial. |
| $D$ | *Deterministic*. El tiempo entre llegadas o el tiempo de servicio es fijo. |
| $G$ | *General*. Se permite otra distribución de probabilidad. |

---

## Servidores, capacidad, población y disciplina

Los campos restantes describen los recursos y las reglas del sistema:

| Campo | Significado |
|---|---|
| $c$ | Número de servidores en paralelo. |
| $K$ | Capacidad máxima dentro del sistema, incluido quien recibe servicio. |
| $N$ | *Number* de fuentes posibles de solicitudes. |
| $D$ | *Discipline*, regla que decide quién sigue. FCFS (*First Come, First Served*) atiende en orden de llegada. |

En los campos $A$ o $S$, $D$ significa *Deterministic*. Como último campo, $D$ significa *Discipline*.

---

## Notación

Los seis campos aparecen en este orden: $A/S/c/K/N/D$. El símbolo $\infty$ indica que la capacidad o la población no tienen límite.

Por ejemplo, $M/M/2/10/\infty/\mathrm{FCFS}$ describe llegadas Poisson, servicio exponencial, dos servidores, capacidad total de diez, población ilimitada y atención en orden de llegada.

Aquí $N(t)$ representa el número de solicitudes dentro del sistema en el instante $t$. En Kendall, $N$ representa la población fuente.

---

## Ejemplos de modelos de Kendall

$M/M/1$ representa llegadas Poisson, servicios exponenciales y un servidor. Si no se especifica, se suponen capacidad y población infinitas.

$M/M/1/K$ limita el número total de solicitudes. Con $K=4$, una recibe servicio y hasta tres esperan.

$M/M/c$ tiene $c$ servidores en paralelo y una fila común. La tasa $\mu$ corresponde a cada servidor.

La abreviatura resume supuestos que deben coincidir con el proceso real.

---

## Modelos de sistemas de atención

Los modelos cambian en número de servidores y límite de solicitudes admitidas.

| Modelo | Esquema | Qué permite estudiar |
|---|---|---|
| $M/M/1$ | <img src="images/modelo-mm1.svg" width="380" alt="Tres solicitudes en una fila con espacio ilimitado, un servidor y una salida"> | Un servidor y espera ilimitada. Permite estudiar utilización, número medio y tiempo de respuesta. |
| $M/M/1/K$ | <img src="images/modelo-mm1k.svg" width="380" alt="Solicitudes y servidor dentro del límite de capacidad, con una llegada adicional rechazada"> | $K$ limita el total, incluida la solicitud atendida. Si se llena, rechaza llegadas. Permite estudiar bloqueo y pérdidas. |
| $M/M/c$ | <img src="images/modelo-mmc.svg" width="380" alt="Una fila común alimenta tres servidores en paralelo"> | Una fila común alimenta $c$ servidores. Si todos están ocupados, hay espera. Permite estudiar el efecto de agregar servidores. |

---

## Proceso Poisson en sistemas de atención

Las solicitudes llegan de forma independiente y a una tasa media constante $\lambda$. Sea $N_A(t)$ el número de llegadas durante un intervalo de duración $t$. Para $k=0,1,2,\ldots$, $k!$ indica el factorial de $k$:

$$
P(N_A(t)=k)=\frac{(\lambda t)^k e^{-\lambda t}}{k!},\qquad k=0,1,2,\ldots
$$

Con $\lambda=6\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$, la probabilidad de recibir exactamente dos solicitudes en un minuto es:

$$
P(N_A(1\ \mathrm{min})=2)=\frac{6^2e^{-6}}{2!}\approx0.0446.
$$

---

## Tiempos entre llegadas en sistemas de atención

Si las llegadas siguen un proceso de Poisson, los tiempos $T$ entre llegadas consecutivas son independientes y exponenciales. Para un umbral temporal $t$, la cola de $T$ y su media $E[T]$ son:

$$
P(T>t)=e^{-\lambda t},\qquad E[T]=\frac{1}{\lambda}.
$$

Con $\lambda=6\ \mathrm{min}^{-1}$, el tiempo medio es $1/6\ \mathrm{min}=10\ \mathrm s$.

La distribución exponencial no tiene memoria. Para una espera previa $s$ y un periodo adicional $t$:

$$
P(T>s+t\mid T>s)=P(T>t).
$$

Haber esperado $s$ sin una llegada no cambia la distribución del tiempo restante.

---

## Poisson y servicio en $M/M/1$

<img class="diagram-small" src="images/poisson-mm1.png" alt="Llegadas Poisson con tasa lambda y media entre llegadas uno sobre lambda pasan a una fila y a un servidor con tiempo de servicio exponencial de media uno sobre mu">

La primera $M$ indica llegadas Poisson, con tiempos entre llegadas exponenciales de media $1/\lambda$. La segunda $M$ indica tiempos de servicio exponenciales de media $1/\mu$. El $1$ representa un servidor. Las llegadas y los tiempos de servicio se suponen independientes.

---

## Ley de Little

Conocemos el número medio de solicitudes presentes y cuántas se completan por segundo. ¿Cuánto permanece cada solicitud?

En régimen estable, la Ley de Little relaciona estas medidas:

$$
L=\lambda_{\mathrm e}W
$$

$L$ cuenta las solicitudes en el sistema. $W$ es su tiempo medio desde la admisión hasta la salida y $\lambda_{\mathrm e}$ la tasa efectiva admitida.

Para esas mismas solicitudes mientras esperan:

$$
L_q=\lambda_{\mathrm e}W_q
$$

$L_q$ cuenta las solicitudes en espera y $W_q$ es su tiempo medio de espera.

<div class="callout">
No requiere distribuciones exponenciales. Use la misma población y frontera.
</div>

---

## ¿De dónde sale la Ley de Little?

Más solicitudes completadas por segundo o más tiempo de permanencia implican más solicitudes presentes. Si se completan $10\ \mathrm{solicitudes}\,\mathrm{s}^{-1}$ y cada una permanece $2\ \mathrm s$, hay $20$ solicitudes en promedio:

$$
(10\ \mathrm{solicitudes}\,\mathrm{s}^{-1})(2\ \mathrm s)=20\ \mathrm{solicitudes}.
$$

El producto de la tasa por el tiempo de permanencia da el número medio de solicitudes presentes.

---

## El área bajo $N(t)$

En cada instante $t$, $N(t)$ cuenta las solicitudes en el sistema. El área $\int_0^T N(t)\,\mathrm{d}t$ suma su tiempo total de permanencia durante una observación de duración $T$.

En una observación larga, el efecto de sus extremos es pequeño. El tiempo acumulado se aproxima a $C(T)W$, donde $C(T)$ es el número de solicitudes completadas. Como $C(T)/T$ se aproxima a $\lambda_{\mathrm e}$:

$$
L=\lim_{T\to\infty}\frac{1}{T}\int_0^T N(t)\,\mathrm{d}t
=\lim_{T\to\infty}\frac{C(T)}{T}W
=\lambda_{\mathrm e}W.
$$

---

## Ejemplo Ley de Little

Una interfaz de programación de aplicaciones (API) completa $120$ solicitudes por segundo y se observan, en promedio, $18$ solicitudes dentro del sistema.

$$
W=\frac{L}{\lambda_{\mathrm e}}
=\frac{18}{120\ \mathrm s^{-1}}
=0.15\ \mathrm s
$$

Si $L_q=12$:

$$
W_q=\frac{12}{120\ \mathrm s^{-1}}
=0.10\ \mathrm s
$$

Por diferencia, el tiempo medio en servicio es $0.05\ \mathrm s$.

---

## Espera y servicio

El tiempo medio de respuesta se descompone en el tiempo medio de espera y el tiempo medio de servicio. Si cada solicitud admitida recibe un servicio que dura, en promedio, $1/\mu$:

$$
W=W_q+\frac{1}{\mu}.
$$

En el ejemplo, $0.15\ \mathrm s=0.10\ \mathrm s+0.05\ \mathrm s$. Esta igualdad descompone el tiempo de respuesta. No es la Ley de Little.

---

## Modelo $M/M/1$

En $M/M/1$, las solicitudes llegan al azar, pueden esperar si el servidor está ocupado y son atendidas por un único servidor.

<img class="diagram" src="images/mm1-model.png" alt="Llegadas con tasa lambda, fila por orden de llegada, un servidor con tasa mu y salidas">

Las llegadas siguen un proceso de Poisson con tasa $\lambda$. Los tiempos de servicio son exponenciales con tasa $\mu$. El servidor atiende en orden de llegada.

Se suponen capacidad de espera y población fuente ilimitadas. El proceso de llegadas y los tiempos de servicio son independientes.

---

## Utilización y estabilidad

$\lambda$ es el número medio de solicitudes que llegan por unidad de tiempo. $\mu$ es el número que un servidor ocupado puede atender por unidad de tiempo.

La utilización es:

$$
\rho=\frac{\lambda}{\mu}.
$$

En régimen estacionario, $\rho$ es la fracción del tiempo que el servidor está ocupado. Los tres casos son:

| Condición | Comportamiento |
|---|---|
| $\lambda<\mu$ | Estable. Existe régimen estacionario con un número medio finito de solicitudes. |
| $\lambda=\mu$ | Límite de estabilidad. No hay distribución estacionaria. |
| $\lambda>\mu$ | Sobrecarga. La fila crece sin límite a largo plazo. |

---

## Cuando la llegada supera el servicio

Si llegan $12\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$ y el servidor puede atender $10\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$:

$$
\lambda=12,\qquad \mu=10,\qquad \rho=\frac{12}{10}=1.2.
$$

El valor $\rho=1.2$ no significa que el servidor esté ocupado el $120\%$ del tiempo. Fuera del régimen estable, $\rho$ compara la carga ofrecida con la capacidad.

La tasa de llegada supera la capacidad en dos solicitudes por minuto. A largo plazo, la acumulación crece aproximadamente a ese ritmo.

Las fórmulas estacionarias de $M/M/1$ solo se aplican bajo esta condición:

$$
\boxed{\lambda<\mu\quad\Longleftrightarrow\quad\rho<1}.
$$

Incluso en este régimen, la variabilidad puede formar filas.

---

## ¿Por qué se forma una fila si $\lambda<\mu$?

Aunque la llegada media sea menor que la capacidad, algunas solicitudes pueden coincidir mientras el servidor está ocupado. Para seguir la acumulación usamos el estado

$$
N(t)=\text{número de solicitudes dentro del sistema}.
$$

Con servicio exponencial, basta conocer cuántas solicitudes hay. Otras distribuciones pueden exigir más información sobre el servicio en curso.

---

## $M/M/1$ como proceso de nacimiento y muerte

<img class="diagram" src="images/nacimiento-muerte-fila.png" alt="Estados 0, 1, 2 y 3 con llegadas lambda y servicios mu, sin salida de servicio desde el estado 0">

El estado $n$ cuenta las solicitudes en el sistema. El proceso cambia el estado de uno en uno. Cada llegada es un nacimiento y cada servicio completado es una muerte.

$$
n\to n+1\text{ a tasa }\lambda
$$

$$
n\to n-1\text{ a tasa }\mu,\qquad n\ge1
$$

---

## Distribución estacionaria de $M/M/1$

Sea $\pi_n$ la probabilidad estacionaria de tener $n$ solicitudes. $\pi_0$ corresponde al sistema vacío.

En equilibrio se igualan los flujos vecinos. Cada flujo es la probabilidad del estado de origen por su tasa de transición:

$$
\pi_n\lambda=\pi_{n+1}\mu
\quad\Longrightarrow\quad
\pi_n=\rho^n\pi_0.
$$

Al normalizar, la serie geométrica converge si $\rho<1$:

$$
1=\sum_{n=0}^{\infty}\pi_n
=\pi_0\sum_{n=0}^{\infty}\rho^n
=\frac{\pi_0}{1-\rho}
\quad\Longrightarrow\quad
\pi_0=1-\rho,
\qquad
\pi_n=(1-\rho)\rho^n.
$$

La suma es una serie geométrica infinita: $\sum_{n=0}^{\infty}\rho^n=\frac{1}{1-\rho}$, válida para $0\leq\rho<1$. Ver [serie geométrica](https://es.wikipedia.org/wiki/Serie_geom%C3%A9trica).

---

## Interpretar la estabilidad

La serie geométrica converge solo si $\rho<1$. En $M/M/1$, esta condición permite normalizar la distribución estacionaria.

Si $\lambda=\mu$, no hay régimen estacionario con promedios finitos. Si $\lambda>\mu$, la carga supera la capacidad.

En ambos casos fallan las fórmulas estacionarias de $M/M/1$. Se puede reducir llegadas, aumentar el servicio o agregar servidores. Limitar la capacidad evita una fila infinita, pero causa pérdidas.

---

## Obtener el número medio en el sistema

Con $\pi_n=(1-\rho)\rho^n$:

$$
L=\sum_{n=0}^{\infty}n\pi_n=(1-\rho)\sum_{n=0}^{\infty}n\rho^n.
$$

Esta suma es un caso de una [serie aritmético-geométrica](https://es.wikipedia.org/wiki/Secuencia_aritm%C3%A9tico-geom%C3%A9trica):

$$
\sum_{n=0}^{\infty}n\rho^n=\frac{\rho}{(1-\rho)^2}.
$$

Por tanto:

$$
L=\frac{\rho}{1-\rho}.
$$

---

## Número medio en espera

Sea $N$ el número de solicitudes en el sistema y $Q$ el número esperando. Como hay un solo servidor:

$$
Q=N-\mathbf{1}_{\{N\geq1\}},
$$

con el indicador igual a uno si el servidor está ocupado y cero si está libre. En régimen estacionario:

$$
L_q=\mathbb{E}[Q]=\mathbb{E}[N]-\mathbb{P}(N\geq1)=L-\mathbb{P}(\text{servidor ocupado}).
$$

En $M/M/1$, $\pi_0=1-\rho$, por lo que:

$$
\mathbb{P}(N\geq1)=1-\pi_0=\rho,
$$

Por tanto, el número medio total se descompone en solicitudes en espera y en servicio. Como hay un solo servidor, el número medio en servicio es $\rho$:

$$
L=L_q+\rho
\quad\Longrightarrow\quad
L_q=L-\rho.
$$

---

## Métricas de $M/M/1$


> Estas fórmulas describen promedios estacionarios y se aplican cuando $\rho<1$.

<div class="columns">
<div>

**Número medio de solicitudes**

$L$ cuenta las solicitudes en espera y en servicio. $L_q$ cuenta solo las que esperan. Como se explicó antes, $L_q=L-\rho$.

$$
\begin{aligned}
L &=\frac{\rho}{1-\rho}=\frac{\lambda}{\mu-\lambda},\\
L_q &=\frac{\rho^2}{1-\rho}=\frac{\lambda^2}{\mu(\mu-\lambda)}.
\end{aligned}
$$

</div>
<div>

**Tiempo medio de permanencia**

$W$ mide el tiempo desde que una solicitud llega hasta que sale. $W_q$ mide solo el tiempo que espera en la fila. Como la cola es ilimitada, todas las llegadas entran al sistema y la tasa efectiva es $\lambda$. Por la ley de Little, $L=\lambda W$ y $L_q=\lambda W_q$.

$$
\begin{aligned}
W &=\frac{L}{\lambda}=\frac{1}{\mu-\lambda},\\
W_q &=\frac{L_q}{\lambda}=\frac{\lambda}{\mu(\mu-\lambda)}=\frac{\rho}{\mu-\lambda}.
\end{aligned}
$$
</div>
</div>

---

## Por qué la latencia crece tan rápido

$$
W=\frac{1}{\mu(1-\rho)}
$$

El tiempo medio de servicio es $1/\mu$. La siguiente tabla expresa el tiempo total de permanencia $W$ como múltiplo de ese tiempo $\frac{W}{1/\mu}=\frac{1}{1-\rho}.$

<table style="margin: 0 auto; text-align: center;">
<thead>
<tr><th>ρ</th><th>W/(1/μ)</th></tr>
</thead>
<tbody>
<tr><td>0.50</td><td>2 veces</td></tr>
<tr><td>0.80</td><td>5 veces</td></tr>
<tr><td>0.90</td><td>10 veces</td></tr>
<tr><td>0.95</td><td>20 veces</td></tr>
</tbody>
</table>


---

## Por qué la latencia crece tan rápido

Por ejemplo, con $\rho=0.95$, el tiempo medio total es $20$ veces el tiempo de servicio. De ese total, $19$ veces corresponden a la espera en la fila, pues $W_q=W-1/\mu$. El servicio no se vuelve más lento. El aumento se debe a la espera.

<div class="warn">

El modelo es estable cuando $\rho<1$. Al acercarse $\rho$ a $1$, el factor $1/(1-\rho)$ crece rápidamente y queda menos margen para variaciones en la carga.

</div>

---

## Ejemplo $M/M/1$: puerta de enlace

Considere una puerta de enlace que recibe solicitudes y las envía a un único servidor. 

Modele las llegadas como un proceso de Poisson y los tiempos de servicio como exponenciales e independientes. Suponga que $\lambda=125\ \mathrm{solicitudes/s}$ y que el tiempo medio de servicio es $1/\mu=2\ \mathrm{ms}$.

### Preguntas

1. Calcule la tasa media de servicio $\mu$ y la utilización $\rho$. Determine si el sistema es estable.
2. Calcule el tiempo medio de permanencia $W$ y el tiempo medio de espera $W_q$.
3. Calcule el número medio de solicitudes en el sistema $L$ y en la fila $L_q$. Interprete los resultados.

---

## Ejemplo $M/M/1$: respuestas

**Tasa media de servicio**

$$
\mu=\frac{1}{\mathbb{E}[S]}=\frac{1}{2\ \mathrm{ms}}=500\ \mathrm{solicitudes/s}.
$$

**Utilización y estabilidad**

$$
\rho=\frac{\lambda}{\mu}=\frac{125}{500}=0.25<1.
$$

El sistema es estable porque $\rho<1$.

---

## Ejemplo $M/M/1$: respuestas de tiempo

$W$ mide el tiempo desde la llegada hasta la salida. $W_q$ mide solo la espera en la fila.

**Permanencia media en el sistema**

$$
W=\frac{1}{\mu-\lambda}=\frac{1}{500-125}\ \mathrm{s}=2.667\ \mathrm{ms}.
$$

**Espera media en la fila**

$$
W_q=\frac{\lambda}{\mu(\mu-\lambda)}=\frac{125}{500(500-125)}\ \mathrm{s}=0.667\ \mathrm{ms}.
$$

---

## Ejemplo $M/M/1$: respuestas de cantidad

Por la ley de Little, $L=\lambda W$ y $L_q=\lambda W_q$:

$$
\begin{aligned}
L &=\lambda W=(125\ \mathrm{solicitudes/s})(0.002667\ \mathrm{s})=0.333\ \mathrm{solicitudes},\\
L_q &=\lambda W_q=(125\ \mathrm{solicitudes/s})(0.000667\ \mathrm{s})=0.083\ \mathrm{solicitudes}.
\end{aligned}
$$

En promedio, cada solicitud permanece $2.667\ \mathrm{ms}$ en el sistema, incluidos $0.667\ \mathrm{ms}$ de espera y $2\ \mathrm{ms}$ de servicio. $L$ y $L_q$ son promedios temporales, por eso pueden ser menores que una solicitud.

---

## Capacidad finita: $M/M/1/K$

$K$ es el número máximo **en el sistema**, incluido quien está en servicio.

Los estados son $0,1,\ldots,K$. En $K$ una llegada es rechazada y no cambia el estado. En el estado $0$, el sistema está vacío y el servidor ocioso. No hay una solicitud en servicio que pueda salir.

El balance entre vecinos sigue dando $\pi_n=\pi_0\rho^n$, pero ahora la normalización tiene solo $K+1$ términos. Aquí $\rho=\lambda/\mu$ compara la carga ofrecida con la capacidad.

Incluso con $\rho\geq1$ existe una distribución estacionaria en esta cadena finita. Eso no garantiza una pérdida aceptable.

---

## Capacidad finita: normalización

Para $\rho\ne1$:

$$
\pi_n=\frac{(1-\rho)\rho^n}{1-\rho^{K+1}},
\qquad 0\le n\le K
$$

Si $\rho=1$, todos los estados tienen igual probabilidad:

$$
\pi_n=\frac{1}{K+1}.
$$

La distribución está truncada por la capacidad. Los clientes que no entran no aparecen dentro de estos estados.

---

## Bloqueo y tasa efectiva

Por la propiedad *Poisson Arrivals See Time Averages* (PASTA), las llegadas ofrecidas de Poisson ven el sistema lleno con la misma probabilidad que en un instante cualquiera:

$$
P_{\mathrm B}=\pi_K
$$

La tasa admitida es:

$$
\lambda_{\mathrm e}=\lambda(1-P_{\mathrm B})
$$

Las llegadas admitidas excluyen los rechazos y no forman necesariamente un proceso de Poisson.

---

## Medidas con capacidad finita

El servidor está ocupado con probabilidad $1-\pi_0$. En equilibrio, la tasa media de salida coincide con la tasa admitida:

$$
\lambda_{\mathrm e}=\mu(1-\pi_0).
$$

Para $\lambda_{\mathrm e}>0$, las medidas de los clientes admitidos son:

$$
L=\sum_{n=0}^{K}n\pi_n,\qquad L_q=L-(1-\pi_0).
$$

$$
W=\frac{L}{\lambda_{\mathrm e}},\qquad W_q=\frac{L_q}{\lambda_{\mathrm e}}=W-\frac{1}{\mu}.
$$

La ocupación real es $1-\pi_0=\lambda_{\mathrm e}/\mu$. No es necesariamente igual a la carga ofrecida $\lambda/\mu$.

---

## Ejemplo de bloqueo

En una fila $M/M/1/K$, use $\lambda=8\ \mathrm{s}^{-1}$, $\mu=10\ \mathrm{s}^{-1}$ y $K=3$:

$$
\rho=0.8
$$

$$
P_{\mathrm B}=\pi_3
=\frac{(1-0.8)(0.8)^3}{1-(0.8)^4}
\approx0.173442
$$

$$
\lambda_{\mathrm e}=8(1-0.173442)
\approx6.6125\ \mathrm{s}^{-1}
$$

Usar $\lambda$ en lugar de $\lambda_{\mathrm e}$ incluye solicitudes rechazadas y subestima el tiempo $W=L/\lambda_{\mathrm e}$ de los clientes admitidos.

---

## Completar el ejemplo de capacidad finita

Con $\rho=0.8$ y $K=3$, la fila $\boldsymbol\pi$ lista las probabilidades de los estados $0$ a $K$:

$$
\boldsymbol\pi\approx\begin{bmatrix}0.338753&0.271003&0.216802&0.173442\end{bmatrix}.
$$

$$
L=\sum_{n=0}^3n\pi_n\approx1.224932,
\qquad W=\frac{L}{\lambda_{\mathrm e}}\approx0.185246\ \mathrm s.
$$

Entonces $W_q=W-0.1\ \mathrm s\approx0.085246\ \mathrm s$.

El tiempo $W$ corresponde a clientes admitidos. El bloqueo se informa aparte, porque las solicitudes rechazadas no reciben servicio.

---

## Taller: dimensionar el espacio de espera

Una puerta de enlace de red recibe $\lambda=125\ \mathrm{paquetes}\,\mathrm{s}^{-1}$ y tiene un tiempo de servicio medio de $2\ \mathrm{ms}$. Suponemos un modelo $M/M/1/K$ con $\rho=0.25$.

Para $B$ puestos de espera, $K=B+1$:

$$
P_{\mathrm B}=\frac{0.75(0.25)^K}{1-(0.25)^{K+1}}.
$$

Con $B=13$, la capacidad total es $K=14$ y $P_{\mathrm B}\approx2.794\times10^{-9}$.

Con $K=9$, $P_{\mathrm B}\approx2.861\times10^{-6}$. Con $K=10$, $P_{\mathrm B}\approx7.153\times10^{-7}$.

Para bajar de un paquete perdido por millón se requieren al menos $K=10$ puestos totales, equivalentes a nueve puestos de espera.

---

## Varios servidores: $M/M/c$

Las solicitudes llegan según Poisson a una fila común ilimitada. Cada uno de los $c$ servidores tiene tiempos de servicio exponenciales de media $1/\mu$. Llegadas y servicios son independientes, y la fila atiende en orden de llegada.

<img class="diagram" src="images/mmc-model.png" alt="Una fila común distribuye solicitudes a varios servidores en paralelo">

En el estado $n$, $\min(n,c)$ es el menor valor entre $n$ y $c$. Por tanto, la tasa total de salida es $\min(n,c)\mu$. Si hay menos clientes que servidores, solo una parte de los servidores está trabajando.

---

## $M/M/c$: carga y estabilidad

Llame $a$ al tráfico ofrecido, medido en Erlangs, y $\rho$ a la carga por servidor. En régimen estable, $\rho$ es la utilización media de cada servidor:

$$
a=\frac{\lambda}{\mu},
\qquad
\rho=\frac{\lambda}{c\mu}=\frac{a}{c}
$$

La condición de estabilidad es:

$$
\lambda<c\mu
$$

---

## $M/M/c$: antes y después de llenar los servidores

El balance local es

$$
\pi_n\lambda=\pi_{n+1}\min(n+1,c)\mu.
$$

Con $a=\lambda/\mu$ y usando $n!$ para el factorial de $n$:

$$
\pi_n=\pi_0\frac{a^n}{n!}\quad(n<c),
\qquad \pi_n=\pi_0\frac{a^c}{c!}\rho^{n-c}\quad(n\geq c).
$$

Hasta $c$ aumenta el número de servidores ocupados. Después, la capacidad total se mantiene en $c\mu$ y comienza a crecer la espera.

---

## Tasas que dependen del estado

En un proceso de nacimiento y muerte, $\lambda_n$ es la tasa de transición de $n$ a $n+1$ y $\mu_n$ la de $n$ a $n-1$. Ambas pueden depender del número $n$ de solicitudes.

Para dos estados vecinos presentes en el sistema, el balance estacionario es:

$$
\pi_n\lambda_n=\pi_{n+1}\mu_{n+1}.
$$

Si $\mu_{n+1}>0$, se obtiene $\pi_{n+1}=\frac{\lambda_n}{\mu_{n+1}}\pi_n$.

En $M/M/1/K$, el estado lleno $K$ no tiene transición de llegada a $K+1$. Las probabilidades se normalizan sobre los estados $0$ a $K$.

---

## Probabilidad de esperar: Erlang C

En $M/M/c$ estable, Erlang C, $C(c,a)$, da la probabilidad de esperar. Sea $T_q$ el tiempo de espera de una solicitud y $W_q$ su media. La notación $\Pr(\cdot)$ indica probabilidad.

Primero calculamos $\pi_0$, la probabilidad de que el sistema esté vacío:

$$
\pi_0=\left[
\sum_{n=0}^{c-1}\frac{a^n}{n!}
+\frac{a^c}{c!}\frac{1}{1-\rho}
\right]^{-1}
$$

Entonces:

$$
C(c,a)=\Pr(T_q>0)
=\frac{a^c}{c!}\frac{1}{1-\rho}\,\pi_0
$$

Esperan las solicitudes que encuentran los $c$ servidores ocupados.

---

## Métricas de $M/M/c$

Una vez calculado $C(c,a)$:

$$
W_q=\frac{C(c,a)}{c\mu-\lambda}
$$

$$
W=W_q+\frac{1}{\mu}
$$

$$
L_q=\lambda W_q,
\qquad
L=\lambda W
$$

<div class="warn">

No se debe modelar $c$ servidores como uno solo de tasa $c\mu$: esa simplificación cambia la distribución del tiempo de servicio y la probabilidad de espera.

</div>

---

## Ejemplo $M/M/2$

Use $\lambda=6\ \mathrm h^{-1}$, $c=2$ y $\mu=4\ \mathrm h^{-1}$ por servidor.

$$
a=\frac{6}{4}=1.5,
\qquad
\rho=\frac{6}{8}=0.75
$$

$$
\pi_0=\left[1+1.5+\frac{1.5^2}{2(1-0.75)}\right]^{-1}
=\frac{1}{7}
$$

$$
C(2,1.5)=\frac{1.5^2}{2(1-0.75)}\frac{1}{7}
=\frac{4.5}{7}\approx0.6429
$$

---

## Completar el ejemplo $M/M/2$

$$
W_q=\frac{9/14}{8-6}\ \mathrm h
=\frac{9}{28}\ \mathrm h\approx0.3214\ \mathrm h
$$

$$
W=\left(\frac{9}{28}+\frac{1}{4}\right)\mathrm h
=\frac{4}{7}\ \mathrm h\approx0.5714\ \mathrm h
$$

$$
L_q=6\left(\frac{9}{28}\right)\approx1.9286
$$

$$
L=6\left(\frac{4}{7}\right)\approx3.4286
$$

Las unidades permiten detectar errores: dividir una probabilidad por una tasa produce tiempo.

---

## Qué significa Erlang C

En el ejemplo $M/M/2$, el $64.29\%$ de las solicitudes debe esperar.

El promedio $W_q=0.3214\ \mathrm h$ incluye a quienes no esperan. Con atención FCFS, entre quienes sí esperan:

$E[\,\cdot\,]$ denota esperanza y $\mid$ significa “condicionado a”.

$$
E[T_q\mid T_q>0]=\frac{1}{c\mu-\lambda}=0.5\ \mathrm h.
$$

La probabilidad de esperar y el tiempo medio entre quienes esperan describen resultados distintos. Un SLA puede limitar cualquiera de ellos.

---

## Selección del modelo

<img class="diagram" src="images/decision-colas.png" alt="Condiciones de uso de Little, M/M/1, M/M/1/K y M/M/c">

Si los supuestos no se sostienen, no basta con sustituir tasas en una fórmula. Debemos cambiar el modelo o usar simulación.

---

## Preguntas para elegir el modelo

Preguntas en orden:

1. ¿Solo se necesitan promedios relacionados? Use Little.
2. ¿Las llegadas son Poisson y los servicios exponenciales?
3. ¿Cuántos servidores existen?
4. ¿La capacidad es finita?
5. Si la capacidad es infinita, ¿se cumple la condición de estabilidad?

---

## Diseñar para un objetivo medio de latencia

Llame $w_{\max}$ al tiempo medio máximo permitido. En $M/M/1$, si se exige $W\leq w_{\max}$:

$$
\frac{1}{\mu-\lambda}\leq w_{\max}
\quad\Longrightarrow\quad
\mu\geq\lambda+\frac{1}{w_{\max}}.
$$

Con $\lambda=18\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$ y $w_{\max}=4\ \mathrm s$, necesitamos $\mu\geq33\ \mathrm{solicitudes}\,\mathrm{min}^{-1}$.

El tiempo medio de servicio debe ser como máximo $60/33=1.818\ \mathrm s$. No basta con que la tasa supere apenas las llegadas.

---

## Un promedio no es un percentil

Un SLA puede exigir que el $95\%$ de las solicitudes responda antes del umbral $w$. Sea $W_{\mathrm{cliente}}$ el tiempo de respuesta de una solicitud. Usamos $\Pr(\cdot)$ para una probabilidad, $e$ para el número de Euler y $\ln$ para el logaritmo natural.

En $M/M/1$ estable con disciplina FCFS, la probabilidad de exceder $w$ es:

$$
\Pr(W_{\mathrm{cliente}}>w)=e^{-(\mu-\lambda)w}.
$$

El percentil 95 es $w_{95}=\ln(20)/(\mu-\lambda)$.

Con $\lambda=18\ \mathrm{min}^{-1}$ y $\mu=24\ \mathrm{min}^{-1}$, el promedio es $10\ \mathrm s$ y el percentil 95 es $29.96\ \mathrm s$.

---

## Diseño con varios servidores

Para $M/M/c$ no despejamos $c$ como una tasa continua. Probamos números enteros de servidores.

1. Comience con $c$ tal que $\lambda<c\mu$.
2. Calcule Erlang C y el tiempo medio de espera.
3. Compare la probabilidad o el tiempo medio con el requisito correspondiente.
4. Aumente $c$ si no cumple y revise el costo.

La primera configuración estable puede no satisfacer el SLA. La estabilidad es una condición de existencia, no una medida de calidad.

---

## Validar el modelo con datos

Antes de recomendar capacidad, contraste los supuestos:

- compare la tasa de llegada entre ventanas, no solo el promedio diario.
- revise la distribución y variabilidad de los tiempos de servicio.
- identifique pérdidas, reintentos, prioridades y límites de memoria.
- mida $L$ y $W$ con la misma frontera y compruebe Little.

Ráfagas correlacionadas, tiempos casi constantes o cambios de capacidad pueden requerir otros modelos o simulación.

---

## Ejercicio: dimensionar un servicio

Una interfaz recibe solicitudes Poisson a tasa $\lambda=18\ \mathrm{min}^{-1}$. Un servidor atiende en tiempo exponencial medio de $2.5\ \mathrm s$.

1. Justifique el modelo.
2. Calcule $\mu$ y $\rho$.
3. Determine $L$, $L_q$, $W$ y $W_q$.
4. Si se exige $W\le4\ \mathrm s$, ¿cumple?
5. Indique una limitación del modelo.

---

## Solución del ejercicio

Con unidades en minutos:

$$
\mu=\frac{60}{2.5}=24\ \mathrm{min}^{-1},
\qquad
\rho=\frac{18}{24}=0.75
$$

$$
L=\frac{18}{24-18}=3,
\qquad
L_q=\frac{18^2}{24(24-18)}=2.25
$$

$$
W=\frac{1}{6}\ \mathrm{min}=10\ \mathrm s
$$

$$
W_q=10-2.5=7.5\ \mathrm s
$$

No cumple $W\le4\ \mathrm s$. Una limitación es suponer servicios exponenciales independientes de la carga.

---

## Comprobación computacional

El programa `codigo/queue_models.py` verifica $M/M/1$, $M/M/1/K$ y $M/M/c$.

Antes de comparar, revise unidades de tiempo, capacidad y número de servidores.

Compruebe $L=\lambda_{\mathrm e}W$ y $L_q=\lambda_{\mathrm e}W_q$. Si no coinciden, quizá se usó la tasa ofrecida en vez de la admitida.

---

## Errores frecuentes

| Error | Corrección |
|---|---|
| Mezclar segundos y minutos | Convierta antes de calcular $\rho$ |
| Usar fórmulas de capacidad infinita sin revisar estabilidad | Verifique $\lambda<c\mu$ |
| Confundir $L$ con $L_q$ | $L$ incluye servicio. $L_q$ sólo espera |
| Usar $\lambda$ si existe bloqueo | Use $\lambda_{\mathrm e}$ en Little |
| Dimensionar solo para estabilidad | Compare la espera con el requisito de servicio |

---

## Notación para estados de capacidad

Los subíndices $N$, $D$ y $F$ representan los estados normal, degradado y de falla, respectivamente.

- $\pi_N$, $\pi_D$ y $\pi_F$ son sus probabilidades estacionarias.
- $\mu_N$, $\mu_D$ y $\mu_F$ son sus tasas de servicio.
- $W_N$ y $W_D$ son los tiempos medios de respuesta en los estados normal y degradado. En falla no hay servicio.

---

## Promediar una métrica por estado

Para un estado $i$, $\pi_i$ es su probabilidad estacionaria y $r_i$ la recompensa por unidad de tiempo asignada a la métrica en ese estado. El promedio de largo plazo es:

$$
\overline r=\sum_i\pi_i r_i.
$$

Para medir la fracción de tiempo que se cumple un objetivo, asigne $r_i=1$ a los estados que lo cumplen y $r_i=0$ a los demás. Un promedio de respuesta solo durante la operación se calcula aparte, excluyendo la falla.

---

## La capacidad depende del estado

<img class="diagram" src="images/estado-capacidad-sla.png" alt="La misma carga produce distintos tiempos de respuesta y resultados de SLA en los estados normal, degradado y de falla">

La disponibilidad identifica cuánto tiempo ocupa cada estado. El modelo de filas indica qué experiencia recibe cada solicitud cuando el servicio está operativo.

---

## Capacidad normal y degradada

Un servicio tiene dos estados operativos y uno de falla. Los cambios de estado son lentos. En estado normal atiende $50\ \mathrm{solicitudes}\,\mathrm{s}^{-1}$ y en estado degradado $25\ \mathrm{solicitudes}\,\mathrm{s}^{-1}$. La carga es $20\ \mathrm{solicitudes}\,\mathrm{s}^{-1}$.

Como aproximación, tratamos cada estado como una fila $M/M/1$ en equilibrio:

$$
W_N=\frac{1}{50-20}=0.0333\ \mathrm s,
\qquad W_D=\frac{1}{25-20}=0.2\ \mathrm s.
$$

Los dos estados entregan servicio, pero tienen tiempos de respuesta muy distintos. Debemos combinar disponibilidad y rendimiento.

---

## Cumplimiento de un objetivo de latencia

Representamos los cambios entre estados del servicio mediante una cadena de Markov de tiempo continuo (CTMC), un modelo que describe estados y tasas de transición. Sus probabilidades estacionarias son $(\pi_F,\pi_D,\pi_N)=(0.02,0.18,0.80)$.

Si el SLA exige tiempo medio menor que $0.1\ \mathrm s$, solo el estado normal $N$ cumple. Asignamos valor uno a los estados que cumplen y cero a los demás. La fracción de tiempo que alcanza el objetivo es:

$$
A_{\mathrm{SLA}}=\pi_N=0.80.
$$

La disponibilidad de conectividad es $\pi_D+\pi_N=0.98$.

---

## Tiempo medio de respuesta durante la operación

Suponemos llegadas Poisson independientes del estado y rechazadas durante la falla. Para aproximar la respuesta de las solicitudes admitidas, ponderamos los tiempos de los estados operativos por sus probabilidades:

$$
\overline W_{\mathrm{op}}\approx\frac{0.18(0.2)+0.80(0.0333)}{0.98}=0.0639\ \mathrm s.
$$

El resultado aproxima el tiempo medio de respuesta de las solicitudes admitidas cuando los cambios de capacidad son lentos.

---

## Límites de la aproximación

El promedio por estados supone que cada fila alcanza aproximadamente su equilibrio antes de que cambie la capacidad.

También supone llegadas independientes del estado y rechazos durante la caída. Si los clientes se acumulan, el estado debe incluir el número en espera.

Si el estado degradado tiene tasa de servicio $\mu_D\leq\lambda$, no podemos asignarle el promedio estacionario $1/(\mu_D-\lambda)$.

Un análisis exacto puede requerir una cadena conjunta de condición del servicio y ocupación, o una simulación que conserve la acumulación entre estados.

---

## Ejercicio de diseño: comparar mejoras

Para el servicio anterior, compare dos cambios:

1. mejorar la capacidad degradada de $25$ a $40\ \mathrm{solicitudes}\,\mathrm{s}^{-1}$.
2. reducir su probabilidad estacionaria de $0.18$ a $0.08$, aumentando la normal de $0.80$ a $0.90$.

Determine qué cambia en la disponibilidad de conectividad, la fracción que cumple el SLA y el promedio aproximado de respuesta.

Justifique si conviene recuperar capacidad o reducir la frecuencia de degradación. Declare los supuestos de la combinación de modelos.

---

## Ejercicio de diseño: resultados

| Configuración | Conectividad | Cumplimiento por estado | $\overline W_{\mathrm{op}}$ |
|---|---:|---:|---:|
| original | $0.98$ | $0.80$ | $0.0639\ \mathrm s$ |
| capacidad degradada de $40$ | $0.98$ | $0.98$ | $0.0364\ \mathrm s$ |
| menos degradación | $0.98$ | $0.90$ | $0.0469\ \mathrm s$ |

Ambas alternativas mantienen la conectividad. La primera consigue que también el estado degradado cumpla el objetivo medio de $0.1$ s.

La elección necesita además costos y evidencia de las tasas y capacidades propuestas.

---

## De los modelos a las decisiones de diseño

La confiabilidad pregunta si se completa una misión. La disponibilidad mide cuánto tiempo se ofrece el servicio. La teoría de filas evalúa cómo responde bajo carga.

- Delimite la frontera y mida tasa, número y tiempo sobre la misma población.
- Justifique los supuestos del modelo y revise estabilidad y unidades.
- Dimensione capacidad frente a latencia, bloqueo y costo.
- Evalúe disponibilidad y rendimiento en conjunto para determinar si se cumple el SLA.

Estos modelos convierten mediciones y requisitos del servicio en decisiones de diseño y operación.

---

## Referencias

1. J. F. Shortle, J. M. Thompson, D. Gross y C. M. Harris, *Fundamentals of Queueing Theory*, 5.ª ed., Wiley, 2018, capítulos 1 a 3 y secciones 7.7 y 9.3.
2. K. S. Trivedi, *Probability and Statistics with Reliability, Queuing, and Computer Science Applications*, 2.ª ed., Wiley, 2002, capítulos 8 y 9.
3. D. P. Bertsekas y R. G. Gallager, *Data Networks*, 2.ª ed., Prentice Hall, 1992, cap. 3.
4. J. M. Martínez, *Continuous Time Markov Chains*, material histórico TEL211, páginas 41 a 50.
5. L. A. Lizama, *Teoría de Colas TEL211*, material histórico USM. Kendall, Little y ejemplo de dimensionamiento del gateway.
