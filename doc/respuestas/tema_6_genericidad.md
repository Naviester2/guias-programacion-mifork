<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

Se puede construir una estructura de datos “tipo vector” cuya implementación interna sea un array que almacena **direcciones** (en C) o **referencias** (en Java). En C, un `void*` puede apuntar a cualquier tipo de dato, de modo que un array de `void*` (`void**`) permite guardar punteros a `int`, `double`, `struct`, etc. En Java, un array de `Object` (`Object[]`) puede almacenar cualquier objeto (porque todas las clases heredan de `Object`).

La contrapartida es que el contenedor guarda únicamente punteros/referencias: al recuperar un elemento se necesita convertirlo al tipo esperado (casting). Además, en C se debe gestionar la memoria y la vida útil del dato apuntado (si se guarda la dirección de una variable local y esta deja de existir, el puntero queda colgando). En Java, los tipos primitivos (`int`, `double`, …) no son objetos, por lo que para almacenarlos en `Object[]` se usan envoltorios (`Integer`, `Double`) mediante autoboxing.

```c
// Vector genérico en C basado en void*
#include <stdlib.h>
#include <stdio.h>

typedef struct {
    void** data;
    size_t size;
    size_t cap;
} VectorVoid;

static void vv_init(VectorVoid* v) {
    v->size = 0;
    v->cap = 4;
    v->data = (void**)malloc(v->cap * sizeof(void*));
}

static void vv_push(VectorVoid* v, void* elem) {
    if (v->size == v->cap) {
        v->cap *= 2;
        v->data = (void**)realloc(v->data, v->cap * sizeof(void*));
    }
    v->data[v->size++] = elem;
}

static void* vv_get(const VectorVoid* v, size_t i) {
    return v->data[i];
}

static void vv_free(VectorVoid* v) {
    free(v->data);
    v->data = NULL;
    v->size = 0;
    v->cap = 0;
}

int main(void) {
    VectorVoid v;
    vv_init(&v);

    int* pi = (int*)malloc(sizeof(int));
    double* pd = (double*)malloc(sizeof(double));
    *pi = 42;
    *pd = 3.14;

    vv_push(&v, pi);
    vv_push(&v, pd);

    printf("int: %d\n", *(int*)vv_get(&v, 0));
    printf("double: %f\n", *(double*)vv_get(&v, 1));

    free(pi);
    free(pd);
    vv_free(&v);
    return 0;
}
```

```java
// Vector genérico en Java basado en Object
import java.util.Arrays;

class VectorObject {
    private Object[] data = new Object[4];
    private int size = 0;

    public void push(Object o) {
        if (size == data.length) {
            data = Arrays.copyOf(data, data.length * 2);
        }
        data[size++] = o;
    }

    public Object get(int i) {
        return data[i];
    }

    public int size() {
        return size;
    }
}

public class DemoVectorObject {
    public static void main(String[] args) {
        VectorObject v = new VectorObject();
        v.push("hola");
        v.push(123); // int -> Integer (autoboxing)

        String s = (String) v.get(0);
        int n = (Integer) v.get(1);

        System.out.println(s.toUpperCase());
        System.out.println(n + 1);
    }
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La **programación genérica** consiste en definir algoritmos y estructuras de datos de forma que funcionen con *distintos tipos* sin duplicar el código para cada uno. En vez de escribir “una lista de enteros” y “una lista de cadenas” por separado, se define una única “lista de `T`”, donde `T` representa el tipo concreto que se decidirá al usarla.

El ejemplo basado en `void*` o `Object` se puede considerar un enfoque **muy básico** para “generalizar” una estructura de datos, porque permite almacenar valores de tipos diferentes en un mismo contenedor. Sin embargo, no es genérico en el sentido fuerte de “parametrizar por tipo con chequeo estático”: se pierde información de tipos y se delega la corrección al uso de conversiones (casts) y a la disciplina del programador.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El principal problema es que el compilador deja de poder verificar si “lo que se mete” y “lo que se saca” del contenedor coincide. Con `void*` o `Object` el contenedor acepta cualquier cosa, de modo que es fácil introducir un valor de un tipo y, más tarde, tratarlo como si fuera otro distinto. El fallo suele aparecer lejos del lugar donde se cometió el error (por ejemplo, se inserta mal en un punto y se rompe al recuperar en otro).

En C, un cast incorrecto sobre un `void*` no suele dar un error controlado: puede producir **comportamiento indefinido** (lectura de memoria con formato erróneo, corrupción de datos o fallo de segmentación). En Java, el cast sí se comprueba en ejecución y, si es incorrecto, aparece una `ClassCastException`, pero igualmente se trata de un error tardío (en ejecución) y obliga a hacer *downcasting* manual.

```c
int i = 7;
void* p = &i;

// Se interpreta la dirección de un int como si apuntase a double: comportamiento indefinido
double d = *(double*)p;
```

```java
Object o = 123;        // realmente es un Integer
String s = (String) o; // ClassCastException en ejecución
```


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los **parámetros de tipo** son “variables de tipo” que se declaran en una clase, interfaz o método para indicar que parte de su definición queda *pendiente de un tipo concreto*. En Java se escriben entre ángulos, por ejemplo `Caja<T>` o `static <T> T max(T a, T b)`. Al usar ese código, se sustituye `T` por un tipo real (`String`, `Integer`, …) y el compilador comprueba que todo encaja.

La idea es que el contenedor o algoritmo se pueda reutilizar sin perder seguridad de tipos: si algo se declara como `Caja<String>`, entonces el compilador garantiza que solo se guardan `String` y que al recuperarlos se obtienen `String` sin necesidad de casts. Esto aporta una forma de “polimorfismo paramétrico”: el comportamiento del código no depende de una jerarquía de herencia, sino del tipo con el que se instancia.

```java
class Caja<T> {
    private final T valor;
    public Caja(T valor) { this.valor = valor; }
    public T get() { return valor; }
}

class DemoCaja {
    public static void main(String[] args) {
        Caja<String> c = new Caja<>("hola");
        String s = c.get(); // sin casting
        System.out.println(s.toUpperCase());
    }
}
```


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java se usan **genéricos** para declarar colecciones con un tipo concreto, por ejemplo `List<String>`. El compilador impide añadir elementos de otro tipo y, al iterar, cada elemento se recupera directamente como `String`, sin *downcasting*. Es un salto respecto a `List` sin genéricos o a `List<Object>`, donde la corrección se comprobaría tarde.

En C++ se usan **templates** de forma parecida: `std::vector<std::string>` es un vector dinámico cuyos elementos son siempre `std::string`. Como el tipo forma parte de la propia instanciación del template, el compilador detecta si se intenta insertar un elemento incompatible y el recorrido produce referencias `std::string` con total seguridad de tipos.

```java
import java.util.ArrayList;
import java.util.List;

public class DemoListaString {
    public static void main(String[] args) {
        List<String> nombres = new ArrayList<>();
        nombres.add("Ana");
        nombres.add("Luis");
        // nombres.add(123); // no compila

        for (String s : nombres) {
            System.out.println(s + " (" + s.getClass().getSimpleName() + ")");
        }
    }
}
```

```cpp
#include <vector>
#include <string>
#include <iostream>

int main() {
    std::vector<std::string> nombres;
    nombres.push_back("Ana");
    nombres.push_back(std::string("Luis"));
    // nombres.push_back(123); // no compila

    for (const std::string& s : nombres) {
        std::cout << s << " (len=" << s.size() << ")\n";
    }
}
```


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Al usar una clase con parámetros de tipo, el compilador comprueba que el código se utiliza de forma coherente con esos tipos: por ejemplo, que en una `List<String>` solo se insertan `String`, o que el tipo `T` de una clase genérica se usa en sitios donde tiene sentido. Sin embargo, el modo en que esto se materializa en el código compilado **no es igual** en Java y en C++.

En **Java** se aplica el **type erasure** (borrado de tipos): tras comprobar los tipos en compilación, el compilador elimina los parámetros genéricos y los sustituye por su límite superior (por defecto `Object`, o `Number` si se usa `T extends Number`). Se generan casts y comprobaciones donde haga falta, pero en ejecución no existe una clase distinta para `List<String>` y `List<Integer>`; ambas son, esencialmente, `List`.

En **C++** los **templates** se instancian: cada combinación de tipo usada genera una versión concreta del código (por ejemplo, una para `std::vector<std::string>` y otra para `std::vector<int>`). Esto produce tipado fuerte y optimizaciones específicas (porque el tipo es conocido durante la compilación), a cambio de más trabajo de compilación y, a veces, un binario más grande.

Como consecuencia práctica, en Java no se puede, por ejemplo, comprobar en tiempo de ejecución `instanceof List<String>` (porque ese `String` se ha borrado), mientras que en C++ la especialización forma parte del propio tipo durante la compilación y se generan implementaciones específicas.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

Una clase genérica `Par<A, B>` permite agrupar dos valores de tipos potencialmente distintos sin perder información de tipos. Es una alternativa típica a “devolver dos cosas” mediante parámetros de salida (estilo C con punteros) o a crear una clase específica para cada combinación de resultados.

Al usar `Par<Double, Double>` como tipo de retorno, se expresa claramente que el método devuelve dos `Double` (media y desviación típica) y se evita cualquier casting al consumir el resultado. El compilador fuerza además que el primer componente sea siempre del tipo `A` y el segundo del tipo `B`.

```java
public final class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() {
        return primero;
    }

    public B getSegundo() {
        return segundo;
    }
}
```

```java
public class Estadistica {
    public static Par<Double, Double> mediaYDesviacion(double[] xs) {
        double suma = 0.0;
        for (double x : xs) {
            suma += x;
        }
        double media = suma / xs.length;

        double acum = 0.0;
        for (double x : xs) {
            double d = x - media;
            acum += d * d;
        }

        double varianza = acum / xs.length;
        double desviacion = Math.sqrt(varianza);
        return new Par<>(media, desviacion);
    }

    public static void main(String[] args) {
        double[] datos = { 1, 2, 3, 4 };
        Par<Double, Double> r = mediaYDesviacion(datos);

        System.out.println("media=" + r.getPrimero());
        System.out.println("desv=" + r.getSegundo());
    }
}
```


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

En Java también se pueden declarar parámetros de tipo **en métodos**, por ejemplo `public static <T> T seleccionaUno(T a, T b)`. En ese caso el compilador infiere `T` a partir de los argumentos y garantiza que el tipo de retorno es exactamente el mismo. Así se puede recuperar el resultado sin casting y con total seguridad.

Si se define el mismo método con `Object`, el compilador pierde la relación “ambos argumentos y el retorno son del mismo tipo”: se podrían pasar objetos de tipos distintos y el resultado sería un `Object`, obligando a *downcasting*. Esto es justo el tipo de situación donde los genéricos evitan fallos en ejecución, porque el error (tipos mezclados o cast inseguro) pasa a ser un error de compilación.

```java
import java.util.Random;

public class DemoSeleccionaUno {
    private static final Random RND = new Random();

    public static Object seleccionaUnoObject(Object a, Object b) {
        return RND.nextBoolean() ? a : b;
    }

    public static <T> T seleccionaUno(T a, T b) {
        return RND.nextBoolean() ? a : b;
    }

    public static void main(String[] args) {
        // Con Object: permite mezclar tipos y obliga a casting
        Object o = seleccionaUnoObject("hola", 123);
        // String s = (String) o; // puede fallar si salió 123

        // Con genéricos: ambos argumentos deben ser del mismo tipo
        String s2 = seleccionaUno("hola", "adios");
        // String s3 = seleccionaUno("hola", 123); // no compila

        System.out.println(s2.toUpperCase());
    }
}
```


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí: en Java se pueden imponer **restricciones (bounds)** en parámetros de tipo con `extends`. Por ejemplo, `<T extends Number>` obliga a que `T` sea un subtipo de `Number`, lo cual permite tratarlo como número (llamando, por ejemplo, a `doubleValue()`). De forma análoga a C++ (donde el compilador comprueba qué operaciones se usan), aquí se limita explícitamente qué tipos son válidos.

Una primera solución sin genéricos es declarar ambas coordenadas como `Number`. Así se admiten `Integer`, `Double`, etc., pero el precio es que el tipo exacto se pierde: `getX()` devuelve `Number` y se depende de conversiones numéricas (`doubleValue()`) para operar. Además, se permite mezclar subtipos distintos en un mismo punto porque ambos campos son `Number`.

La solución con genéricos es parametrizar el punto como `Punto<T extends Number>` y hacer que `x` e `y` sean de tipo `T`. Con ello se refuerza el chequeo: si se instancia `Punto<Integer>`, entonces ambas coordenadas son `Integer` y `getX()` devuelve `Integer`. Aun así, por **type erasure**, en el bytecode `T` se borra y se reemplaza por su límite superior, por lo que el tipo “real” tras compilar pasa a ser `Number` (más casts insertados por el compilador cuando haga falta).

```java
// Solución 1: sin genéricos (usa Number)
public class PuntoNumber {
    private final Number x;
    private final Number y;

    public PuntoNumber(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(PuntoNumber otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
// Solución 2: con genéricos (refuerza el tipo exacto)
public class Punto<T extends Number> {
    private final T x;
    private final T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

class DemoPuntos {
    public static void main(String[] args) {
        PuntoNumber pn = new PuntoNumber(1, 2.5); // mezcla permitida

        Punto<Integer> pi = new Punto<>(1, 2);
        // Punto<Integer> mal = new Punto<>(1, 2.5); // no compila

        System.out.println(pn.getX().getClass().getSimpleName());
        System.out.println(pi.getX().getClass().getSimpleName());
    }
}
```


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

La solución basada solo en `Number` permite mezclar subtipos sin problema: se puede crear un punto con `x` como `Integer` y `y` como `Double` porque ambos son `Number`. Esto da flexibilidad, pero también hace que sea más fácil cometer errores sutiles, ya que el compilador no distingue si cada coordenada era “int” o “double” en origen; únicamente sabe que son “algún número”.

En la solución con genéricos `Punto<T extends Number>`, si se instancia, por ejemplo, como `Punto<Integer>`, entonces **ambas coordenadas** deben ser `Integer` (y análogamente para `Double`, `Long`, etc.). Por ello no se permite (por defecto) un punto con una coordenada entera y otra real: el parámetro `T` fija un único subtipo para todo el objeto. En cuanto a los getters, `getX()` devuelve `Number` en la solución sin genéricos, mientras que devuelve `T` (por ejemplo, `Integer`) en la solución genérica.

```java
PuntoNumber p1 = new PuntoNumber(1, 2.5);
Number x1 = p1.getX();

Punto<Integer> p2 = new Punto<>(1, 2);
Integer x2 = p2.getX();
```


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Se puede evitar el `instanceof` usando un patrón de genéricos llamado a veces “**tipo propio**” (o *F-bounded polymorphism*). La idea es parametrizar la interfaz con el tipo concreto que implementa: `Punto<P extends Punto<P>>`. Así, el método `distanciaA` ya no recibe un `Punto` cualquiera, sino un `P` del mismo tipo que el objeto receptor.

Con este enfoque, `Punto2D` implementa `Punto<Punto2D>` y el compilador obliga a que `distanciaA` acepte un `Punto2D` (no un `Punto3D`). Se elimina la necesidad de ramificar por tipos y de hacer downcasting: si alguien intenta calcular la distancia de un `Punto2D` a un `Punto3D`, el error aparece en compilación.

```java
public interface Punto<P extends Punto<P>> {
    double distanciaA(P p);
}

public class Punto2D implements Punto<Punto2D> {
    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}

class DemoPuntosGenericos {
    public static void main(String[] args) {
        Punto2D a = new Punto2D(0, 0);
        Punto2D b = new Punto2D(3, 4);
        System.out.println(a.distanciaA(b));

        Punto3D c = new Punto3D(0, 0, 0);
        // System.out.println(a.distanciaA(c)); // no compila
    }
}
```


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

Aunque `String` sea subtipo de `Object`, **no** se cumple que `List<String>` sea subtipo de `List<Object>` en Java: los genéricos son **invariantes** por defecto. Si se permitiese esa relación, se podría tratar una `List<String>` como si fuera `List<Object>` e insertar un `Integer` dentro; la lista quedaría “contaminada” y, al recuperar, se rompería la promesa de que eran `String`.

En cambio, los arrays en Java sí son **covariantes**, por lo que `String[]` *sí* es subtipo de `Object[]`. Esta decisión es histórica y se compensa con comprobaciones en tiempo de ejecución: el array recuerda su tipo real de elementos y lanza `ArrayStoreException` si se intenta insertar algo incompatible. El problema es que el error aparece tarde (en ejecución), justo lo contrario de lo que se busca con genéricos.

```java
Object[] a = new String[1];
a[0] = 123; // compila, pero falla en ejecución: ArrayStoreException
```

Un tipo parametrizado `G<T>` es **covariante** si de `A <: B` se deduce `G<A> <: G<B>` (como ocurre con arrays en Java). Es **contravariante** si de `A <: B` se deduce `G<B> <: G<A>` (útil para “consumidores” de valores). Es **invariante** si no existe relación de subtipo entre `G<A>` y `G<B>` salvo que `A` y `B` sean exactamente el mismo tipo; este es el comportamiento estándar de los genéricos en Java.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un **wildcard** (`?`) representa “un tipo desconocido” como argumento de un genérico y sirve para aceptar más casos sin perder seguridad. Por ejemplo, `List<? extends Number>` significa “una lista de *algún subtipo* de `Number`” (podría ser `List<Integer>`, `List<Double>`, etc.). Esto permite leer elementos como `Number`, pero impide añadir nuevos elementos (salvo `null`), porque no se sabe cuál era exactamente el subtipo concreto.

En cambio, `List<? super Integer>` significa “una lista de *algún supertipo* de `Integer`” (por ejemplo `List<Integer>`, `List<Number>` o `List<Object>`). Aquí sí es seguro **añadir `Integer`**, porque cualquier supertipo puede almacenar un `Integer`. La lectura, sin embargo, suele quedar limitada a `Object`, ya que el tipo exacto podría ser más general. Esta asimetría se resume a menudo con la regla *PECS*: *Producer Extends, Consumer Super*.

```java
import java.util.List;

public class DemoWildcards {
    // (i) "extends": la lista produce Numbers (se leen)
    public static double suma(List<? extends Number> xs) {
        double s = 0.0;
        for (Number n : xs) {
            s += n.doubleValue();
        }
        return s;
    }

    // (ii) "super": la lista consume Integers (se escriben)
    public static void agregaEnteros(List<? super Integer> xs) {
        xs.add(1);
        xs.add(2);
        xs.add(3);
    }
}
```
