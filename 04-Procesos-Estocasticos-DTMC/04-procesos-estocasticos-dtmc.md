---
marp: true
math: katex
paginate: true
size: 16:9
style: |
  section {
    font-size: 27px;
    line-height: 1.35;
  }

  section.lead {
    text-align: center;
  }

  section.lead h1 {
    font-size: 2.1em;
  }

  h2, h3 {
    color: #12394f;
  }

  table {
    font-size: 0.78em;
  }

  .small {
    font-size: 0.82em;
  }

  .compact {
    font-size: 24px;
  }

  .callout {
    background: #eef6fb;
    border-left: 6px solid #2f6f9f;
    border-radius: 6px;
    padding: 0.7em 0.9em;
  }

  .bridge {
    background: #f7f8fa;
    border-left: 6px solid #6b7280;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }

  .warn {
    background: #fff4df;
    border-left: 6px solid #b7791f;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }

  .example {
    background: #eef8f1;
    border-left: 6px solid #2f855a;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }

  .columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 32px;
    align-items: start;
  }

  img.diagram {
    display: block;
    margin: 0 auto;
    max-width: 100%;
    max-height: 285px;
  }

  img.diagram-small {
    display: block;
    margin: 0 auto;
    max-width: 100%;
    max-height: 220px;
  }

---

<!-- _class: lead -->

# TEL211
## Disponibilidad y Rendimiento de Sistemas TIC
### Procesos estocásticos y cadenas de Markov en tiempo discreto

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Continuidad

Hasta ahora hemos respondido preguntas como:

> ¿Cuál es la probabilidad de que el sistema complete una misión sin fallar?

Los modelos de confiabilidad y los RBD permiten obtener

$$
R_{\text{sistema}}(t).
$$

Pero una misión de confiabilidad **no incorpora naturalmente la recuperación después de una falla**.

<div class="bridge">

Ahora queremos seguir al sistema mientras cambia de estado: puede funcionar, degradarse, fallar y volver a operar.

</div>

---

## De una arquitectura a su evolución

Un RBD responde:

> **¿Qué combinación de componentes debe funcionar?**

Una cadena de Markov responde:

> **¿Cómo cambia el estado del sistema con el tiempo?**

Por ejemplo, un servicio podría evolucionar como

$$
\text{operativo}
\rightarrow
\text{fallado}
\rightarrow
\text{operativo}
\rightarrow
\text{operativo}
\rightarrow\cdots
$$

<div class="callout">

Ahora interesa conocer **en qué estado se encuentra el sistema en cada instante**, además de saber si falló.

</div>

---

## Propósito de esta clase

Al finalizar podremos:

1. representar la evolución aleatoria de un sistema mediante estados.
2. construir e interpretar una matriz de transición.
3. calcular probabilidades después de varios pasos.
4. distinguir comportamiento transiente y estacionario.
5. utilizar una cadena de Markov en tiempo discreto, abreviada DTMC, para describir sistemas reparables.
6. diferenciar estar en un estado después de $n$ pasos de llegar a él por primera vez.

---

## De una variable aleatoria a un proceso

En confiabilidad trabajamos, por ejemplo, con

$$
T=\text{tiempo hasta la primera falla}.
$$

$T$ es **una variable aleatoria**.

Ahora observaremos repetidamente al sistema:

$$
X_0,\,X_1,\,X_2,\ldots
$$

donde $X_n$ indica su estado en el paso $n$.

<div class="callout">

Un proceso estocástico describe una **secuencia de estados aleatorios**, no solamente un único resultado aleatorio.

</div>

---

## Comparación: Variable vs Proceso

<img class="diagram" src="images/variable_aleatoria_vs_proceso_aleatorio.png" alt="Comparación visual entre una variable aleatoria y un proceso aleatorio">

<div class="callout">

Un lanzamiento produce un único valor: el resultado $5$ se transforma en $X=1$. Al repetir el experimento en los pasos $n=0,1,2,\ldots$, obtenemos una secuencia $X_0,X_1,X_2,\ldots$, es decir, una trayectoria del proceso.

</div>

---

## Proceso estocástico

Formalmente, un proceso estocástico es una familia de variables aleatorias:

$$
\{X_t:t\in\mathcal T\}.
$$

- $\mathcal T$ indica cuándo observamos el sistema.
- $X_t$ indica su estado en ese instante.

Por ejemplo:

$$
X_n=
\begin{cases}
0,&\text{servicio caído},\\
1,&\text{servicio operativo}.
\end{cases}
$$

---

## Trayectoria

Una **trayectoria** es una realización particular del proceso.

Por ejemplo:

$$
1,\,1,\,0,\,0,\,1,\,1,\,1,\ldots
$$

podría representar

$$
\text{operativo},
\text{operativo},
\text{caído},
\text{caído},
\text{operativo},\ldots
$$

<div class="callout">

El proceso describe todas las trayectorias posibles. Una trayectoria es solo una realización del proceso.

</div>

---

## Ejemplo: Estados en una llamada de voz

Supongamos que observamos una llamada de voz sobre IP (VoIP) cada $20\text{ ms}$. Cada intervalo representa un **paso** del modelo.

En cada paso clasificamos la llamada en uno de dos estados:

- **Silencio**: no se detecta actividad de voz.
- **Hablando**: se detecta actividad de voz.

Así, transformamos una señal continua en una secuencia discreta:

$$
0=\text{silencio},
\qquad
1=\text{hablando}.
$$

La secuencia $X_0,X_1,X_2,\ldots$ registra el estado observado en cada paso.

---

## Ejemplo: Estados en una llamada de voz

<img class="diagram" src="images/voz-dtmc.png" alt="Cadena de Markov con los estados silencio y hablando, transiciones p y q y permanencias 1-p y 1-q">

Entre dos observaciones consecutivas:

$$
0\to0:1-p,\quad 0\to1:p,
\qquad
1\to1:1-q,\quad 1\to0:q.
$$

donde $p$ y $q$ son probabilidades por intervalo. Las flechas curvas indican permanencias o cambios de estado.

---

## Tiempo y estado

Los procesos se pueden clasificar según dos dimensiones:

| Tiempo | Estado | Ejemplo |
|---|---|---|
| discreto | discreto | estado de un servidor cada minuto |
| continuo | discreto | número de trabajos en una fila |
| discreto | continuo | latencia media medida cada minuto |
| continuo | continuo | temperatura de un procesador |

En esta clase trabajaremos con:

$$
\boxed{\text{tiempo discreto + estados discretos}}
$$

---

## ¿Qué significa tiempo discreto?

Decimos que el tiempo es **discreto** cuando observamos el sistema en instantes separados, llamados pasos, en lugar de seguirlo continuamente.

Al combinar estos pasos con un conjunto discreto de estados y modelar cómo el sistema cambia entre ellos, obtenemos una **cadena de Markov en tiempo discreto**, o **DTMC** (*Discrete-Time Markov Chain*).

Una DTMC observa el sistema en pasos:

$$
n=0,1,2,\ldots
$$

---

## ¿Qué significa tiempo discreto?

Por ejemplo:

$$
1\text{ paso}=1\text{ minuto}.
$$

Entonces

$$
X_5
$$

representa el estado observado a los cinco minutos.

<div class="warn">

La duración de un paso forma parte del modelo. Una probabilidad de falla por minuto no es lo mismo que una probabilidad de falla por hora.

</div>

---

## El estado

El **estado** debe contener la información necesaria para describir la evolución futura.

Ejemplos:

- operativo o fallado.
- número de servidores operativos.
- número de paquetes esperando.
- calidad actual de un enlace.

<div class="callout">

Definir correctamente el estado es una decisión de modelado, no solo una cuestión de notación.

</div>

---

## ¿Cuánta información debe contener el estado?

El estado debe conservar las variables que afectan la próxima transición, pero no toda la historia del sistema.

| Situación | ¿Qué necesitamos conocer? | Estado posible |
|---|---|---|
| Un servidor sin envejecimiento | si está funcionando | $X_n\in\{\text{operativo},\text{fallado}\}$ |
| Dos servidores idénticos | cuántos funcionan | $X_n\in\{0,1,2\}$ |
| Una fila de espera | cuántos paquetes esperan | $X_n=N_n$ |
| Un componente con envejecimiento | condición y edad | $X_n=(C_n,A_n)$ |

Por ejemplo, si el riesgo de falla depende de la edad, dos componentes operativos de edades distintas no deberían representarse con el mismo estado.

<div class="warn">

Si el futuro depende de una variable que omitimos, el modelo puede dejar de ser Markoviano.

</div>

---

## Propiedad de Markov

Una cadena tiene la propiedad de Markov cuando, conocido el estado actual, la historia anterior no agrega información para predecir el siguiente paso:

$$
P(X_{n+1}=j\mid X_n=i,X_{n-1},\ldots,X_0)
=
P(X_{n+1}=j\mid X_n=i).
$$

<div class="callout">

No significa que el sistema "olvide físicamente" el pasado. Significa que **el estado actual resume toda la información histórica relevante para el modelo**.

</div>

---

## Ejemplo de estado mal elegido

Suponga que la probabilidad de falla aumenta con la edad.

Si usamos solamente:

$$
X_n=
\begin{cases}
0,&\text{fallado},\\
1,&\text{operativo},
\end{cases}
$$

dos componentes operativos de edades distintas tendrían el mismo estado, aunque su probabilidad futura de falla sea diferente.

<div class="warn">

En ese caso, la edad debería incorporarse al estado o utilizarse otro modelo.

</div>

---

## Homogeneidad

Además supondremos que la regla de transición no cambia con el número de paso:

$$
P(X_{n+1}=j\mid X_n=i)=p_{ij}.
$$

Esto se llama una **DTMC homogénea**.

Por ejemplo, si un servidor operativo falla durante un minuto con probabilidad $0.1$, utilizaremos esa misma probabilidad en cada paso.

<div class="bridge">

Markov especifica **qué información importa**.  
Homogeneidad especifica que **la regla no cambia con el tiempo**.

</div>

---

## Ejemplo: Servicio reparable - Definición

Observamos un servicio una vez por minuto.

Definimos:

$$
X_n=
\begin{cases}
0,&\text{caído},\\
1,&\text{operativo}.
\end{cases}
$$

Durante cada minuto:

- si está caído, se recupera con probabilidad $r=0.4$.
- si está operativo, falla con probabilidad $p=0.1$.

---

## Ejemplo: Servicio reparable - Diagrama de estados

<img class="diagram" src="images/servicio-reparable.png" alt="Cadena de Markov de un servicio reparable con los estados caído y operativo, fallas, reparaciones y permanencias">

Las flechas curvas representan reparación y falla. Las flechas que vuelven al mismo círculo representan permanencia.

<div class="callout">

El diagrama muestra **todas las posibilidades del siguiente paso**. Para trabajar con ellas, identificaremos cada flecha mediante su estado de origen y su estado de destino.

</div>

---

## Probabilidad de transición

Definimos

$$
p_{ij}=P(X_{n+1}=j\mid X_n=i).
$$

donde $i$ corresponde al estado de origen, y $j$ al estado destino.

<div class="bridge">

Cada enlace $i\to j$ del diagrama de estados se convierte en una entrada $p_{ij}$. Al reunir todas las conexiones según su origen y destino, obtenemos la matriz de transición $P$.

</div>

---

## Construcción de la matriz de transición

Supongamos que la cadena tiene $m$ estados. La **matriz de transición** reúne todas las probabilidades de pasar desde un estado hacia otro:

$$
P=(p_{ij})_{m\times m}
=
\begin{bmatrix}
p_{00}&p_{01}&\cdots&p_{0,m-1}\\
p_{10}&p_{11}&\cdots&p_{1,m-1}\\
\vdots&\vdots&\ddots&\vdots\\
p_{m-1,0}&p_{m-1,1}&\cdots&p_{m-1,m-1}
\end{bmatrix}.
$$

Cada fila corresponde a un estado de origen y sus entradas deben sumar $1$:

$$
p_{ij}\geq0,
\qquad
\sum_jp_{ij}=1.
$$

> **fila = origen, columna = destino.**

En una DTMC homogénea, esta matriz es la misma en todos los pasos.

---

## Ejemplo: Servicio reparable - Matriz de transición del servicio

Aplicamos la matriz teórica al servicio reparable y usamos el orden de estados

$$
(0,1)=(\text{caído},\text{operativo}).
$$

Desde el estado $0$:

$$
P(0\to0)=0.6,
\qquad
P(0\to1)=0.4.
$$

Desde el estado $1$:

$$
P(1\to0)=0.1,
\qquad
P(1\to1)=0.9.
$$

Por tanto,

$$
\boxed{
P=
\begin{bmatrix}
0.6&0.4\\
0.1&0.9
\end{bmatrix}}
$$

---

## Cómo leer la matriz

$$
P=
\begin{bmatrix}
0.6&0.4\\
0.1&0.9
\end{bmatrix}
$$

Por ejemplo,

$$
p_{01}=0.4
$$

significa:

> Si el servicio está caído ahora, existe probabilidad $0.4$ de encontrarlo operativo en la próxima observación.

Mientras que

$$
p_{11}=0.9
$$

es la probabilidad de permanecer operativo.

---

## Verificación de la matriz

Cada fila debe representar una distribución de probabilidad:

$$
p_{ij}\geq0
$$

y

$$
\sum_j p_{ij}=1.
$$

En nuestro ejemplo:

$$
0.6+0.4=1,
\qquad
0.1+0.9=1.
$$

<div class="warn">

Si usamos vectores fila, **las filas de la matriz de transición suman uno**. Mantendremos esta convención durante toda la clase.

</div>

---

## Uso de la matriz de transición

La matriz $P$ permite responder directamente preguntas de **un paso**.

Por ejemplo:

> Si el servicio está caído, ¿cuál es la probabilidad de estar operativo dentro del siguiente minuto (tiempo)?

$$
P(X_1=1\mid X_0=0)=p_{01}=0.4.
$$

Pero ¿qué ocurre si queremos mirar dos, diez o cien pasos hacia adelante?

---

## Dos pasos: pensar en trayectorias

Partiendo desde $0$, para estar en $1$ después de dos pasos existen dos posibilidades:

$$
0\to0\to1
$$

o

$$
0\to1\to1.
$$

Por tanto,

$$
P(X_2=1\mid X_0=0)
=
(0.6)(0.4)+(0.4)(0.9).
$$

Así,

$$
\boxed{P(X_2=1\mid X_0=0)=0.60}
$$

---

## Dos caminos

<img class="diagram" src="images/dos-pasos-trayectorias.png" alt="Dos trayectorias de dos pasos desde el estado 0 hasta el estado 1">

Las ramas curvas corresponden a $0\to0\to1$ y $0\to1\to1$. Sus probabilidades se multiplican y luego se suman.

---

## ¿Por qué se suman esas trayectorias?

Los dos caminos $0\to0\to1$ y $0\to1\to1$ son mutuamente excluyentes.

Sus probabilidades son

$$
0.6\cdot0.4=0.24 \text{ y } 0.4\cdot0.9=0.36.
$$

Entonces:

$$
0.24+0.36=0.60.
$$

---

## Multiplicación matricial para trayectorias

Al calcular

$$
P^2=P\,P
$$

se obtiene

$$
P^2=
\begin{bmatrix}
0.40&0.60\\
0.15&0.85
\end{bmatrix}.
$$

---

## Multiplicación matricial para trayectorias

En general, cada entrada de $P^2$ se calcula como

$$
(P^2)_{ij}=\sum_k p_{ik}p_{kj}.
$$

El índice $k$ recorre los posibles estados intermedios. Por eso, para ir de $0$ a $1$ en dos pasos:

$$
(P^2)_{01}=p_{00}p_{01}+p_{01}p_{11}
=(0.6)(0.4)+(0.4)(0.9)=0.60.
$$

Cada producto representa una trayectoria posible y la suma reúne todas las trayectorias.

---

## Probabilidades en $n$ pasos

Definimos

$$
p_{ij}^{(n)}
=
P(X_n=j\mid X_0=i).
$$

Entonces:

$$
\boxed{p_{ij}^{(n)}=(P^n)_{ij}}
$$

La matriz $P$ describe un paso.

La matriz $P^n$ describe $n$ pasos.

---

## Descomposición de una transición - Chapman Kolmogorov

El cálculo anterior dividió dos pasos en pasos independientes para cada trayectoria. La misma idea se puede aplicar a cualquier recorrido de $n+r$ pasos: primero observamos los $n$ pasos iniciales y luego los $r$ pasos restantes.

Si el sistema pasa por el estado intermedio $k$ al cabo de los primeros $n$ pasos, entonces:

- $p_{ik}^{(n)}$ es la probabilidad de ir de $i$ a $k$ en los primeros $n$ pasos.
- $p_{kj}^{(r)}$ es la probabilidad de ir de $k$ a $j$ en los siguientes $r$ pasos.

---

## Descomposición de una transición - Chapman Kolmogorov

Como el estado intermedio puede ser cualquiera, sumamos sobre todos los valores posibles de $k$:

$$
p_{ij}^{(n+r)}
=
\sum_k
p_{ik}^{(n)}
p_{kj}^{(r)}.
$$

En forma matricial:

$$
\boxed{P^{n+r}=P^nP^r}
$$

<div class="bridge">

Esta identidad se conoce como **ecuación de Chapman-Kolmogorov**. La multiplicación de matrices la expresa de forma compacta.

</div>

---

## ¿Por qué es útil Chapman Kolmogorov?

La descomposición permite pasar de una probabilidad de un paso a probabilidades en horizontes más largos:

- Divide un recorrido largo en etapas más simples
- Evita enumerar manualmente todas las trayectorias posibles
- Permite reutilizar cálculos como $P^n$ y $P^r$
- Conecta las transiciones locales con la evolución global del sistema

<div class="callout">

Esta idea es la base para calcular distribuciones futuras y analizar sistemas durante muchos pasos.

</div>

---

## Ejemplo: Servicio reparable - Chapman Kolmogorov

Queremos calcular la probabilidad de que el servicio esté operativo después de cuatro minutos, sabiendo que comenzó caído:

$$
P(X_4=1\mid X_0=0)=p_{01}^{(4)}.
$$

La matriz de transición de un paso es

$$
P=
\begin{bmatrix}
0.6&0.4\\
0.1&0.9
\end{bmatrix}.
$$

---

## Ejemplo: Servicio reparable - Chapman Kolmogorov

Separamos los cuatro pasos como $4=2+2$. Calculamos una sola vez la matriz de dos pasos:

$$
P^2=P\,P=
\begin{bmatrix}
0.40&0.60\\
0.15&0.85
\end{bmatrix}.
$$

El estado intermedio después de los primeros dos pasos puede ser $0$ o $1$:

$$
\begin{aligned}
 p_{01}^{(4)}
&=p_{00}^{(2)}p_{01}^{(2)}+p_{01}^{(2)}p_{11}^{(2)}\\
&=(0.40)(0.60)+(0.60)(0.85)\\
&=0.75.
\end{aligned}
$$

En forma matricial, reutilizamos $P^2$:

$$
P^4=P^2P^2,
\qquad
(P^4)_{01}=0.75.
$$
