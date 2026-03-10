<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En C ya se puede expresar la idea de composición, aunque no se use todavía orientación a objetos. Basta con definir una `struct Punto` y después una `struct Linea` que contenga dos puntos. Eso representa exactamente la relación "una línea tiene dos puntos". Es una forma de construir tipos mayores a partir de otros más pequeños.

La operación de distancia entre dos puntos puede calcularse con la fórmula euclídea, y la longitud de una línea no es más que la distancia entre su punto inicial y su punto final. Se ve así que, incluso en C, ya aparece una estructura compuesta en la que un tipo reutiliza a otro para representar algo más complejo.

```c
#include <stdio.h>
#include <math.h>

typedef struct {
	double x;
	double y;
} Punto;

typedef struct {
	Punto inicio;
	Punto fin;
} Linea;

double distancia(Punto a, Punto b) {
	double dx = b.x - a.x;
	double dy = b.y - a.y;
	return sqrt(dx * dx + dy * dy);
}

double longitudLinea(Linea linea) {
	return distancia(linea.inicio, linea.fin);
}

int main() {
	Punto p1 = {1.0, 2.0};
	Punto p2 = {4.0, 6.0};
	Linea linea = {p1, p2};

	printf("Longitud: %.2f\n", longitudLinea(linea));
	return 0;
}
```


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta

En Java, la misma idea pasa de expresarse con `struct` a expresarse con clases. La composición consiste en que `Linea` contiene dos objetos `Punto`. La diferencia importante respecto a C es que ahora se puede ocultar el estado interno y obligar a que los objetos solo se creen en un estado válido. Si no se proporcionan métodos para modificar atributos y además estos se declaran `final`, el objeto queda inmutable.

En este caso interesa que ni un `Punto` cambie sus coordenadas ni una `Linea` cambie los puntos que une una vez construida. Así se evita que el objeto cambie “por detrás” y se consigue un diseño más seguro y fácil de razonar. La lógica relacionada con cada dato queda además en su propia clase: `Punto` sabe calcular distancias y `Linea` sabe calcular su propia longitud.

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

	public double distancia(Punto otro) {
		double dx = otro.x - x;
		double dy = otro.y - y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

public final class Linea {
	private final Punto inicio;
	private final Punto fin;

	public Linea(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser null");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public Punto getInicio() {
		return inicio;
	}

	public Punto getFin() {
		return fin;
	}

	public double longitud() {
		return inicio.distancia(fin);
	}
}
```


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad indica cuántos objetos de una clase se relacionan con cuántos objetos de otra. No describe el contenido del objeto ni sus métodos, sino la cantidad permitida en la relación. Es una forma de contestar preguntas como: “¿una línea tiene uno, dos o muchos puntos?” o “¿un punto pertenece a una o a varias líneas?”.

En el ejemplo propuesto, de `Linea` a `Punto` la multiplicidad es `2`, porque cada línea está formada exactamente por dos puntos: el inicial y el final. En sentido contrario, de `Punto` a `Linea`, la multiplicidad no queda fijada por esas clases. Un mismo punto podría no pertenecer a ninguna línea, o podría reutilizarse en muchas, así que en general se expresaría como `0..*`. Es importante observar que la multiplicidad en una dirección no obliga a que en la otra exista la misma cantidad.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

La diferencia principal entre composición fuerte y composición débil está en el ciclo de vida de los objetos contenidos. En la composición fuerte, el objeto contenedor “posee” realmente a los objetos internos: nacen como parte de él y su existencia queda ligada a la del contenedor. En la composición débil, en cambio, el contenedor solo mantiene referencias a objetos que pueden seguir existiendo por su cuenta.

Por eso, cuando se destruye el contenedor, en la composición fuerte también dejan de tener sentido los componentes que pertenecían exclusivamente a él. En la débil no ocurre eso: los componentes pueden seguir siendo usados desde otros lugares. Habitualmente se llama **agregación** o **asociación** a la composición débil, mientras que se reserva el nombre de **composición** propiamente dicha para la relación fuerte.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

En esos casos se habla normalmente de **dependencia**, no de composición. La clave es que la otra clase se usa para realizar una operación concreta, pero no pasa a formar parte del estado permanente del objeto. Dicho de otra manera, la relación existe mientras se ejecuta un método o mientras se necesita un cálculo, pero no queda guardada como atributo estable del objeto.

La composición aparece cuando una clase tiene campos que son objetos de otra clase y esos campos representan una parte de su estructura interna. Si una clase recibe un objeto por parámetro, crea uno dentro de un método o usa variables locales, eso indica uso o colaboración puntual. Por tanto, composición significa “tener como parte de sí”; dependencia significa “necesitar temporalmente para hacer algo”.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

Para modelar composición fuerte conviene hacer que `Linea` no reciba objetos `Punto` ya creados, sino los datos necesarios para construirlos internamente. Así, desde fuera no se comparten esos puntos con nadie y quedan claramente ligados a la línea. Es una forma de expresar que esos puntos existen como parte de esa línea concreta.

En la composición débil, por el contrario, `Linea` recibe referencias a puntos que podrían haber sido creados antes y podrían estar compartidos con otras líneas. La clase sigue usando puntos como parte de su estructura, pero su ciclo de vida no depende exclusivamente del de la línea. En el código siguiente se observan las dos variantes.

```java
public final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double distancia(Punto otro) {
		double dx = otro.x - x;
		double dy = otro.y - y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

public final class LineaFuerte {
	private final Punto inicio;
	private final Punto fin;

	public LineaFuerte(double x1, double y1, double x2, double y2) {
		this.inicio = new Punto(x1, y1);
		this.fin = new Punto(x2, y2);
	}

	public double longitud() {
		return inicio.distancia(fin);
	}
}

public final class LineaDebil {
	private final Punto inicio;
	private final Punto fin;

	public LineaDebil(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser null");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public double longitud() {
		return inicio.distancia(fin);
	}
}
```


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En Java no se destruyen objetos de forma explícita como podría hacerse en otros lenguajes con memoria manual. Lo que ocurre es que un objeto queda disponible para ser recogido por el recolector de basura cuando ya no existe ninguna referencia accesible hacia él. Por eso, en una composición fuerte, al desaparecer el objeto contenedor suelen dejar de existir también las únicas referencias hacia los objetos internos.

No se ve un código donde `Linea` “destruya” a `Punto` porque Java delega esa tarea en el recolector de basura. Si `Linea` era quien tenía las únicas referencias a sus puntos, al dejar de ser accesible la propia `Linea`, esos `Punto` también quedan inaccesibles y el sistema podrá liberar su memoria más adelante. El momento exacto no lo decide el programador, sino la máquina virtual.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

Aquí interesa una composición débil porque los profesores no “nacen” dentro del departamento ni dejan de existir cuando este desaparece. El departamento simplemente mantiene referencias a profesores ya existentes. Además, aparece una segunda relación compuesta: el departamento tiene muchos profesores y, a la vez, tiene un director, que debe ser uno de esos profesores. Esa condición es la invariante principal de la clase.

Para mantener la encapsulación no se debe devolver directamente el array interno ni permitir modificarlo desde fuera. Por eso se ofrece una interfaz pequeña: añadir profesor, eliminar por posición, consultar cuántos hay, obtener uno por índice y cambiar el director. Cada operación debe comprobar que no se rompa la invariante, por ejemplo impidiendo eliminar al director si fuese el único caso que dejaría al director fuera de la lista.

```java
public final class Profesor {
	private final String nombre;

	public Profesor(String nombre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede estar vacio");
		}
		this.nombre = nombre;
	}

	public String getNombre() {
		return nombre;
	}
}

public class Departamento {
	private static final int MAX_PROFESORES = 50;

	private final String nombre;
	private final Profesor[] profesores;
	private int numProfesores;
	private Profesor director;

	public Departamento(String nombre, Profesor directorInicial) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede estar vacio");
		}
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}

		this.nombre = nombre;
		this.profesores = new Profesor[MAX_PROFESORES];
		this.profesores[0] = directorInicial;
		this.numProfesores = 1;
		this.director = directorInicial;
	}

	public String getNombre() {
		return nombre;
	}

	public int getNumProfesores() {
		return numProfesores;
	}

	public Profesor getProfesor(int pos) {
		comprobarPosicion(pos);
		return profesores[pos];
	}

	public Profesor getDirector() {
		return director;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser null");
		}
		if (numProfesores >= MAX_PROFESORES) {
			throw new IllegalStateException("No caben mas profesores");
		}
		profesores[numProfesores] = profesor;
		numProfesores++;
	}

	public void setDirector(Profesor nuevoDirector) {
		if (nuevoDirector == null) {
			throw new IllegalArgumentException("El director no puede ser null");
		}
		if (!contieneProfesor(nuevoDirector)) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}
		director = nuevoDirector;
	}

	public void removeProfesor(int pos) {
		comprobarPosicion(pos);
		Profesor eliminado = profesores[pos];

		if (eliminado == director) {
			throw new IllegalStateException("No se puede eliminar al director");
		}

		for (int i = pos; i < numProfesores - 1; i++) {
			profesores[i] = profesores[i + 1];
		}

		profesores[numProfesores - 1] = null;
		numProfesores--;
	}

	private boolean contieneProfesor(Profesor profesor) {
		for (int i = 0; i < numProfesores; i++) {
			if (profesores[i] == profesor) {
				return true;
			}
		}
		return false;
	}

	private void comprobarPosicion(int pos) {
		if (pos < 0 || pos >= numProfesores) {
			throw new IndexOutOfBoundsException("Posicion no valida");
		}
	}
}
```


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Con `List` desaparece gran parte del trabajo manual asociado a los arrays. Ya no hace falta llevar una variable `numProfesores`, ni desplazar elementos a mano al borrar, ni gestionar posiciones libres al final. La colección crece dinámicamente y ya ofrece operaciones como `add`, `get`, `remove` y `size`. Eso simplifica mucho la implementación y hace que el código se concentre en las invariantes del dominio, no en detalles de almacenamiento.

Si existiera un método para devolver todos los profesores, no convendría retornar directamente la lista interna, porque desde fuera podría modificarse y romperse la encapsulación. Por ejemplo, se podría eliminar al director o insertar un `null` sin pasar por las comprobaciones de la clase. La solución habitual es devolver una vista no modificable o una copia defensiva, de manera que el exterior pueda consultar la colección sin alterar el estado interno.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Departamento {
	private final String nombre;
	private final List<Profesor> profesores;
	private Profesor director;

	public Departamento(String nombre, Profesor directorInicial) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede estar vacio");
		}
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}

		this.nombre = nombre;
		this.profesores = new ArrayList<>();
		this.profesores.add(directorInicial);
		this.director = directorInicial;
	}

	public int getNumProfesores() {
		return profesores.size();
	}

	public Profesor getProfesor(int pos) {
		return profesores.get(pos);
	}

	public List<Profesor> getProfesores() {
		return Collections.unmodifiableList(profesores);
	}

	public Profesor getDirector() {
		return director;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser null");
		}
		profesores.add(profesor);
	}

	public void setDirector(Profesor nuevoDirector) {
		if (nuevoDirector == null) {
			throw new IllegalArgumentException("El director no puede ser null");
		}
		if (!profesores.contains(nuevoDirector)) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}
		director = nuevoDirector;
	}

	public void removeProfesor(int pos) {
		Profesor eliminado = profesores.get(pos);
		if (eliminado == director) {
			throw new IllegalStateException("No se puede eliminar al director");
		}
		profesores.remove(pos);
	}
}
```


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

Se habla de composición recursiva cuando una clase contiene como parte de sí otra referencia al mismo tipo. En este caso, una `Persona` tiene una madre, que también es una `Persona`. La idea puede impresionar al principio, pero en realidad es parecida a una lista enlazada o a una excepción que contiene otra excepción como causa: el patrón es siempre “un objeto que incluye otro de su misma clase”.

Para mantener la inmutabilidad basta con declarar los atributos `final`, no exponer métodos de modificación y validar el estado en el constructor. En el ejemplo siguiente se crea una pequeña cadena familiar: abuela, madre y nieto. Otros ejemplos clásicos de composiciones recursivas son los nodos de un árbol, los directorios que contienen subdirectorios o los comentarios de un foro que contienen respuestas, que a su vez son comentarios.

```java
public final class Persona {
	private final String nombre;
	private final Persona madre;

	public Persona(String nombre, Persona madre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede estar vacio");
		}
		this.nombre = nombre;
		this.madre = madre;
	}

	public String getNombre() {
		return nombre;
	}

	public Persona getMadre() {
		return madre;
	}
}

public class Main {
	public static void main(String[] args) {
		Persona abuela = new Persona("Carmen", null);
		Persona madre = new Persona("Laura", abuela);
		Persona nieto = new Persona("Diego", madre);

		System.out.println("Nieto: " + nieto.getNombre());
		System.out.println("Madre: " + nieto.getMadre().getNombre());
		System.out.println("Abuela: " + nieto.getMadre().getMadre().getNombre());
	}
}
```

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Una relación bidireccional es aquella en la que ambos lados conocen la relación. No solo `Departamento` sabe qué `Profesor` contiene, sino que además cada `Profesor` sabe a qué `Departamento` pertenece. En una relación unidireccional solo uno de los dos sentidos está implementado; en una bidireccional, se puede navegar en ambos.

Para implementarlo en el ejemplo habría que añadir en `Profesor` un atributo `Departamento departamento` y mantener la coherencia desde ambos lados. Eso obliga a actualizar las dos clases cada vez que se añade o elimina un profesor, o cuando cambia de departamento. El punto delicado es que no basta con cambiar una referencia: siempre debe modificarse la relación por las dos partes para que no aparezcan inconsistencias, como un profesor que diga pertenecer a un departamento que no lo tiene realmente en su lista.
