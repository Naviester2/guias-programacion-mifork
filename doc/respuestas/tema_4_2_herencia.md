<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
### Nota del director:
Composición -> "tiene un / tiene varios"  vs  Herencia -> "es un"

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

En orientación a objetos, la **herencia** es un mecanismo para definir una clase nueva (subclase) a partir de otra ya existente (superclase). La relación se suele expresar como **"A es-un B"**: si `Artillero` hereda de `Soldado`, entonces *un artillero es un soldado*, es decir, comparte su naturaleza general pero añade detalles propios.

Esa relación trae dos implicaciones principales. (1) **Compatibilidad de tipos**: cualquier objeto real de una subclase se puede usar donde se espera el supertipo (por ejemplo, meter `Artillero` y `Zapador` en un array de `Soldado`). (2) **Herencia de estado y comportamiento**: los objetos de la subclase contienen los atributos definidos en la superclase (estado) y también “arrastran” sus métodos (comportamiento), pudiendo reutilizarlos sin reescribirlos.

En Java la herencia se declara con `extends`. En el siguiente ejemplo, `Soldado` define el `nombre` (privado) y el método `saludar()`. Luego `Artillero` y `Zapador` heredan ese comportamiento y añaden su estado específico (cohetes o minas) con sus getters; además, se aprovecha la compatibilidad de tipos para recorrer un array de `Soldado` y pedir el saludo a todos.

```java
public class DemoHerencia {
	public static void main(String[] args) {
		Soldado[] escuadron = new Soldado[] {
			new Artillero("Alicia", 3),
			new Zapador("Bruno", 5),
			new Artillero("Carla", 1)
		};

		for (Soldado s : escuadron) {
			s.saludar();
		}
	}
}

class Soldado {
	private final String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}

	public void saludar() {
		System.out.println("Hola, soy " + nombre);
	}
}

class Artillero extends Soldado {
	private final int cohetes;

	public Artillero(String nombre, int cohetes) {
		super(nombre);
		this.cohetes = cohetes;
	}

	public int getCohetes() {
		return cohetes;
	}

	public void dispararCohete() {
		System.out.println("Disparando cohete...");
	}
}

class Zapador extends Soldado {
	private final int minas;

	public Zapador(String nombre, int minas) {
		super(nombre);
		this.minas = minas;
	}

	public int getMinas() {
		return minas;
	}

	public void ponerMina() {
		System.out.println("Colocando mina...");
	}
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta

Al crear un objeto de una subclase en Java se ejecuta **un constructor por cada clase de la cadena de herencia**, en orden desde la más general a la más específica. Por ejemplo, al hacer `new Artillero(...)` se construye primero la parte de `Object`, después la parte de `Soldado` y, por último, el propio `Artillero`. Esto garantiza que el estado heredado quede inicializado antes de que la subclase añada su inicialización.

Dentro de un constructor, `super(...)` significa **invocar un constructor de la superclase** y debe ser la primera instrucción del constructor. Si no se escribe, el compilador intenta insertar un `super()` implícito. Por tanto, si la clase base **no** tiene un constructor sin parámetros accesible, entonces se debe escribir `super(args)` explícitamente para llamar a algún constructor visible; si no, el código ni siquiera compila.

```java
class Soldado {
	public Soldado(String nombre) {
		System.out.println("Construyendo Soldado");
	}
}

class Artillero extends Soldado {
	public Artillero(String nombre) {
		super(nombre);
		System.out.println("Construyendo Artillero");
	}
}
```

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

En memoria, una instancia de una subclase **incluye también** los atributos declarados en sus superclases. Dicho de otra forma: un `Artillero` no “tiene” un `Soldado` aparte, sino que **es** un único objeto cuyo estado está compuesto por la parte heredada (por ejemplo, `Soldado.nombre`) más la parte propia (por ejemplo, `Artillero.cohetes`). Por eso `saludar()` funciona para cualquier subtipo: el campo `nombre` existe dentro del objeto real.

Ahora bien, que el campo exista **no implica** que sea accesible desde el código de la subclase. En Java, `private` es una restricción de acceso a nivel de clase, así que `Artillero` no puede referirse directamente a `nombre` si está declarado `private` en `Soldado`. Para usar ese dato desde la subclase se necesitaría un método (por ejemplo, un getter) o cambiar el nivel de acceso (por ejemplo, a `protected`), según lo que se quiera permitir.

```java
class Soldado {
	private final String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}

	public void saludar() {
		System.out.println("Hola, soy " + nombre);
	}
}

class Artillero extends Soldado {
	public Artillero(String nombre) {
		super(nombre);
	}

	public void ejemplo() {
		// System.out.println(nombre); // No compila: 'nombre' es private en Soldado
		saludar(); // Sí compila: el método es público y está heredado
	}
}
```

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad de tipos permite escribir código “contra el supertipo” (`Soldado`) sin conocer los subtipos concretos. Esto mejora la **extensibilidad**: se pueden añadir nuevos tipos de soldado sin reescribir el código que solo necesita capacidades comunes (por ejemplo, que todos puedan `saludar()`). Es una forma práctica de aplicar la idea de que el código de uso debería cambiar poco al crecer el sistema.

Si se crea un nuevo subtipo, por ejemplo `Medico`, lo único que cambia es que se pueden **instanciar y añadir** objetos de ese nuevo tipo. El bucle que recorre `Soldado[]` y llama a `saludar()` permanece exactamente igual, porque el contrato que usa (ser un `Soldado`) no ha cambiado.

```java
class Medico extends Soldado {
	private final int botiquines;

	public Medico(String nombre, int botiquines) {
		super(nombre);
		this.botiquines = botiquines;
	}

	public int getBotiquines() {
		return botiquines;
	}
}

public class DemoExtensibilidad {
	public static void main(String[] args) {
		Soldado[] escuadron = new Soldado[] {
			new Artillero("Alicia", 3),
			new Zapador("Bruno", 5),
			new Medico("Diana", 2)
		};

		for (Soldado s : escuadron) {
			s.saludar(); // Este código no se modifica al añadir 'Medico'
		}
	}
}
```


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

En Java se puede tener una referencia del supertipo apuntando a un objeto real de un subtipo (por ejemplo, `Soldado s = new Artillero(...)`). Esto es la base del polimorfismo: el objeto real “sigue siendo” un `Artillero`, pero se mira a través del tipo `Soldado`. Sin embargo, desde esa referencia solo se pueden invocar **los métodos que el compilador conoce en el tipo de la referencia** (los declarados en `Soldado` o heredados por él), aunque en ejecución se usará la versión sobrescrita si existe.

El **upcasting** es convertir una referencia de subtipo a supertipo; es implícito y seguro (no cambia el objeto, solo la “vista” del tipo). El **downcasting** es convertir de supertipo a subtipo; es explícito y puede fallar en ejecución con `ClassCastException` si el objeto real no es de ese subtipo. Para evitarlo se usa `instanceof`, que comprueba el tipo real del objeto antes de hacer el cast.

```java
public class DemoCasting {
	public static void main(String[] args) {
		Soldado[] escuadron = new Soldado[] {
			new Artillero("Alicia", 3),
			new Zapador("Bruno", 5),
			new Artillero("Carla", 1)
		};

		for (Soldado s : escuadron) {
			s.saludar();

			if (s instanceof Artillero) {
				Artillero a = (Artillero) s; // downcasting seguro tras instanceof
				System.out.println("Cohetes: " + a.getCohetes());
			}
		}
	}
}
```


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El acceso **protegido** (`protected`) es un nivel intermedio entre `private` y `public`. Permite que un miembro se use desde la propia clase y también desde sus **subclases** (y además desde clases del mismo paquete). Se emplea cuando se quiere habilitar reutilización o personalización en herencia sin exponer el detalle a todo el mundo, aunque conviene usarlo con cuidado porque aumenta el acoplamiento entre superclase y subclases.

En Java se implementa con la palabra clave `protected`. En el ejemplo siguiente, el nombre del soldado pasa a ser `protected`, de modo que `Zapador` puede usarlo directamente al ejecutar su acción específica de poner bombas/minas.

```java
class Soldado {
	protected final String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}

	public void saludar() {
		System.out.println("Hola, soy " + nombre);
	}
}

class Zapador extends Soldado {
	private int minas;

	public Zapador(String nombre, int minas) {
		super(nombre);
		this.minas = minas;
	}

	public void ponerBomba() {
		if (minas <= 0) {
			System.out.println(nombre + " no tiene minas disponibles");
			return;
		}
		minas--;
		System.out.println(nombre + " ha colocado una mina. Restantes: " + minas);
	}
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

No todos los lenguajes orientados a objetos tienen necesariamente una **clase base universal** para absolutamente todo. En C++ por ejemplo se pueden construir jerarquías de herencia, pero no existe una superclase obligatoria de la que hereden todas las clases; además, hay tipos que no son objetos (por ejemplo, tipos primitivos y estructuras sin una raíz común obligatoria).

En Java sí existe una raíz común: `java.lang.Object`. Toda clase hereda de `Object` aunque no se escriba explícitamente, lo que permite que cualquier instancia se trate como `Object` y tenga operaciones comunes como `toString()`, `equals()` y `hashCode()`. Los tipos primitivos (`int`, `double`, etc.) no heredan de `Object`, pero tienen clases envoltorio (por ejemplo, `Integer`) para tratarlos como objetos cuando hace falta.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La **herencia múltiple** es la capacidad de que una clase herede de **más de una** superclase al mismo tiempo, reutilizando estado y comportamiento de varias fuentes. Aunque puede parecer útil para “sumar” funcionalidades, introduce problemas clásicos como conflictos de nombres y el **problema del diamante** (dos caminos distintos que llevan a la misma superclase), lo que complica la resolución de qué implementación se debe usar.

En Java **no** existe herencia múltiple de clases: una clase solo puede usar `extends` con una única superclase. Sin embargo, Java permite implementar múltiples **interfaces** (`implements`), lo que da herencia múltiple de *tipo* (y, desde Java 8, también puede haber métodos `default` en interfaces, aunque los conflictos se resuelven obligando a sobrescribir).

```java
interface Nadador {
	void nadar();
}

interface Tirador {
	void disparar();
}

class Comando extends Soldado implements Nadador, Tirador {
	public Comando(String nombre) {
		super(nombre);
	}

	@Override
	public void nadar() {
		System.out.println("Nadando...");
	}

	@Override
	public void disparar() {
		System.out.println("Disparando...");
	}
}
```


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta

En Java, una excepción personalizada se define creando una clase que herede de una excepción existente. Si se quiere que sea **no controlada** (unchecked), se hereda de `RuntimeException`, de manera que el compilador no obliga a capturarla con `try/catch` ni a declararla con `throws` (aunque se puede hacer por documentación).

Para “componer” la excepción con el objeto que causó el problema, basta con guardar una referencia a un `Usuario` dentro de la excepción. Además, se pueden ofrecer dos constructores: uno con solo el `Usuario` y otro que también reciba una causa (`Throwable`) para encadenar el error original mediante `super(mensaje, causa)`.

```java
class Usuario {
	private final String dni;
	private final String nombre;

	public Usuario(String dni, String nombre) {
		this.dni = dni;
		this.nombre = nombre;
	}

	public String getDni() {
		return dni;
	}

	public String getNombre() {
		return nombre;
	}
}

class UsuarioNoEncontradoException extends RuntimeException {
	private final Usuario usuario;

	public UsuarioNoEncontradoException(Usuario usuario) {
		super("Usuario no encontrado: " + (usuario == null ? "(null)" : usuario.getDni()));
		this.usuario = usuario;
	}

	public UsuarioNoEncontradoException(Usuario usuario, Throwable cause) {
		super("Usuario no encontrado: " + (usuario == null ? "(null)" : usuario.getDni()), cause);
		this.usuario = usuario;
	}

	public Usuario getUsuario() {
		return usuario;
	}
}
```


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

La herencia no es un mecanismo genérico de “copiar y pegar” comportamiento, sino una forma de modelar una relación conceptual **"es-un"**. Si se usa solo para reutilizar código cuando esa relación no es realmente cierta, el modelo queda forzado: aparece una jerarquía que no representa bien el dominio y se termina violando la idea de que cualquier subtipo debería poder sustituir al supertipo sin sorpresas (principio de sustitución).

Además, la herencia crea un acoplamiento fuerte: la subclase depende de detalles y decisiones de la superclase, y cambios en la clase base pueden romper subclases existentes (problema de la “clase base frágil”). Cuando lo que se busca es reutilización sin una relación “es-un” clara, suele ser mejor extraer una clase colaboradora y **delegar** (composición), o bien usar utilidades, interfaces y otras formas de compartir comportamiento sin heredar implementación.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

La **composición** modela relaciones del tipo **"tiene-un"**: un objeto contiene a otro y le delega parte del trabajo. Esto suele dar diseños más flexibles porque se reutiliza comportamiento “por colaboración” en lugar de quedar atado a una jerarquía fija. Desde una mentalidad cercana a C/C++, se puede ver como pasar de “heredar código” a “guardar un campo y llamar a sus funciones/métodos”, pero con encapsulación y contratos mejor definidos.

Además, con composición se pueden intercambiar componentes (por ejemplo, distintas estrategias) sin cambiar la jerarquía, se evita la proliferación de subclases para pequeñas variaciones y se reduce el impacto de cambios internos. En general se consigue menos acoplamiento, más facilidad para probar piezas por separado y más capacidad de evolución del diseño sin romper código existente.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

Se dice que la herencia puede “romper la encapsulación” porque las subclases, para ser útiles, suelen necesitar conocer o depender de detalles internos de la superclase. Esto ocurre especialmente cuando se exponen miembros `protected` o cuando el comportamiento de la subclase solo funciona correctamente si asume invariantes, orden de llamadas o efectos secundarios internos de la superclase.

Como consecuencia, cambios “internos” en la superclase (que idealmente no deberían afectar al exterior) pueden afectar a las subclases y romperlas, aunque su interfaz pública no haya cambiado. Por eso, cuando la variación se puede expresar mejor como colaboración, la composición conserva mejor la encapsulación: el objeto compuesto usa a otro a través de su interfaz pública, sin apoyarse en detalles de implementación.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

Con **herencia**, se extraen los datos comunes (`dni`, `nombre`) a una superclase `Persona` y luego `Estudiante` y `Trabajador` heredan de ella. Este enfoque expresa que *un estudiante es una persona* y *un trabajador es una persona*, por lo que tiene sentido si el modelo del dominio realmente encaja con una relación “es-un” y si la superclase define un comportamiento común estable.

Con **composición**, se encapsulan esos datos comunes en un objeto `DatosPersonales` y las clases `Estudiante` y `Trabajador` lo contienen. Este enfoque expresa que *un estudiante tiene datos personales*, y facilita reutilizar esos datos sin obligar a encajar en una jerarquía; además, se cumple el requisito de recibir una instancia de `DatosPersonales` en el constructor, dejando claro qué parte es “reutilizable” y qué parte es específica de cada rol.

```java
// Alternativa 1: Herencia
class Persona {
	private final String dni;
	private final String nombre;

	public Persona(String dni, String nombre) {
		this.dni = dni;
		this.nombre = nombre;
	}

	public String getDni() {
		return dni;
	}

	public String getNombre() {
		return nombre;
	}
}

class Estudiante extends Persona {
	public Estudiante(String dni, String nombre) {
		super(dni, nombre);
	}
}

class Trabajador extends Persona {
	public Trabajador(String dni, String nombre) {
		super(dni, nombre);
	}
}
```

```java
// Alternativa 2: Composición
class DatosPersonales {
	private final String dni;
	private final String nombre;

	public DatosPersonales(String dni, String nombre) {
		this.dni = dni;
		this.nombre = nombre;
	}

	public String getDni() {
		return dni;
	}

	public String getNombre() {
		return nombre;
	}
}

class Estudiante {
	private final DatosPersonales datos;

	public Estudiante(DatosPersonales datos) {
		if (datos == null) {
			throw new IllegalArgumentException("DatosPersonales no puede ser null");
		}
		this.datos = datos;
	}

	public DatosPersonales getDatos() {
		return datos;
	}
}

class Trabajador {
	private final DatosPersonales datos;

	public Trabajador(DatosPersonales datos) {
		if (datos == null) {
			throw new IllegalArgumentException("DatosPersonales no puede ser null");
		}
		this.datos = datos;
	}

	public DatosPersonales getDatos() {
		return datos;
	}
}
```
