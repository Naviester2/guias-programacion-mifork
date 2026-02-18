<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

### Respuesta

En POO, la **encapsulación** busca agrupar en una misma unidad (la clase) el **estado** (atributos) y el **comportamiento** (métodos) que opera sobre ese estado. La **ocultación de información** (information hiding) persigue que los detalles internos (cómo se representa y gestiona el estado) no queden expuestos a quien usa la clase; en su lugar, se ofrece un conjunto controlado de operaciones públicas.

Las ventajas típicas de ocultar información son: reducción del acoplamiento (las otras clases dependen menos de detalles internos), capacidad de cambiar la implementación sin romper el código cliente, protección frente a usos incorrectos del estado (se puede validar y mantener invariantes), y mejora de la mantenibilidad y la comprensión (se separa “qué hace” de “cómo lo hace”). También facilita localizar errores porque el estado sólo puede cambiarse a través de puntos controlados.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

### Respuesta

La **interfaz pública** de una clase es el conjunto de miembros accesibles desde fuera: normalmente, sus **constructores públicos** y sus **métodos públicos** (y, si existieran, campos públicos). Es, en la práctica, el “contrato” que ofrece la clase: qué operaciones se pueden invocar y qué garantías da sobre su comportamiento.

La ocultación de información se apoya directamente en esa idea: se decide qué parte de la clase se expone como interfaz pública y qué parte se mantiene privada. De este modo, el código externo se ve obligado a interactuar con el objeto a través de esa interfaz, sin depender de la representación interna, lo que permite cambiarla (por ejemplo, cambiar dos atributos por un array interno) sin que tenga que cambiar el código cliente.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

### Respuesta

La interfaz pública debe diseñarse con cuidado porque es la parte que usarán otras clases, y por tanto crea dependencias. Si se expone “de más” (por ejemplo, campos o setters sin necesidad), se incrementa el acoplamiento y se vuelve más difícil mantener invariantes; si se expone “de menos”, se dificulta el uso legítimo de la clase y se empuja a soluciones incorrectas.

Cambiar la interfaz pública **no suele ser fácil** una vez que hay código que la utiliza: renombrar métodos, cambiar firmas o eliminar miembros públicos suele romper a los clientes (igual que cambiar la firma de una función en C rompe las llamadas). Por ello, una interfaz pública estable tiende a evolucionar con compatibilidad hacia atrás (añadiendo métodos nuevos o sobrecargas) y minimizando cambios incompatibles.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

### Respuesta

Las **invariantes de clase** son condiciones que deben cumplirse siempre que un objeto está en un estado “válido”. Por ejemplo, en una clase que representa una fracción, una invariante típica sería “el denominador nunca es 0” o “la fracción está simplificada”. Estas condiciones permiten razonar sobre el comportamiento: si se asume que la invariante se cumple, los métodos pueden implementarse con menos casos especiales.

La ocultación de información ayuda a mantener invariantes porque evita que código externo modifique el estado arbitrariamente. Si los atributos se mantienen `private`, sólo los constructores y métodos de la propia clase pueden cambiarlos, y ahí se puede validar (rechazando valores inválidos) o normalizar (ajustando valores a una forma canónica). Así, se concentra el control del estado en pocos lugares, lo que reduce errores.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

### Respuesta

Un ejemplo de `Punto` puede ocultar sus coordenadas declarando los atributos como `private` y ofreciendo métodos públicos para operar con ellos. De esa forma, el resto del programa no necesita saber cómo se guardan `x` e `y`; sólo necesita poder construir puntos y pedirles cálculos como la distancia al origen.

La **interfaz pública** de `Punto` sería el conjunto de sus miembros `public`: en el ejemplo, el constructor y los métodos `getX`, `getY`, `calcularDistanciaAOrigen` y `calcularDistanciaAPunto`. La palabra clave `public` indica que el miembro es accesible desde cualquier otra clase; `private` indica que sólo es accesible desde el propio código de la clase `Punto`.

```java
public final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double getX() {
		return x;
	}

	public double getY() {
		return y;
	}

	public double calcularDistanciaAOrigen() {
		return Math.sqrt(x * x + y * y);
	}

	public double calcularDistanciaAPunto(Punto otro) {
		double dx = this.x - otro.x;
		double dy = this.y - otro.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}
```


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

### Respuesta

En Java, `public` y `private` se aplican a miembros y tipos según el contexto. Se pueden aplicar a **atributos (campos)**, **métodos** y **constructores** para controlar desde dónde se pueden usar. También se pueden aplicar a **clases internas (nested classes)**, interfaces internas y enumerados internos.

Para las **clases de nivel superior** (top-level) Java no permite `private`: una clase top-level puede ser `public` o tener visibilidad de paquete (sin modificador). En cambio, dentro de una clase, una clase interna sí puede declararse `private` para ocultarla totalmente a código externo.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

### Respuesta

Además de `public` y `private`, muchos lenguajes orientados a objetos ofrecen una visibilidad intermedia para facilitar la reutilización y la herencia sin exponerlo todo. Un caso típico es `protected`, que suele permitir acceso a clases derivadas, y mecanismos equivalentes a “visible sólo dentro del mismo módulo/paquete”.

En Java existen cuatro niveles: `public`, `protected`, **visibilidad de paquete** (sin modificador, accesible dentro del mismo paquete) y `private`. En otros lenguajes hay variaciones: C++ tiene `public/protected/private`; C# añade niveles como `internal` (similar a paquete/ensamblado) y combinaciones (`protected internal`, `private protected`). El objetivo común es controlar el acoplamiento y la exposición de detalles.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

### Respuesta

En Java, un miembro `private` está oculto para **otras clases** (a), pero **no** está oculto para otras instancias de la **misma clase** (b). Esto se debe a que el control de acceso se define por **clase**, no por “objeto individual”: cualquier método de `Punto` puede acceder a los `private` de cualquier otro `Punto`.

Eso permite implementar métodos como `calcularDistanciaAPunto(Punto otro)` usando directamente los campos privados de `otro`, sin necesidad de getters (aunque también podrían usarse). En el siguiente ejemplo, `otro.x` y `otro.y` son accesibles porque el código está dentro de la clase `Punto`.

```java
public double calcularDistanciaAPunto(Punto otro) {
	double dx = this.x - otro.x;
	double dy = this.y - otro.y;
	return Math.sqrt(dx * dx + dy * dy);
}
```


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta

Los métodos **getter** (accesores) y **setter** (mutadores) son métodos que permiten leer o modificar, respectivamente, un atributo que normalmente se mantiene `private`. Por ejemplo, `getX()` devuelve el valor de `x`, y `setX(double x)` podría cambiarlo. Se usan para que el acceso al estado pase por una interfaz controlada.

La idea importante es que un getter o un setter no son “sólo una forma distinta de hacer público un atributo”: al pasar por un método, se puede validar (rechazar valores inválidos), mantener invariantes, disparar actualizaciones relacionadas, o cambiar la representación interna sin afectar al código cliente. Aun así, no siempre conviene crear setters: una interfaz mínima suele ser más segura y más fácil de mantener.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta

En este contexto, “seguridad” suele referirse a **seguridad en el sentido de robustez/corrección**: reducir la probabilidad de que el programa entre en estados inválidos o se use mal una clase. Al ocultar el estado y obligar a usar métodos, se puede impedir que se asignen valores incoherentes y se puede garantizar que las invariantes se mantengan.

No se está hablando directamente de ciberseguridad (“que no pueda ser hackeado”), aunque una buena encapsulación puede contribuir indirectamente a reducir superficies de error. La encapsulación por sí sola no sustituye a medidas de seguridad reales (validación de entradas externas, autenticación, permisos, etc.), pero sí ayuda a que el código sea más predecible y menos vulnerable a fallos por mal uso interno.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta

Un **miembro de instancia** pertenece a cada objeto creado: cada `Punto` tiene su propio `x` e `y`. Un **miembro de clase** (en Java, un miembro `static`) pertenece a la clase como tal: existe una única copia compartida por todas las instancias, y se usa para información común (constantes, contadores globales de instancias, configuraciones compartidas, etc.).

Los miembros de clase también se pueden ocultar: `static` no implica “público”. Se puede declarar un campo `private static` para que sea compartido internamente pero inaccesible desde fuera, y exponerlo sólo mediante métodos públicos si conviene. Esto mantiene el mismo principio de encapsulación: controlar el acceso al estado, sea de instancia o de clase.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta

Sí, un constructor `private` tiene sentido cuando se quiere **controlar cómo se crean** los objetos. Por ejemplo, se puede impedir la creación directa y obligar a usar métodos factoría (`static`) que validen parámetros, apliquen reglas de negocio, redondeen valores o devuelvan instancias compartidas (caché).

También se usa para patrones como **Singleton** (una sola instancia), para **clases de utilidades** que no deben instanciarse (sólo tienen métodos `static`), o para tipos que representan un conjunto cerrado de instancias creadas internamente. En estos casos, hacer el constructor privado refuerza la intención y evita usos incorrectos.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

### Respuesta

En Java, los miembros de clase se indican con la palabra clave `static`. Eso significa que el miembro no depende de un objeto concreto, sino de la clase; por ejemplo, `Punto.getMaxX()` se puede invocar sin tener una instancia. Si se quiere mantener la ocultación, esos campos `static` pueden ser `private` y exponerse con métodos `public`.

En este ejemplo, se guardan `maxX` y `maxY` como estado de clase, y se actualizan cada vez que se crea un nuevo `Punto`. Para simplificar, se considera el máximo numérico (no el valor absoluto); si se quisieran máximos absolutos, se usaría `Math.abs` al actualizar.

```java
public final class Punto {
	private final double x;
	private final double y;

	private static double maxX = Double.NEGATIVE_INFINITY;
	private static double maxY = Double.NEGATIVE_INFINITY;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
		if (x > maxX) {
			maxX = x;
		}
		if (y > maxY) {
			maxY = y;
		}
	}

	public double getX() {
		return x;
	}

	public double getY() {
		return y;
	}

	public static double getMaxX() {
		return maxX;
	}

	public static double getMaxY() {
		return maxY;
	}

	public double calcularDistanciaAOrigen() {
		return Math.sqrt(x * x + y * y);
	}
}
```


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

### Respuesta

Un método factoría suele ser `public static` porque se invoca sobre la clase (por ejemplo, `Punto.crearRedondeado(...)`) y no sobre un objeto ya existente. De esa forma, se puede centralizar una regla de creación (aquí, redondear) sin duplicarla en el código cliente y sin exponer detalles internos.

En Java, para redondear al entero más cercano manteniendo el tipo `double` se puede usar `Math.rint`, que devuelve un `double` con el valor entero más cercano. Por tanto, sí se usa `static`.

```java
public static Punto crearRedondeado(double x, double y) {
	return new Punto(Math.rint(x), Math.rint(y));
}
```


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta

Cambiar la implementación interna sin cambiar la interfaz pública es una demostración directa de encapsulación. Si el código cliente sólo depende del constructor y de métodos como `getX()`, `getY()` o `calcularDistanciaAOrigen()`, entonces se puede reemplazar la representación interna (dos campos → un array) sin que el resto del programa tenga que modificarse.

Para lograrlo, se mantiene la misma “cara pública” (mismos constructores y métodos públicos) y sólo se cambia el almacenamiento: un `double[]` de longitud 2. El array se declara `private` para impedir que se escape una referencia mutable hacia fuera (si se devolviera el array, se rompería la ocultación).

```java
public final class Punto {
	private static final int IDX_X = 0;
	private static final int IDX_Y = 1;

	private final double[] coords = new double[2];

	public Punto(double x, double y) {
		coords[IDX_X] = x;
		coords[IDX_Y] = y;
	}

	public double getX() {
		return coords[IDX_X];
	}

	public double getY() {
		return coords[IDX_Y];
	}

	public double calcularDistanciaAOrigen() {
		double x = coords[IDX_X];
		double y = coords[IDX_Y];
		return Math.sqrt(x * x + y * y);
	}

	public double calcularDistanciaAPunto(Punto otro) {
		double dx = this.coords[IDX_X] - otro.coords[IDX_X];
		double dy = this.coords[IDX_Y] - otro.coords[IDX_Y];
		return Math.sqrt(dx * dx + dy * dy);
	}
}
```


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta

Aunque un atributo tuviera un getter y un setter públicos, declararlo público no suele ser mejor porque un campo público elimina la posibilidad de controlar el acceso en el futuro. Con métodos, se puede añadir validación, registrar cambios, mantener consistencia con otros campos, o incluso cambiar la representación interna sin que cambie el código cliente; con un campo público, cualquier acceso directo queda “fijado” y es más difícil de corregir sin romper compatibilidad.

La convención más habitual es declarar los atributos `private` (o como mínimo no públicos) y exponer sólo lo necesario mediante métodos. Esto se relaciona directamente con las invariantes de clase: si el estado sólo puede cambiar a través de puntos controlados (constructores/métodos), se puede garantizar que cada modificación preserva la invariante; si el estado es público, cualquier parte del programa puede violarla sin que la clase pueda evitarlo.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta

Una clase es **inmutable** si, tras construir un objeto, su estado observable no cambia. En Java esto suele implicar campos `private final`, ausencia de setters, y cuidado para no exponer referencias mutables internas. Si se necesita “cambiar” algo, se crea un nuevo objeto con los valores deseados (por ejemplo, un método `conX(double nuevaX)` que devuelve un nuevo `Punto`).

Un **método modificador** es cualquier método que cambia el estado interno del objeto (por ejemplo, `mover(dx, dy)`, `añadirElemento(...)`, `incrementar()`). No tiene por qué llamarse `set...`: un modificador puede tener un nombre de acción o de operación. La inmutabilidad tiene ventajas: facilita el razonamiento, reduce efectos laterales, es más segura en entornos concurrentes y permite compartir instancias sin miedo a que otra parte del programa las cambie.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta

No es recomendable incluir setters “por defecto” sin una necesidad real. Un setter público amplía la interfaz y permite cambios de estado en cualquier momento, lo que aumenta los estados posibles del objeto y complica mantener invariantes. En muchas clases, especialmente las que modelan conceptos con reglas (por ejemplo, una fecha válida), conviene limitar o incluso prohibir cambios arbitrarios.

La práctica habitual es exponer setters sólo cuando representan una operación válida del dominio y cuando se puede mantener la consistencia del objeto (validando y preservando invariantes). Cuando se desea flexibilidad sin setters indiscriminados, se pueden usar constructores bien definidos, métodos específicos de negocio (más expresivos que un `set...`) o, si interesa inmutabilidad, métodos que devuelven nuevas instancias.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta

En Java, `String` es **inmutable**: una vez creada una cadena, su contenido no cambia. Esto permite compartir instancias de forma segura y facilita optimizaciones internas (como el pool de strings). La consecuencia es que cualquier operación que “modifique” una cadena realmente crea otra.

Al concatenar dos cadenas (por ejemplo, `a + b`) se produce un **nuevo** objeto `String` con el resultado. Si se concatena repetidas veces en un bucle para construir una cadena larga, se pueden crear muchos objetos intermedios y degradar el rendimiento. En ese caso se debe usar `StringBuilder` (o `StringBuffer` si se necesita sincronización), añadiendo partes con `append(...)` y obteniendo el resultado final con `toString()`.


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

### Respuesta

En POO se distinguen dos nociones: **identidad** (si dos referencias apuntan al mismo objeto) y **igualdad por contenido** (si dos objetos representan el mismo valor/estado). Según el tipo, interesa una u otra: para objetos “valor” (como puntos, fechas, dinero) suele interesar comparar por contenido; para objetos con identidad propia (como una conexión abierta) suele interesar la identidad.

En Java, `equals` es el método para expresar igualdad lógica. Por defecto, `Object.equals` se comporta como `==` (compara identidad). Si se quiere comparar por contenido, se debe **sobrescribir** `equals` (y coherentemente `hashCode`). Para cadenas, se deben comparar con `a.equals(b)` (o `Objects.equals(a, b)` si puede haber `null`), no con `==`, porque `==` compara referencias.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

### Respuesta

Las clases **wrapper** (envoltorio) son clases que representan como objetos a tipos primitivos. En Java existen `Integer` para `int`, `Double` para `double`, `Boolean` para `boolean`, etc. Esto permite tratar valores primitivos como objetos cuando el lenguaje o las bibliotecas lo requieren (por ejemplo, en colecciones genéricas como `List<Integer>`).

En Java, el envoltorio puede hacerse manualmente (por ejemplo, `Integer.valueOf(5)`) y también de forma automática mediante **autoboxing/unboxing** (el compilador inserta conversiones entre primitivo y wrapper cuando procede). Las ventajas incluyen poder usar genéricos, disponer de métodos utilitarios (parseo, constantes, comparaciones), y poder representar ausencia de valor con `null` (aunque esto también puede ser una fuente de `NullPointerException`). No todos los lenguajes OO tienen primitivos separados: en algunos (por ejemplo, Python) todo es objeto y no se necesita este mecanismo.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta

Un **tipo enumerado** (enum) representa un conjunto finito y cerrado de valores con nombre (por ejemplo, los días de la semana o los meses). En vez de usar enteros “mágicos” (1..12) o cadenas, se usa un tipo específico que sólo admite esos valores, lo que mejora la expresividad y evita errores.

En Java, un `enum` es, efectivamente, una forma especial de **clase**: puede tener campos, métodos y un constructor (implícitamente privado). En términos de encapsulación, esto permite asociar comportamiento y datos a cada constante (por ejemplo, días del mes), ocultar detalles con campos `private`, y garantizar que no existan instancias fuera del conjunto definido, reforzando invariantes (no puede “inventarse” un mes 13).


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

### Respuesta

Un `enum` `Mes` permite representar los meses como un tipo seguro, evitando depender de enteros sueltos. Además, al ser una clase, cada instancia puede llevar asociados datos como el número de días o el ordinal del mes en el año, manteniendo esos datos encapsulados mediante atributos `private`.

El constructor de un `enum` en Java no se puede invocar desde fuera: sólo se llama al definir las constantes. Por eso es una buena herramienta de encapsulación: se garantiza que sólo existen los 12 meses declarados y que cada uno queda inicializado de forma consistente.

```java
public enum Mes {
	ENERO(1, 31),
	FEBRERO(2, 28),
	MARZO(3, 31),
	ABRIL(4, 30),
	MAYO(5, 31),
	JUNIO(6, 30),
	JULIO(7, 31),
	AGOSTO(8, 31),
	SEPTIEMBRE(9, 30),
	OCTUBRE(10, 31),
	NOVIEMBRE(11, 30),
	DICIEMBRE(12, 31);

	private final int ordinalEnAnio; // 1-12
	private final int dias;

	Mes(int ordinalEnAnio, int dias) {
		this.ordinalEnAnio = ordinalEnAnio;
		this.dias = dias;
	}

	public int getDias() {
		return dias;
	}

	public int getOrdinalEnAnio() {
		return ordinalEnAnio;
	}
}
```


## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta

Para clasificar meses por estaciones, hace falta fijar un criterio. Una opción sencilla es asignar cada mes a una estación usando el criterio meteorológico: en el hemisferio norte, primavera (marzo–mayo), verano (junio–agosto), otoño (septiembre–noviembre) e invierno (diciembre–febrero). Si se está en el hemisferio sur, las estaciones se invierten (verano ↔ invierno, primavera ↔ otoño).

Estos métodos se añaden como comportamiento del propio `enum`, manteniendo la lógica encapsulada en el tipo `Mes` en lugar de repartir comparaciones por todo el programa. El parámetro `enHemisferioNorte` permite reutilizar el mismo tipo para ambos hemisferios sin duplicar estructuras.

```java
public boolean esDePrimavera(boolean enHemisferioNorte) {
	if (enHemisferioNorte) {
		return this == MARZO || this == ABRIL || this == MAYO;
	}
	return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
}

public boolean esDeVerano(boolean enHemisferioNorte) {
	if (enHemisferioNorte) {
		return this == JUNIO || this == JULIO || this == AGOSTO;
	}
	return this == DICIEMBRE || this == ENERO || this == FEBRERO;
}

public boolean esDeOtono(boolean enHemisferioNorte) {
	if (enHemisferioNorte) {
		return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
	}
	return this == MARZO || this == ABRIL || this == MAYO;
}

public boolean esDeInvierno(boolean enHemisferioNorte) {
	if (enHemisferioNorte) {
		return this == DICIEMBRE || this == ENERO || this == FEBRERO;
	}
	return this == JUNIO || this == JULIO || this == AGOSTO;
}
```
