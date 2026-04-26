<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a una función es una variable que almacena la dirección (la “referencia”) de una función. De ese modo se puede invocar la función de forma indirecta y, sobre todo, pasarla como parámetro o guardarla en estructuras para elegir un comportamiento en tiempo de ejecución (callbacks), algo muy común en C.

En C el tipo del puntero debe coincidir con la firma de la función (parámetros y retorno). Por eso la declaración usa paréntesis: `char* (*aMayusculas)(char*)` se lee como “puntero a una función que recibe `char*` y devuelve `char*`”. En el ejemplo se transforma el contenido en el propio buffer (sin reservar memoria dinámica) y se llama a través del puntero local `aMayusculas`.

```c
#include <ctype.h>
#include <stdio.h>

char* aMayusculasImpl(char* s) {
	for (char* p = s; *p != '\0'; ++p) {
		*p = (char)toupper((unsigned char)*p);
	}
	return s;
}

int main(void) {
	char texto[] = "Hola, mundo"; /* array modificable */

	char* (*aMayusculas)(char*) = aMayusculasImpl; /* puntero a función */
	printf("%s\n", aMayusculas(texto));            /* invocación indirecta */

	return 0;
}
```


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima (sin nombre) que se define “en el sitio” y se trata como un valor: se puede asignar a una variable, pasar como argumento o devolver como resultado. Se emplea para expresar transformaciones o callbacks pequeños sin crear una función con nombre por separado.

En JavaScript una lambda suele escribirse como una función flecha (`=>`). En Java (desde Java 8) una lambda necesita un tipo objetivo: se asigna a una interfaz funcional, por ejemplo `Function<String, String>`, y se invoca con el método abstracto de esa interfaz (`apply` en el caso de `Function`).

En ambos lenguajes la variable local `aMayusculas` “apunta” a la función y se invoca como cualquier llamada, con la diferencia de que la implementación no tiene nombre propio.

```javascript
const aMayusculas = (s) => s.toUpperCase();

console.log(aMayusculas("Hola"));
```

```java
import java.util.function.Function;

public class Demo {
	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();
		System.out.println(aMayusculas.apply("Hola"));
	}
}
```


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación en el que se pone el foco en componer funciones y transformar datos, en lugar de organizar la lógica principalmente alrededor de objetos con estado mutable. Suele promover ideas como funciones puras (mismo input → mismo output), menor dependencia de efectos laterales, e inmutabilidad cuando es posible, porque facilita razonar sobre el código y reutilizar piezas.

Se denomina multi-paradigma a un lenguaje cuando permite programar de forma natural con más de un estilo. Java sigue siendo un lenguaje fuertemente orientado a objetos (clases, herencia, polimorfismo), pero desde Java 8 incorpora herramientas funcionales (lambdas, referencias a métodos, `Stream`) que permiten expresar ciertas operaciones de forma declarativa; por eso se considera multi-paradigma.

Que las funciones sean “ciudadanos de primera clase” significa que se pueden tratar como cualquier otro valor: guardarlas en variables, pasarlas como parámetros y devolverlas como resultado. En C esto se aproxima con punteros a funciones; en Java se logra mediante interfaces funcionales y lambdas (es decir, “objetos” cuyo comportamiento es una función).


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis básica de una lambda en Java tiene dos partes separadas por `->`: a la izquierda se declaran los parámetros, y a la derecha el cuerpo. Los parámetros pueden ir entre paréntesis y con tipos explícitos, o bien sin tipos cuando el compilador puede inferirlos a partir del tipo objetivo (la interfaz funcional a la que se asigna).

El cuerpo puede ser una única expresión (en cuyo caso el `return` es implícito si la lambda devuelve algo) o un bloque `{ ... }` con varias sentencias. En el bloque, si la interfaz funcional devuelve un valor, se usa `return` como en un método normal.

La lambda siempre se “adapta” a una interfaz funcional concreta (por ejemplo `Function<String, String>`). Esa interfaz determina cuántos parámetros hay, de qué tipos, y qué se devuelve.

```java
import java.util.function.Function;

Function<String, String> f1 = (String s) -> s.toUpperCase();
Function<String, String> f2 = s -> {
	String limpio = s.trim();
	return limpio.toUpperCase();
};
```


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Recibir una función como parámetro permite escribir métodos genéricos que “no saben” qué transformación concreta se aplicará, solo que recibirán una transformación y la ejecutarán. Este enfoque evita duplicar métodos como `aMayusculas`, `aMinusculas`, `invertir`, etc., y concentra la lógica común (recibir un texto y devolver el resultado) en un único lugar.

En JavaScript se pasa una función (incluida una lambda) y se invoca como `transformador(texto)`. En Java se pasa una interfaz funcional (por ejemplo `Function<String, String>`) y se invoca con `apply`, que es el método abstracto de `Function`.

```javascript
function transformar(texto, transformador) {
  return transformador(texto);
}

const aMayusculas = (s) => s.toUpperCase();

console.log(transformar("Hola", aMayusculas));
```

```java
import java.util.function.Function;

public class Demo {
	static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();
		System.out.println(transformar("Hola", aMayusculas));
	}
}
```


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Cuando la transformación solo se usa una vez, resulta habitual definir la lambda directamente en la llamada al método, sin asignarla antes a una variable. Esto hace el código más expresivo: se ve el dato y la operación aplicada en el mismo lugar, especialmente útil para transformaciones pequeñas.

Para invertir una cadena, en JavaScript puede separarse en caracteres, invertir el array y unirlo de nuevo. En Java se suele usar `StringBuilder` y su método `reverse`. En ambos casos la lambda se define “inline” justo donde se pasa como parámetro a `transformar`.

```javascript
function transformar(texto, transformador) {
  return transformador(texto);
}

console.log(transformar("Hola", (s) => s.split("").reverse().join("")));
```

```java
import java.util.function.Function;

public class Demo {
	static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		System.out.println(transformar("Hola", s -> new StringBuilder(s).reverse().toString()));
	}
}
```


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre (closure) es una función que, además de su código, “recuerda” el entorno donde fue creada: puede acceder a variables definidas fuera de la propia función. En otras palabras, la lambda se “cierra” sobre esas variables, y las usa aunque la ejecución de la lambda ocurra en otro momento o en otro método.

En Java las lambdas pueden capturar variables locales, pero con una restricción importante: esas variables deben ser *efectivamente finales* (no se reasignan después). Esta regla existe para que el comportamiento sea predecible y seguro, ya que la lambda puede vivir más tiempo que el bloque donde se definió.

En el siguiente ejemplo se define una cadena `sufijo` fuera de la lambda y la lambda la concatena al texto de entrada; `sufijo` queda capturada por la closure.

```java
import java.util.function.Function;

public class Demo {
	static String transformar(String texto, Function<String, String> transformador) {
		return transformador.apply(texto);
	}

	public static void main(String[] args) {
		String sufijo = " (FIN)"; // variable local capturada

		Function<String, String> concatenarSufijo = s -> s + sufijo;

		System.out.println(transformar("Hola", concatenarSufijo));
	}
}
```


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Un puntero a función en C es, esencialmente, solo una dirección de memoria: “apunta” a una rutina concreta y no lleva información adicional. Si se necesita que la función trabaje con un estado externo (por ejemplo, un porcentaje de descuento), ese estado debe pasarse explícitamente como parámetro, o bien gestionarse con variables globales o con estructuras que se pasen junto al puntero.

Una lambda (en JavaScript o en Java) suele ser un cierre: además del código puede capturar variables del entorno y “transportarlas” consigo. En Java, además, una lambda tiene un tipo objetivo (una interfaz funcional) y se integra con el sistema de tipos, genéricos y sobrecarga; por debajo, se materializa como una instancia que implementa esa interfaz.

En consecuencia, la diferencia clave no es solo sintáctica, sino conceptual: el puntero en C representa “código sin contexto”, mientras que una lambda puede representar “código + contexto capturado”. Cuando se quiere emular closures en C, suele acabarse con el patrón “puntero a función + puntero a datos” (por ejemplo, `void*`) para transportar el estado.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Devolver funciones permite construir “fábricas de comportamiento”: se llama a un método una vez para configurar un parámetro, y se obtiene una función lista para aplicarse muchas veces. En el caso de los descuentos, resulta natural crear una función que recuerde el porcentaje y solo necesite el precio para calcular el resultado.

`crearDescuento(porcentaje)` devuelve una lambda `precio -> ...`. Esa lambda es una closure porque captura el valor de `porcentaje` del entorno donde fue creada. Por eso pueden coexistir varias funciones descuento diferentes (10%, 25%, etc.) sin interferirse entre sí.

Cada función devuelta “lleva dentro” su propio porcentaje capturado. Esa es la idea práctica de una closure: la función no solo ejecuta código, también conserva los datos necesarios para ese código.

```java
import java.util.function.Function;

public class Demo {
	static Function<Double, Double> crearDescuento(double porcentaje) {
		return precio -> precio * (1.0 - porcentaje / 100.0);
	}

	public static void main(String[] args) {
		Function<Double, Double> desc10 = crearDescuento(10);
		Function<Double, Double> desc25 = crearDescuento(25);

		double precio = 200.0;
		System.out.println(desc10.apply(precio)); // 180.0
		System.out.println(desc25.apply(precio)); // 150.0
	}
}
```


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional es una interfaz que representa un “tipo función”: especifica una única operación abstracta (un único método abstracto) que una lambda o una referencia a método puede implementar. Ese método abstracto define la firma: cuántos parámetros hay, de qué tipos son, y qué se devuelve.

El requisito principal es tener exactamente un método abstracto (se suele llamar SAM: *Single Abstract Method*). Puede tener métodos `default` y `static` adicionales sin dejar de ser funcional, porque no cuentan como abstractos. La anotación `@FunctionalInterface` no es obligatoria, pero es útil porque el compilador valida que se cumple el requisito.

Gracias a esto, Java mantiene su tipado estático: aunque se escriba una lambda “sin nombre”, se sabe con precisión qué se puede hacer con ella (por ejemplo, si se invoca con `apply`, `test`, `accept`, etc., según la interfaz).


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Crear una interfaz funcional “a mano” permite definir un tipo propio para una operación concreta. Conceptualmente es equivalente a `Function<String, String>`, pero con un nombre que puede resultar más expresivo en el dominio del problema (por ejemplo, “Transformador” en lugar de “Function”).

Al tener un único método abstracto, la interfaz puede implementarse con una lambda. Así se sigue disfrutando del estilo funcional, pero con tipos más descriptivos.

```java
@FunctionalInterface
interface Transformador {
	String transformar(String entrada);
}

public class Demo {
	static String transformar(String texto, Transformador t) {
		return t.transformar(texto);
	}

	public static void main(String[] args) {
		Transformador aMayusculas = s -> s.toUpperCase();
		System.out.println(transformar("Hola", aMayusculas));
	}
}
```


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Si se parametriza la interfaz con genéricos, se obtiene un “tipo función” reutilizable: transforma un valor de tipo `T` en un valor de tipo `R`. Es la misma idea que en `Function<T, R>`, pero con un nombre propio, lo que ayuda a conectar con el tema de genericidad y con el sistema de tipos.

En el ejemplo, se define un `Transformador<Double, Integer>` que redondea un `Double` a `Integer` usando `Math.round`. La lambda encaja porque recibe un `Double` y devuelve un `Integer`, que coincide con la firma genérica.

```java
@FunctionalInterface
interface Transformador<T, R> {
	R transformar(T entrada);
}

public class Demo {
	public static void main(String[] args) {
		Transformador<Double, Integer> redondear = d -> (int) Math.round(d);
		System.out.println(redondear.transformar(3.6)); // 4
	}
}
```


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java incluye un conjunto amplio de interfaces funcionales predefinidas, principalmente en el paquete `java.util.function`. Su objetivo es evitar crear una interfaz nueva para cada caso frecuente (consumir un valor, producir un valor, transformar, comprobar una condición, etc.) y estandarizar los nombres de los métodos (`apply`, `accept`, `test`, `get`).

Además de las versiones genéricas, existen variantes para dos parámetros (prefijo `Bi`) y especializaciones para tipos primitivos (`int`, `long`, `double`) que evitan el *boxing/unboxing* (`Integer` ↔ `int`) cuando se busca eficiencia.

A continuación se muestra una selección de las más usadas (hay más especializaciones y combinaciones):

```text
Paquete java.util.function (genéricas)
- Function<T, R>        R apply(T t)
- UnaryOperator<T>      T apply(T t)
- BiFunction<T, U, R>   R apply(T t, U u)
- Predicate<T>          boolean test(T t)
- BiPredicate<T, U>     boolean test(T t, U u)
- Consumer<T>           void accept(T t)
- BiConsumer<T, U>      void accept(T t, U u)
- Supplier<T>           T get()

Especializaciones para primitivos (ejemplos)
- IntPredicate / LongPredicate / DoublePredicate
- IntConsumer / LongConsumer / DoubleConsumer
- IntSupplier / LongSupplier / DoubleSupplier
- IntUnaryOperator / LongUnaryOperator / DoubleUnaryOperator
- ToIntFunction<T> / ToLongFunction<T> / ToDoubleFunction<T>

Otras funcionales habituales fuera de ese paquete
- Runnable              void run()                 (java.lang)
- Comparator<T>         int compare(T a, T b)      (java.util)
```


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

`forEach` es una forma funcional de recorrer una colección: en lugar de escribir el control del bucle manualmente, se indica “qué hacer con cada elemento”. El método recibe un `Consumer<T>`, es decir, una función que toma un elemento y no devuelve nada (normalmente produce un efecto, como imprimir).

En el ejemplo se recorre una lista de `Integer` y, para cada elemento, se comprueba si es positivo. La comprobación queda dentro de la lambda, manteniendo el recorrido expresado de manera declarativa.

```java
import java.util.Arrays;
import java.util.List;

public class Demo {
	public static void main(String[] args) {
		List<Integer> numeros = Arrays.asList(-2, 0, 3, 7, -1);

		numeros.forEach(n -> {
			if (n > 0) {
				System.out.println(n + " es positivo");
			}
		});
	}
}
```

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

`forEach` usa `Consumer<? super T>` porque el parámetro es un *consumidor* de elementos: se le van a “entregar” valores de tipo `T`. En genéricos, permitir `? super T` introduce contravarianza y hace el método más flexible: una lista de `Integer` puede aceptar un `Consumer<Number>` (porque un consumidor de `Number` también puede consumir `Integer`, que es un subtipo de `Number`). Con `Consumer<T>` esa sustitución no sería posible.

PECS significa *Producer Extends, Consumer Super*: si un genérico *produce* valores (se leen), suele usarse `? extends ...`; si *consume* valores (se escriben/reciben), suele usarse `? super ...`. Es una regla práctica para decidir entre `extends` y `super` al diseñar APIs con genéricos.

Aplicándolo a `transformar`, la función transformadora *consume* un `T` (el argumento de entrada) y *produce* un `R` (el resultado). Por eso una firma más flexible es `Function<? super T, ? extends R>`: admite transformadores que acepten supertipos de `T` y que devuelvan subtipos de `R`.

```java
import java.util.List;
import java.util.function.Consumer;
import java.util.function.Function;

public class Demo {
	static <T, R> R transformar(T valor, Function<? super T, ? extends R> transformador) {
		return transformador.apply(valor);
	}

	public static void main(String[] args) {
		List<Integer> nums = List.of(1, 2, 3);
		Consumer<Number> imprimirNumero = n -> System.out.println("Número: " + n);
		nums.forEach(imprimirNumero); // posible gracias a ? super Integer

		Number x = 5;
		Integer r = transformar(x, n -> n.intValue() * 2); // Function<Number, Integer>
		System.out.println(r);
	}
}
```

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Una referencia a un método consiste en guardar “cómo llamar” a un método para invocarlo más tarde. En JavaScript los métodos son funciones, pero existe un detalle importante: al extraer un método a una variable puede perderse el valor de `this`. Para conservar el contexto, se usa `bind`, que devuelve una función con `this` fijado.

En Java una referencia a método (`objeto::metodo`) es una forma compacta de crear una instancia de una interfaz funcional compatible. Si `saludar` no recibe parámetros y no devuelve nada, encaja con `Runnable`; la invocación se realiza llamando a `run`.

```javascript
class Persona {
  constructor(nombre) {
	this.nombre = nombre;
  }

  saludar() {
	console.log(`Hola, soy ${this.nombre}`);
  }
}

const p = new Persona("Ana");

const saludar = p.saludar.bind(p); // referencia al método con this fijado
saludar();
```

```java
class Persona {
	private final String nombre;

	Persona(String nombre) {
		this.nombre = nombre;
	}

	void saludar() {
		System.out.println("Hola, soy " + nombre);
	}
}

public class Demo {
	public static void main(String[] args) {
		Persona p = new Persona("Ana");

		Runnable saludar = p::saludar; // referencia a método de instancia
		saludar.run();
	}
}
```


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

En Java existen varias formas de referencias a métodos, todas con la misma idea: escribir `X::y` en lugar de una lambda equivalente, siempre que la firma coincida con la interfaz funcional destino. No es “magia” diferente a las lambdas, sino una sintaxis más compacta cuando ya existe un método con la forma adecuada.

Las categorías más habituales son: referencia a método estático (`Clase::metodoEstatico`), referencia a constructor (`Clase::new`), referencia a método de instancia de un objeto concreto (`instancia::metodo`) y referencia a método de instancia sobre cualquier instancia de un tipo (`Clase::metodoInstancia`). En este último caso, el primer parámetro de la interfaz funcional actúa como “receptor” (la instancia sobre la que se invoca el método).

La compatibilidad se decide por firma: número/tipos de parámetros y tipo de retorno. Si encaja, la referencia puede asignarse a `Function`, `Consumer`, `Supplier`, `Runnable`, etc.

```java
import java.util.function.Function;
import java.util.function.IntUnaryOperator;

class Persona {
	private final String nombre;

	Persona(String nombre) {
		this.nombre = nombre;
	}

	void saludar() {
		System.out.println("Hola, soy " + nombre);
	}

	String getNombre() {
		return nombre;
	}
}

public class Demo {
	public static void main(String[] args) {
		// 1) Referencia a método estático
		IntUnaryOperator abs = Math::abs;

		// 2) Referencia a constructor
		Function<String, Persona> crearPersona = Persona::new;

		// 3) Método de instancia de una instancia concreta
		Persona p = crearPersona.apply("Ana");
		Runnable saludar = p::saludar;

		// 4) Método de instancia sobre cualquier instancia (del tipo)
		Function<Persona, String> obtenerNombre = Persona::getNombre;

		System.out.println(abs.applyAsInt(-5));
		saludar.run();
		System.out.println(obtenerNombre.apply(p));
	}
}
```


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

Ordenar con `Collections.sort` consiste en proporcionar un comparador: una función que, dadas dos personas, devuelve un entero negativo/cero/positivo según el orden deseado. Con una lambda se escribe ese comparador de forma directa, comparando primero por edad y, en caso de empate, por nombre.

La versión “manual” es explícita y muestra la lógica paso a paso. La versión con `Comparator` resulta más declarativa y menos propensa a errores: se compone el criterio con `comparingInt` y `thenComparing`, lo que expresa claramente “ordenar por edad y luego por nombre”. Ambas terminan pasando un `Comparator<Persona>` a `Collections.sort`.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class Persona {
	private final String nombre;
	private final int edad;

	Persona(String nombre, int edad) {
		this.nombre = nombre;
		this.edad = edad;
	}

	String getNombre() {
		return nombre;
	}

	int getEdad() {
		return edad;
	}

	@Override
	public String toString() {
		return nombre + " (" + edad + ")";
	}
}

public class Demo {
	public static void main(String[] args) {
		List<Persona> personas = new ArrayList<>(Arrays.asList(
				new Persona("Luis", 20),
				new Persona("Ana", 20),
				new Persona("Marta", 18)
		));

		// Versión 1: comparador manual (lambda)
		Collections.sort(personas, (p1, p2) -> {
			int cmp = Integer.compare(p1.getEdad(), p2.getEdad());
			if (cmp != 0) {
				return cmp;
			}
			return p1.getNombre().compareToIgnoreCase(p2.getNombre());
		});
		System.out.println(personas);

		// Versión 2: usando Comparator (composición)
		Collections.sort(personas,
				Comparator.comparingInt(Persona::getEdad)
						.thenComparing(Persona::getNombre, String.CASE_INSENSITIVE_ORDER)
		);
		System.out.println(personas);
	}
}
```
