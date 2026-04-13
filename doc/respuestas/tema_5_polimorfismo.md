<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

Se llama **polimorfismo** a la capacidad de tratar objetos de distintos tipos concretos como si fueran del mismo tipo general (un supertipo), manteniendo un comportamiento “adecuado” para cada objeto real. En la práctica, permite escribir código genérico (por ejemplo, recorrer un array de `Soldado`) y que, al invocar un método común, cada objeto responda a su manera. Visto desde C, se parece a tener una única “interfaz” de funciones y que la llamada se redirija a la implementación correcta sin tener que hacer `switch` manual ni manejar punteros a función explícitos.

La **sobreescritura** (overriding) ocurre cuando una subclase define de nuevo un método heredado con **la misma firma** (mismo nombre y parámetros) para cambiar su comportamiento. Es el mecanismo típico que hace posible el polimorfismo “de ejecución”: aunque la referencia sea del tipo base, el método que realmente se ejecuta es el de la clase concreta si está sobrescrito.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La **ligadura dinámica** (o **enlace tardío**) significa que la decisión de “qué implementación de método ejecutar” se toma **en tiempo de ejecución** usando el tipo real del objeto, y no el tipo de la variable/referencia con la que se lo está manejando. Es el soporte técnico del polimorfismo: si se invoca `saludar()` a través de una referencia `Soldado`, se ejecuta la versión de `Zapador` o `Artillero` según el objeto real.

En **C++** esto no ocurre por defecto: para que una llamada sea dinámica, el método debe ser `virtual` (y se suele marcar la sobreescritura con `override`), y la llamada debe hacerse a través de un puntero o referencia al tipo base. Si el método no es `virtual`, el enlace suele ser temprano (en compilación) y se ejecuta la versión asociada al tipo estático de la expresión. En **Java**, en cambio, los métodos de instancia son “virtuales” por defecto: si un método se sobrescribe, la selección dinámica ocurre automáticamente al llamar a través de una referencia del supertipo; solo quedan fuera casos como `static`, `final` o `private`.

En **Python** el enlace es todavía más dinámico: la búsqueda del atributo/método se hace en tiempo de ejecución y no se declara nada como `virtual`. Además, por el tipado dinámico y el “duck typing”, a menudo ni siquiera se exige un supertipo común explícito: si un objeto “tiene” el método con ese nombre, se puede invocar, y la resolución depende del objeto concreto en ese momento.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

En este ejemplo `Soldado` define un método `saludar()`. La subclase `Zapador` **sobrescribe** ese método y lo reemplaza por completo (no reutiliza la versión base), mientras que `Artillero` hereda el saludo “normal”. Así se ve claramente la idea de “mismo mensaje (saludar), distintas formas de responder”.

El polimorfismo se aprecia al guardar objetos de ambos tipos en un array de `Soldado` y recorrerlo: aunque la variable del bucle sea de tipo `Soldado`, la llamada `s.saludar()` ejecuta en ejecución la implementación correspondiente al tipo real del objeto. Se evita tener que comprobar el tipo y ramificar manualmente para decidir qué saludo imprimir.

```java
public class DemoPolimorfismo {
	public static void main(String[] args) {
		Soldado[] escuadron = new Soldado[] {
			new Soldado(),
			new Zapador(),
			new Artillero(),
			new Zapador()
		};

		for (Soldado s : escuadron) {
			s.saludar();
		}
	}
}

class Soldado {
	public void saludar() {
		System.out.println("SALUDO BASE: soy un soldado");
	}
}

class Artillero extends Soldado {
	// Hereda el saludo base sin cambios
}

class Zapador extends Soldado {
	@Override
	public void saludar() {
		System.out.println("SALUDO ZAPADOR: listo para despejar el terreno");
	}
}
```


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Al sobrescribir un método sí se puede **invocar la versión de la clase base** y, a partir de ahí, extender o ajustar el comportamiento. Esto es útil cuando interesa reutilizar la parte común (por ejemplo, el saludo estándar) y solo añadir un detalle propio en la subclase.

En Java se utiliza la palabra clave `super` para referirse explícitamente a la implementación de la superclase. En el siguiente ejemplo, `Zapador` llama primero a `super.saludar()` (saludo normal) y después añade el mensaje adicional.

```java
public class DemoSuper {
	public static void main(String[] args) {
		Soldado s1 = new Soldado();
		Soldado s2 = new Zapador();

		s1.saludar();
		s2.saludar();
	}
}

class Soldado {
	public void saludar() {
		System.out.println("SALUDO BASE: soy un soldado");
	}
}

class Zapador extends Soldado {
	@Override
	public void saludar() {
		super.saludar();
		System.out.println("ZAPADOR A SUS ORDENES");
	}
}
```


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Para **sobreescribir** un método en Java se exige conservar la **misma firma**: mismo nombre y mismos tipos y orden de parámetros (no se cambia el “contrato” de llamada). El tipo de retorno debe ser el mismo, o bien un tipo **covariante** (un subtipo del retorno original). Además, no se puede reducir la visibilidad (por ejemplo, de `public` a `protected`) y, con excepciones comprobadas (checked), no se pueden declarar excepciones más generales que las del método base.

La **sobreescritura (overriding)** ocurre entre superclase y subclase con la misma firma, y su elección es característica del polimorfismo: la versión ejecutada depende del objeto real en ejecución. La **sobrecarga (overloading)**, en cambio, consiste en definir varios métodos con el mismo nombre pero **distinta lista de parámetros** (distinto número o tipos). La sobrecarga se decide en compilación según los tipos de los argumentos, y no requiere herencia.

La anotación `@Override` le pide al compilador que verifique que el método realmente está sobrescribiendo otro. Es recomendable usarla siempre porque evita errores sutiles (por ejemplo, escribir una firma distinta por accidente y terminar creando una sobrecarga no deseada), y además documenta la intención de forma clara.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

En Java se empieza a usar polimorfismo muy pronto, incluso sin “notarlo”, porque todas las clases heredan de `Object` y muchas APIs trabajan con referencias de tipos generales. Al sobrescribir `toString()` o `equals(Object)`, se está redefiniendo un método heredado que luego será invocado a través de referencias del supertipo (`Object`), y la versión ejecutada dependerá del tipo real del objeto.

Por ejemplo, `System.out.println(obj)` acaba llamando a `obj.toString()` y lo hace sin conocer el subtipo concreto. Del mismo modo, colecciones como `List.contains(...)` o `Set` dependen de `equals` para comparar objetos: si se sobrescribe `equals`, ese comportamiento personalizado se aplica automáticamente cuando la colección opera con referencias del tipo más general.

```java
class Persona {
	private final String nombre;

	public Persona(String nombre) {
		this.nombre = nombre;
	}

	@Override
	public String toString() {
		return "Persona(" + nombre + ")";
	}
}

public class DemoToString {
	public static void main(String[] args) {
		Object o = new Persona("Ana");
		System.out.println(o); // Se usa el toString() de Persona por enlace dinámico
	}
}
```


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una **clase abstracta** es una clase pensada para ser usada como base: puede incluir estado, constructores y métodos ya implementados, pero también puede dejar partes “sin definir” para que las concreten sus subclases. Un **método abstracto** es un método sin cuerpo (sin implementación) que declara qué se debe poder hacer, obligando a que una subclase concreta lo implemente.

No se pueden crear instancias directas de una clase abstracta: no se permite `new Soldado()` si `Soldado` es `abstract`. Sí se pueden crear objetos de subclases concretas, que heredan la parte común y completan lo abstracto. En Java, la palabra `abstract` se coloca en la declaración de la clase y también en la declaración del método que queda sin implementación.

En el siguiente ejemplo, `Soldado` define `saludar()` (concreto) y fuerza a implementar `atacar()` (abstracto). Luego `Artillero` y `Zapador` implementan `atacar()` de forma distinta; al recorrer un array de `Soldado` se ve que el método ejecutado depende del tipo real.

```java
public class DemoAbstract {
	public static void main(String[] args) {
		Soldado[] escuadron = new Soldado[] {
			new Artillero(),
			new Zapador()
		};

		for (Soldado s : escuadron) {
			s.saludar();
			s.atacar();
		}
	}
}

abstract class Soldado {
	public void saludar() {
		System.out.println("Soy un soldado y saludo de forma estándar");
	}

	public abstract void atacar();
}

class Artillero extends Soldado {
	@Override
	public void atacar() {
		System.out.println("ARTILLERO: disparando cohetes");
	}
}

class Zapador extends Soldado {
	@Override
	public void atacar() {
		System.out.println("ZAPADOR: colocando explosivos");
	}
}
```


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

En Java, marcar un **método** como `final` significa que **no se puede sobrescribir** en subclases. Esto “congela” el comportamiento del método a lo largo de la jerarquía: aunque se pueda seguir usando el objeto por una referencia del supertipo, ya no se puede crear un subtipo que cambie esa parte concreta del comportamiento, lo cual reduce (a propósito) las posibilidades de polimorfismo.

Marcar una **clase** como `final` significa que **no se puede heredar** de ella. Por tanto, esa clase no puede actuar como “base” de una familia de tipos que aporten nuevas variantes mediante sobreescritura. Aun así, una clase `final` sigue participando como subtipo de sus supertipos (por ejemplo, `String` sigue siendo un `Object`) y se puede usar polimórficamente a ese nivel, solo que no es extensible por herencia.

En la API estándar hay ejemplos conocidos de clases `final`, como `java.lang.String`, y también varios envoltorios numéricos como `java.lang.Integer` o `java.lang.Double` (entre otros). `java.lang.System` también es `final`.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

En Java, una **interfaz** define un conjunto de métodos (un contrato) que indica qué operaciones ofrece un tipo, sin imponer una implementación concreta. Se usa para modelar “capacidades” (“se puede ordenar”, “se puede comparar”, “se puede cerrar”, etc.) y permite programar contra esa abstracción en lugar de depender de clases concretas.

Se parecen a las clases abstractas en que ambas pueden expresar un contrato que luego se cumple en subclases/implementaciones. La diferencia práctica es que una **clase abstracta** puede llevar estado (atributos de instancia), constructores y una implementación parcial con lógica compartida; una **interfaz** se centra en el contrato y, aunque desde Java 8 puede incluir métodos `default` (con implementación) y `static`, no sirve para compartir estado de instancia como una clase.

Una clase puede **implementar más de una interfaz** (por ejemplo, `class X implements A, B, C`), lo que es una alternativa común a la herencia múltiple de clases. Esto habilita polimorfismo por interfaz: se puede tratar el mismo objeto como cualquiera de las interfaces que implementa según lo que se necesite en cada punto del programa.


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Una forma directa de modelarlo es declarar `Punto` como clase abstracta con el método abstracto `calcularDistanciaA(Punto otro)`. Luego se crean dos subtipos (`Punto2D` y `Punto3D`) que implementan el cálculo de distancia con su fórmula correspondiente. Así, el mismo mensaje “calcular distancia” se resuelve de manera distinta según el tipo real del punto.

Para cumplir la condición de “distancia solo entre puntos del mismo subtipo”, cada implementación puede comprobar el tipo con `instanceof` y, si es compatible, hacer *downcasting* para acceder al subtipo concreto. Si no es compatible, se suele lanzar una excepción (por ejemplo `IllegalArgumentException`) indicando que se ha mezclado 2D con 3D.

Con ese diseño, `Linea` puede almacenar referencias de tipo `Punto` sin conocer si son 2D o 3D; para calcular su longitud solo debe llamar `inicio.calcularDistanciaA(fin)`. El polimorfismo permite que se ejecute automáticamente la versión correcta (2D o 3D) sin que `Linea` necesite ramificar por tipos.

```java
public class DemoGeometria {
	public static void main(String[] args) {
		Linea l2 = new Linea(new Punto2D(0, 0), new Punto2D(3, 4));
		System.out.println("Longitud 2D: " + l2.longitud());

		Linea l3 = new Linea(new Punto3D(0, 0, 0), new Punto3D(1, 2, 2));
		System.out.println("Longitud 3D: " + l3.longitud());

		try {
			Linea mezclada = new Linea(new Punto2D(0, 0), new Punto3D(0, 0, 0));
			System.out.println(mezclada.longitud());
		} catch (IllegalArgumentException ex) {
			System.out.println("Error: " + ex.getMessage());
		}
	}
}

abstract class Punto {
	private final double x;
	private final double y;

	protected Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public final double getX() {
		return x;
	}

	public final double getY() {
		return y;
	}

	public abstract double calcularDistanciaA(Punto otro);
}

final class Punto2D extends Punto {
	public Punto2D(double x, double y) {
		super(x, y);
	}

	@Override
	public double calcularDistanciaA(Punto otro) {
		if (!(otro instanceof Punto2D)) {
			throw new IllegalArgumentException("Se esperaba un Punto2D");
		}
		Punto2D o = (Punto2D) otro;
		double dx = getX() - o.getX();
		double dy = getY() - o.getY();
		return Math.sqrt(dx * dx + dy * dy);
	}
}

final class Punto3D extends Punto {
	private final double z;

	public Punto3D(double x, double y, double z) {
		super(x, y);
		this.z = z;
	}

	public double getZ() {
		return z;
	}

	@Override
	public double calcularDistanciaA(Punto otro) {
		if (!(otro instanceof Punto3D)) {
			throw new IllegalArgumentException("Se esperaba un Punto3D");
		}
		Punto3D o = (Punto3D) otro;
		double dx = getX() - o.getX();
		double dy = getY() - o.getY();
		double dz = getZ() - o.getZ();
		return Math.sqrt(dx * dx + dy * dy + dz * dz);
	}
}

class Linea {
	private final Punto inicio;
	private final Punto fin;

	public Linea(Punto inicio, Punto fin) {
		this.inicio = inicio;
		this.fin = fin;
	}

	public double longitud() {
		return inicio.calcularDistanciaA(fin);
	}
}
```


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La **herencia de interfaces** en Java consiste en que una interfaz puede **extender** (`extends`) a otra, heredando sus métodos y pudiendo añadir nuevos. De esta manera, una interfaz derivada expresa un contrato “más específico”: todo `FicheroEscribible` es también un `Fichero`, pero con operaciones adicionales.

Sí existe **herencia múltiple de interfaces**: una interfaz puede extender varias interfaces a la vez (por ejemplo, `interface X extends A, B { ... }`). Esto permite combinar contratos sin heredar implementación ni estado de una clase. A nivel de clases, una clase también puede implementar múltiples interfaces, uniendo varias capacidades en un solo tipo concreto.

En el ejemplo, `Fichero` define la operación de lectura, y `FicheroEscribible` la amplía con escritura y borrado. Una clase que implemente `FicheroEscribible` queda obligada a implementar **todos** los métodos declarados (los propios y los heredados).

```java
interface Fichero {
	String leerContenido();
}

interface FicheroEscribible extends Fichero {
	void escribirContenido(String contenido);
	void eliminar();
}

class FicheroEnMemoria implements FicheroEscribible {
	private String contenido = "";
	private boolean eliminado = false;

	@Override
	public String leerContenido() {
		if (eliminado) {
			throw new IllegalStateException("El fichero ha sido eliminado");
		}
		return contenido;
	}

	@Override
	public void escribirContenido(String contenido) {
		if (eliminado) {
			throw new IllegalStateException("El fichero ha sido eliminado");
		}
		this.contenido = contenido;
	}

	@Override
	public void eliminar() {
		eliminado = true;
		contenido = "";
	}
}
```
