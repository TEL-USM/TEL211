---
marp: true
math: mathjax
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

## Ruta

$$
\boxed{
\text{proceso}
\rightarrow
\text{estado}
\rightarrow
\text{transición}
\rightarrow
P
\rightarrow
P^n
\rightarrow
\text{transiente}
\rightarrow
\text{estacionario}
}
$$

Luego aplicaremos esta ruta a un sistema reparable.

<div class="bridge">

Pregunta guía: **si conozco el estado actual, ¿qué puedo decir sobre los estados futuros?**

</div>

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

Una **cadena de Markov en tiempo discreto** se denomina **DTMC**, por *Discrete-Time Markov Chain*.

Una DTMC observa el sistema en pasos:

$$
n=0,1,2,\ldots
$$

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

Definir correctamente el estado es una decisión de modelación, no solo una cuestión de notación.

</div>

---

## ¿Cuánta información debe contener el estado?

| Situación | Estado razonable |
|---|---|
| servidor sin envejecimiento explícito | operativo / fallado |
| dos servidores idénticos | número de servidores operativos |
| enlace con calidad persistente | calidad actual |
| componente cuyo riesgo depende de la edad | condición + edad |

<div class="warn">

Si el futuro depende de una variable que hemos omitido del estado, el modelo puede dejar de ser Markoviano.

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

## Ejemplo guía: un servicio reparable

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

## Diagrama de estados

Las transiciones principales son

$$
\boxed{0}
\xrightleftharpoons[p=0.1]{r=0.4}
\boxed{1}
$$

pero también es posible permanecer en el mismo estado:

$$
P(0\to0)=1-r=0.6
$$

$$
P(1\to1)=1-p=0.9.
$$

<div class="callout">

Desde cada estado debemos considerar **todas las posibilidades del siguiente paso**.

</div>

---

## Probabilidad de transición

Definimos

$$
p_{ij}=P(X_{n+1}=j\mid X_n=i).
$$

La lectura es siempre:

$$
\boxed{\text{fila }i=\text{estado de origen}}
$$

$$
\boxed{\text{columna }j=\text{estado de destino}}
$$

Una forma útil de recordarlo es:

> **fila = desde, columna = hacia.**

---

## Construcción de la matriz

Usamos el orden de estados

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

## Una transición responde una pregunta local

La matriz $P$ permite responder directamente preguntas de **un paso**.

Por ejemplo:

> Si el servicio está caído, ¿cuál es la probabilidad de estar operativo dentro de un minuto?

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

## ¿Por qué se suman esas trayectorias?

Los dos caminos

$$
0\to0\to1
$$

y

$$
0\to1\to1
$$

son mutuamente excluyentes.

Sus probabilidades son

$$
0.6\cdot0.4=0.24
$$

y

$$
0.4\cdot0.9=0.36.
$$

Entonces:

$$
0.24+0.36=0.60.
$$

<div class="callout">

La multiplicación sigue una trayectoria.  
La suma reúne trayectorias alternativas.

</div>

---

## La multiplicación matricial hace ese cálculo

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

La entrada

$$
(P^2)_{01}=0.60
$$

contiene exactamente la suma de todas las trayectorias de dos pasos desde $0$ hasta $1$.

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

## Chapman Kolmogorov

Para ir de $i$ a $j$ en $n+r$ pasos, podemos separar el recorrido mediante un estado intermedio $k$:

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

La multiplicación de matrices es una forma compacta de sumar todos los caminos posibles.

</div>

---

## ¿Y si no sabemos exactamente el estado inicial?

Podemos representar la incertidumbre inicial mediante un vector fila:

$$
\boldsymbol\alpha^{(0)}
=
\begin{bmatrix}
P(X_0=0)&P(X_0=1)
\end{bmatrix}.
$$

Por ejemplo,

$$
\boldsymbol\alpha^{(0)}
=
\begin{bmatrix}
0.3&0.7
\end{bmatrix}
$$

significa que inicialmente existe $30\%$ de probabilidad de estar caído y $70\%$ de estar operativo.

---

## Distribución transiente

Después de un paso:

$$
\boldsymbol\alpha^{(1)}
=
\boldsymbol\alpha^{(0)}P.
$$

Después de $n$ pasos:

$$
\boxed{
\boldsymbol\alpha^{(n)}
=
\boldsymbol\alpha^{(0)}P^n
}
$$

Cada componente

$$
\alpha_j^{(n)}
$$

es la probabilidad de encontrar el sistema en el estado $j$ en el paso $n$.

---

## Ejemplo transiente

Suponga que el servicio comienza caído:

$$
\boldsymbol\alpha^{(0)}
=
\begin{bmatrix}
1&0
\end{bmatrix}.
$$

Después de un paso:

$$
\boldsymbol\alpha^{(1)}
=
\begin{bmatrix}
1&0
\end{bmatrix}
P
=
\begin{bmatrix}
0.6&0.4
\end{bmatrix}.
$$

La probabilidad de estar operativo después de un minuto es $0.4$.

---

## Dos pasos

Como

$$
P^2=
\begin{bmatrix}
0.40&0.60\\
0.15&0.85
\end{bmatrix},
$$

entonces

$$
\boldsymbol\alpha^{(2)}
=
\begin{bmatrix}
1&0
\end{bmatrix}P^2
=
\begin{bmatrix}
0.40&0.60
\end{bmatrix}.
$$

La probabilidad de estar operativo después de dos minutos aumentó a

$$
\boxed{0.60}.
$$

---

## Tres preguntas distintas

| Pregunta | Objeto |
|---|---|
| Si estoy en $i$, ¿qué ocurre en el próximo paso? | $p_{ij}$ |
| Si parto en $i$, ¿dónde estaré después de $n$ pasos? | $p_{ij}^{(n)}$ |
| Si mi estado inicial es incierto, ¿cómo se distribuye el sistema después de $n$ pasos? | $\boldsymbol\alpha^{(n)}$ |

<div class="callout">

Todas utilizan la misma matriz de transición, pero responden preguntas diferentes.

</div>

---

## ¿Qué ocurre después de muchos pasos?

Hasta ahora hemos preguntado por tiempos específicos:

$$
n=1,\,2,\,10,\ldots
$$

Pero también podemos preguntar:

> Si el sistema opera durante mucho tiempo, ¿qué fracción de las observaciones estará en cada estado?

Esto conduce al comportamiento **estacionario**.

---

## Distribución estacionaria

Una distribución

$$
\boldsymbol\pi
=
\begin{bmatrix}
\pi_0&\pi_1&\cdots
\end{bmatrix}
$$

es estacionaria si permanece igual después de una transición:

$$
\boxed{\boldsymbol\pi=\boldsymbol\pi P}
$$

con

$$
\sum_i\pi_i=1,
\qquad
\pi_i\geq0.
$$

---

## Interpretación de $\boldsymbol\pi$

Si el sistema ya tiene distribución $\boldsymbol\pi$,

$$
\boldsymbol\pi P=\boldsymbol\pi.
$$

Por tanto, una nueva transición no modifica la distribución global.

<div class="callout">

La probabilidad asociada a un estado puede interpretarse como su fracción de largo plazo cuando la cadena cumple las condiciones adecuadas de convergencia.

</div>

---

## Resolver una cadena de dos estados

Considere

$$
P=
\begin{bmatrix}
1-r&r\\
p&1-p
\end{bmatrix}.
$$

En equilibrio, el flujo promedio entre los dos estados debe compensarse:

$$
\pi_0r=\pi_1p.
$$

Además,

$$
\pi_0+\pi_1=1.
$$

---

## Solución general

A partir de

$$
\pi_0r=\pi_1p
$$

y

$$
\pi_0+\pi_1=1,
$$

se obtiene

$$
\boxed{
\pi_0=\frac{p}{p+r}
}
$$

y

$$
\boxed{
\pi_1=\frac{r}{p+r}
}.
$$

---

## Ejemplo del servicio

Con

$$
p=0.1,
\qquad
r=0.4,
$$

se obtiene

$$
\pi_0=\frac{0.1}{0.1+0.4}=0.2
$$

y

$$
\pi_1=\frac{0.4}{0.1+0.4}=0.8.
$$

Por tanto,

$$
\boxed{
\boldsymbol\pi=
\begin{bmatrix}
0.2&0.8
\end{bmatrix}}
$$

---

## Interpretación

A largo plazo:

- el servicio está caído aproximadamente el $20\%$ de las observaciones.
- está operativo aproximadamente el $80\%$.

<div class="callout">

La distribución estacionaria no describe una misión sin fallas. Permite fallar, recuperarse y volver a operar repetidamente.

</div>

Esto ya se parece mucho más a una pregunta de **disponibilidad**.

---

## Transiente y estacionario

Partiendo caído:

$$
\boldsymbol\alpha^{(0)}=[1\,0].
$$

Luego:

$$
\alpha_1^{(1)}=0.40,
$$

$$
\alpha_1^{(2)}=0.60,
$$

y, después de muchos pasos,

$$
\alpha_1^{(n)}\longrightarrow0.80.
$$

<div class="bridge">

El transiente conserva información sobre cómo comenzó el sistema.  
El estacionario describe su comportamiento de largo plazo.

</div>

---

## ¿Toda distribución estacionaria es un límite?

No necesariamente.

Considere

$$
P=
\begin{bmatrix}
0&1\\
1&0
\end{bmatrix}.
$$

La cadena alterna obligatoriamente:

$$
0\to1\to0\to1\to\cdots
$$

Existe una distribución estacionaria:

$$
\boldsymbol\pi=
\begin{bmatrix}
0.5&0.5
\end{bmatrix},
$$

pero si comenzamos en $0$, la distribución transiente oscila.

---

## Condiciones de convergencia

Para una cadena finita, dos propiedades importantes son:

**Irreducibilidad**

Desde cualquier estado es posible alcanzar eventualmente cualquier otro estado.

**Aperiodicidad**

La cadena no está obligada a regresar siguiendo únicamente un ciclo de longitud fija.

<div class="callout">

Una cadena finita, irreducible y aperiódica converge hacia una única distribución estacionaria desde cualquier distribución inicial.

</div>

---

## Tres conceptos que no deben confundirse

| Pregunta | Respuesta |
|---|---|
| ¿Dónde estará en el paso $n$? | $\boldsymbol\alpha^{(n)}$ |
| ¿Qué distribución no cambia al aplicar $P$? | $\boldsymbol\pi$ |
| ¿Hacia dónde converge $\boldsymbol\alpha^{(n)}$? | depende de la estructura de la cadena |

<div class="warn">

Estacionario e infinito no son sinónimos. Primero se define la distribución estacionaria. Después se estudia si el transiente converge hacia ella.

</div>

---

## De estados a disponibilidad

Sea

$$
\mathcal U
$$

el conjunto de estados que cumplen el nivel de servicio requerido.

Entonces la disponibilidad en el paso $n$ es

$$
\boxed{
A_n=\sum_{i\in\mathcal U}\alpha_i^{(n)}
}.
$$

Si existe régimen estacionario:

$$
\boxed{
A_\infty=\sum_{i\in\mathcal U}\pi_i
}.
$$

---

## Disponibilidad del servicio

En nuestro ejemplo,

$$
\mathcal U=\{1\}.
$$

Si comienza caído:

$$
A_1=0.40,
$$

$$
A_2=0.60.
$$

A largo plazo:

$$
\boxed{A_\infty=\pi_1=0.80}.
$$

<div class="callout">

La disponibilidad depende de qué estados consideramos aceptables para prestar el servicio.

</div>

---

## Un estado degradado puede ser suficiente... o no

Suponga ahora tres estados:

$$
0=\text{caído},
\qquad
1=\text{degradado},
\qquad
2=\text{normal}.
$$

Si el SLA acepta operación degradada:

$$
\mathcal U=\{1,2\}.
$$

Si exige capacidad completa:

$$
\mathcal U=\{2\}.
$$

<div class="bridge">

La cadena describe estados. El requisito de servicio determina cuáles cuentan como disponibles.

</div>

---

## Dos componentes reparables

Ahora considere dos componentes idénticos.

Cada minuto:

- un componente operativo falla con probabilidad $p$.
- un componente fallado se repara con probabilidad $r$.

En lugar de representar cada configuración por separado, definimos

$$
X_n=\text{número de componentes operativos}.
$$

Por tanto,

$$
X_n\in\{0,1,2\}.
$$

---

## ¿Por qué basta contar componentes?

Las configuraciones físicas son:

| Componentes | Estado agregado |
|---|---|
| $(0,0)$ | $0$ |
| $(1,0)$ o $(0,1)$ | $1$ |
| $(1,1)$ | $2$ |

Podemos agrupar $(1,0)$ y $(0,1)$ porque:

- los componentes son idénticos.
- tienen las mismas probabilidades $p$ y $r$.
- el servicio depende solamente de cuántos están operativos.

---

## Supuestos de actualización

Durante un intervalo:

- cada componente operativo puede fallar.
- cada componente fallado puede repararse.
- los eventos son independientes entre componentes.
- observamos el nuevo estado al final del intervalo.

<div class="warn">

Las cantidades p y r son **probabilidades por intervalo**, no tasas de tiempo continuo.

</div>

---

## Transiciones desde el estado 2

Si ambos componentes están operativos:

$$
P(2\to2)=(1-p)^2
$$

$$
P(2\to1)=2p(1-p)
$$

$$
P(2\to0)=p^2.
$$

El factor $2$ aparece porque cualquiera de los dos componentes puede ser el que falle.

---

## Transiciones desde el estado 0

Si ambos componentes están fallados:

$$
P(0\to0)=(1-r)^2
$$

$$
P(0\to1)=2r(1-r)
$$

$$
P(0\to2)=r^2.
$$

Ahora el factor $2$ cuenta cuál de los dos componentes es reparado.

---

## Transiciones desde el estado 1

Hay un componente operativo y uno fallado.

Para terminar con cero operativos:

$$
P(1\to0)=p(1-r).
$$

Para terminar con dos:

$$
P(1\to2)=(1-p)r.
$$

---

## Permanecer en el estado 1

Existen **dos formas** de continuar con un componente operativo.

Nada cambia:

$$
(1-p)(1-r).
$$

O el operativo falla mientras el otro se repara:

$$
pr.
$$

Por tanto,

$$
\boxed{
P(1\to1)
=
(1-p)(1-r)+pr
}
$$

<div class="callout">

Un mismo estado final puede obtenerse mediante varios eventos distintos.

</div>

---

<!-- _class: compact -->

## Matriz del sistema reparable

Con el orden

$$
(0,1,2),
$$

la matriz es

$$
P=
\begin{bmatrix}
(1-r)^2&2r(1-r)&r^2\\
p(1-r)&(1-p)(1-r)+pr&(1-p)r\\
p^2&2p(1-p)&(1-p)^2
\end{bmatrix}.
$$

Cada fila describe todas las posibilidades partiendo desde un número determinado de componentes operativos.

---

## Ejemplo numérico

Utilicemos nuevamente

$$
p=0.1,
\qquad
r=0.4.
$$

Entonces:

$$
P=
\begin{bmatrix}
0.36&0.48&0.16\\
0.06&0.58&0.36\\
0.01&0.18&0.81
\end{bmatrix}.
$$

Verificación:

$$
\sum_jp_{ij}=1
$$

para cada fila.

---

## Régimen estacionario

Resolviendo

$$
\boldsymbol\pi=\boldsymbol\pi P
$$

con

$$
\pi_0+\pi_1+\pi_2=1,
$$

se obtiene

$$
\boxed{
\boldsymbol\pi=
\begin{bmatrix}
0.04&0.32&0.64
\end{bmatrix}
}.
$$

---

## ¿Qué significa ese resultado?

A largo plazo:

$$
P(X=0)=0.04,
$$

$$
P(X=1)=0.32,
$$

$$
P(X=2)=0.64.
$$

Si basta **al menos un componente operativo**:

$$
A_\infty=\pi_1+\pi_2
=0.32+0.64
=\boxed{0.96}.
$$

---

## El mismo sistema, dos niveles de servicio

Si se requiere al menos un componente:

$$
\mathcal U=\{1,2\}
$$

y

$$
A_\infty=0.96.
$$

Si se exige capacidad completa:

$$
\mathcal U=\{2\}
$$

y

$$
A_\infty=0.64.
$$

<div class="callout">

La cadena es la misma. Lo que cambia es la definición de servicio aceptable.

</div>

---

## ¿Qué agregó Markov respecto del RBD?

El RBD responde:

> ¿Qué componentes deben sobrevivir durante una misión?

La DTMC permite además:

- fallar.
- repararse.
- visitar estados degradados.
- volver a operar.
- calcular probabilidades en instantes futuros.
- estudiar el régimen de largo plazo.

<div class="bridge">

Confiabilidad y disponibilidad son preguntas diferentes porque permiten historias temporales diferentes.

</div>

---

## Una nueva pregunta

Sabemos calcular

$$
p_{ij}^{(n)}
=
P(X_n=j\mid X_0=i).
$$

Pero esta probabilidad permite que el sistema haya visitado $j$ antes.

A veces queremos preguntar:

> ¿Cuál es la probabilidad de llegar al estado $j$ **por primera vez** exactamente en el paso $n$?

---

## Tiempo de primera visita

Definimos

$$
T_j=
\min\{n\geq1:X_n=j\}.
$$

La probabilidad de primera visita es

$$
\boxed{
f_{ij}^{(n)}
=
P(T_j=n\mid X_0=i)
}.
$$

En general, estas probabilidades no coinciden.

$$
f_{ij}^{(n)}\neq p_{ij}^{(n)}.
$$

<div class="warn">

La matriz de n pasos incluye trayectorias que pudieron visitar el estado j anteriormente.

</div>

---

## Primer retorno

Si comenzamos en el mismo estado al que queremos volver:

$$
X_0=i,
$$

definimos

$$
T_i^+
=
\min\{n\geq1:X_n=i\}.
$$

El símbolo $+$ indica que el estado inicial en $n=0$ **no cuenta como retorno**.

---

## Ejemplo de primer retorno

Volvamos al servicio de dos estados:

$$
0=\text{caído},
\qquad
1=\text{operativo}.
$$

Para regresar **por primera vez** a $0$ en el paso $2$, la trayectoria debe ser:

$$
0\to1\to0.
$$

Por tanto,

$$
P(T_0^+=2\mid X_0=0)
=
rp.
$$

Con $r=0.4$ y $p=0.1$:

$$
\boxed{0.04}.
$$

---

## ¿Por qué no usamos $(P^2)_{00}$?

Después de dos pasos:

$$
(P^2)_{00}=0.40.
$$

Ese valor incluye, entre otras, la trayectoria

$$
0\to0\to0.
$$

Pero esa trayectoria ya regresó a $0$ en el paso $1$.

<div class="callout">

La potencia n de la matriz P responde **dónde estamos**.  
El primer retorno agrega la condición **sin haber regresado antes**.

</div>

---

## Primer retorno en el paso 3

Para volver por primera vez a $0$ en el paso $3$, debemos seguir

$$
0\to1\to1\to0.
$$

Por tanto,

$$
P(T_0^+=3\mid X_0=0)
=
r(1-p)p.
$$

Con

$$
r=0.4,\qquad p=0.1,
$$

se obtiene

$$
\boxed{0.036}.
$$

---

<!-- _class: compact -->

## Extensión: recurrencia de primera visita

Las probabilidades de varios pasos pueden descomponerse según el instante de la primera llegada a $j$:

$$
p_{ij}^{(n)}
=
\sum_{k=1}^{n}
f_{ij}^{(k)}
p_{jj}^{(n-k)},
\qquad
p_{jj}^{(0)}=1.
$$

Por tanto,

$$
f_{ij}^{(n)}
=
p_{ij}^{(n)}
-
\sum_{k=1}^{n-1}
f_{ij}^{(k)}
p_{jj}^{(n-k)}.
$$

<div class="bridge">

La idea es restar las trayectorias que llegaron antes al estado j.

</div>

---

## Errores frecuentes

| Error | Qué ocurrió |
|---|---|
| las columnas suman $1$ | se mezclaron convenciones de vectores fila y columna |
| falta $p_{ii}$ | se olvidó la posibilidad de permanecer en el estado |
| interpretar $P^n$ como primera visita | se permiten visitas anteriores |
| resolver $P\pi=\pi$ | aquí usamos $\pi$ como vector fila |
| llamar estacionario a $\alpha^{(n)}$ | se confundió un instante finito con el régimen estacionario |
| usar $p$ como si fuera una tasa | $p$ es una probabilidad asociada a un intervalo |

---

## Ejercicio integrado

Un enlace se observa cada minuto y puede estar:

$$
0=\text{malo},
\qquad
1=\text{bueno}.
$$

Si está malo, mejora con probabilidad $0.4$.

Si está bueno, empeora con probabilidad $0.1$.

1. Construya el diagrama y la matriz $P$.
2. Si parte malo, calcule la probabilidad de estar bueno después de dos minutos.
3. Obtenga la distribución estacionaria.
4. Interprete $\pi_1$.
5. Calcule la probabilidad de regresar por primera vez al estado malo en el paso $2$.

---

## Solución: modelo

Con el orden $(0,1)$:

$$
P=
\begin{bmatrix}
0.6&0.4\\
0.1&0.9
\end{bmatrix}.
$$

Cada fila suma $1$.

La primera fila describe qué ocurre partiendo desde estado malo.

La segunda describe qué ocurre partiendo desde estado bueno.

---

## Solución: dos pasos

Calculamos

$$
P^2=
\begin{bmatrix}
0.40&0.60\\
0.15&0.85
\end{bmatrix}.
$$

Como parte malo:

$$
\boldsymbol\alpha^{(0)}
=
\begin{bmatrix}
1&0
\end{bmatrix}.
$$

Entonces:

$$
\boldsymbol\alpha^{(2)}
=
\begin{bmatrix}
0.40&0.60
\end{bmatrix}.
$$

La probabilidad solicitada es

$$
\boxed{0.60}.
$$

---

## Solución: largo plazo

El balance es

$$
0.4\pi_0=0.1\pi_1
$$

con

$$
\pi_0+\pi_1=1.
$$

Por tanto,

$$
\boxed{
\boldsymbol\pi=
\begin{bmatrix}
0.2&0.8
\end{bmatrix}}
$$

y el enlace permanece en estado bueno aproximadamente el

$$
\boxed{80\%}
$$

de las observaciones de largo plazo.

---

## Solución: primer retorno

Para regresar por primera vez al estado malo en el paso $2$:

$$
0\to1\to0.
$$

Por tanto,

$$
P(T_0^+=2\mid X_0=0)
=
(0.4)(0.1)
=
\boxed{0.04}.
$$

No incluimos

$$
0\to0\to0
$$

porque ya regresó al estado $0$ en el primer paso.

---

<!-- _class: compact -->

## Actividad

Cada segundo, una tarjeta reporta:

$$
0=\text{pobre},\quad
1=\text{regular},\quad
2=\text{buena},\quad
3=\text{excelente}.
$$

Las transiciones son:

- desde $0$: $0.50$ a $0$ y $0.50$ a $1$.
- desde $1$: $0.04$ a $0$, $0.90$ a $1$ y $0.06$ a $2$.
- desde $2$: $0.04$ a $0$, $0.90$ a $2$ y $0.06$ a $3$.
- desde $3$: $(0.04,0.02,0.04,0.90)$.

Construya $P$ y verifique el modelo.

---

<!-- _class: compact -->

## Solución de la actividad

Con el orden $(0,1,2,3)$:

$$
P=
\begin{bmatrix}
0.50&0.50&0&0\\
0.04&0.90&0.06&0\\
0.04&0&0.90&0.06\\
0.04&0.02&0.04&0.90
\end{bmatrix}.
$$

Las cuatro filas suman $1$.

Las entradas diagonales representan la probabilidad de mantener la misma calidad durante el siguiente segundo.

---

## Qué debemos poder hacer ahora

Ante una DTMC:

1. definir qué significa cada estado.
2. fijar qué representa un paso.
3. construir y verificar $P$.
4. usar $P^n$ para probabilidades futuras.
5. usar $\boldsymbol\alpha^{(0)}P^n$ para el transiente.
6. resolver $\boldsymbol\pi=\boldsymbol\pi P$ para el régimen estacionario.
7. sumar estados aceptables para obtener disponibilidad.
8. distinguir $P^n$ de una probabilidad de primera visita.

---

## De tiempo discreto a tiempo continuo

En esta clase:

> durante cada intervalo ocurre una transición con cierta **probabilidad**.

Pero las fallas y reparaciones reales pueden ocurrir en cualquier instante.

En tiempo continuo aparecerán **tasas**:

$$
0
\xrightleftharpoons[\mu]{\lambda}
1.
$$

<div class="bridge">

La siguiente clase reemplazará la matriz de probabilidades P por una matriz generadora Q.

Los conceptos de **estado, transiente y estacionario** permanecerán.

</div>

---

## La conexión con el resto del curso

Hasta ahora:

$$
\text{distribuciones}
\rightarrow
R_i(t)
\rightarrow
R_{\text{sistema}}(t)
$$

Ahora:

$$
\text{estados}
\rightarrow
\text{transiciones}
\rightarrow
\text{evolución temporal}
$$

Luego:

$$
\text{CTMC}
\rightarrow
\text{fallas + reparaciones}
\rightarrow
\text{disponibilidad}.
$$

<div class="callout">

Pasamos de preguntar **si una misión sobrevive** a preguntar **cómo se comporta un sistema que puede fallar y recuperarse repetidamente**.

</div>

---

## Referencias

- K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017, caps. 7 y 8.
- W. J. Stewart, *Probability, Markov Chains, Queues, and Simulation*, Princeton University Press, 2009, caps. 3 y 5.
- J. M. Martínez, *TEL211: Stochastic Processes*, material histórico USM.
- Certámenes TEL211 2022 y 2023, problemas de cadenas de Markov en tiempo discreto.
