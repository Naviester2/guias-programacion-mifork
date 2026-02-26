<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

En C no existe un mecanismo integrado de excepciones, por lo que el programador debe idear formas manuales de señalizar errores al código que llama a la función. Esto obliga a establecer convenciones que tanto la función como el llamador deben respetar. A continuación se presentan dos opciones habituales.

**Opción 1: Devolver un valor especial (valor centinela).** Se puede hacer que la función devuelva un valor negativo (por ejemplo `-1.0`) cuando el argumento no es válido, ya que una raíz cuadrada nunca devuelve un número negativo. El llamador debe comprobar el valor devuelto para saber si hubo error.

```c
#include <stdio.h>
#include <math.h>

double raiz(double n) {
    if (n < 0) {
        return -1.0; // Valor centinela que indica error
    }
    return sqrt(n);
}

int main() {
    double resultado = raiz(-9.0);
    if (resultado < 0) {
        printf("Error: no se puede calcular la raíz de un número negativo.\n");
    } else {
        printf("Resultado: %f\n", resultado);
    }
    return 0;
}
```

**Opción 2: Usar un parámetro de salida para el error.** Se puede pasar un puntero a una variable entera que la función modifica para indicar si la operación fue exitosa o no. El resultado se devuelve normalmente por `return`, y el llamador consulta la variable de error.

```c
#include <stdio.h>
#include <math.h>

double raiz(double n, int *error) {
    if (n < 0) {
        *error = 1;
        return 0.0;
    }
    *error = 0;
    return sqrt(n);
}

int main() {
    int error;
    double resultado = raiz(-9.0, &error);
    if (error) {
        printf("Error: no se puede calcular la raíz de un número negativo.\n");
    } else {
        printf("Resultado: %f\n", resultado);
    }
    return 0;
}
```

Ambas opciones tienen el inconveniente de que el llamador puede ignorar la comprobación de error, lo que provocaría que el programa continuase con datos incorrectos sin que nadie se percatara. Además, el código de manejo de errores se mezcla con la lógica principal, dificultando la legibilidad.


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una excepción es un mecanismo que proporcionan ciertos lenguajes de programación (como Java, C++, Python, etc.) para señalizar y manejar situaciones anómalas o errores que se producen durante la ejecución de un programa. Cuando ocurre una condición inesperada —por ejemplo, un argumento inválido, un fichero que no existe o una división por cero—, se "lanza" una excepción que interrumpe el flujo normal de ejecución.

Cuando un programador implementa una función, utiliza excepciones para comunicar al llamador que algo ha ido mal sin necesidad de recurrir a valores de retorno especiales ni a parámetros de salida adicionales. De este modo, la función se centra exclusivamente en su lógica principal y delega la decisión de cómo manejar el error al código que la invoca.

Desde el lado del llamador, las excepciones permiten separar claramente el código de la lógica normal del código de manejo de errores. El programador que llama a una función puede decidir en qué punto de su código desea capturar y gestionar el error, lo que produce un código más limpio, legible y mantenible que las técnicas manuales utilizadas en C.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

En Java se puede utilizar el mecanismo de excepciones para señalizar el error directamente desde el método `raiz`. Si el argumento es negativo, se lanza una excepción con `throw`, lo que interrumpe la ejecución del método y transfiere el control al código llamador. No es necesario devolver valores centinela ni usar parámetros de salida.

Desde el método `main`, se rodea la llamada a `raiz` con un bloque `try-catch`. Si el método se ejecuta sin problemas, el flujo continúa con normalidad. Si se lanza una excepción, el bloque `catch` la captura y permite mostrar un mensaje de error al usuario. De este modo, la lógica de la función y el manejo del error quedan separados limpiamente.

```java
public class Calculadora {

    public static double raiz(double n) {
        if (n < 0) {
            throw new IllegalArgumentException("No se puede calcular la raíz de un número negativo: " + n);
        }
        return Math.sqrt(n);
    }

    public static void main(String[] args) {
        try {
            double resultado = Calculadora.raiz(-9.0);
            System.out.println("Resultado: " + resultado);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

Como se puede observar, el método `raiz` no necesita preocuparse de cómo se presenta el error al usuario; simplemente lanza la excepción con un mensaje descriptivo. Es el `main` (el llamador) quien decide qué hacer con ella, en este caso imprimirla por pantalla.


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

**Lanzar** una excepción consiste en crear un objeto excepción y emitirlo mediante la palabra clave `throw`. En ese instante se interrumpe la ejecución normal del método y el control pasa al sistema de gestión de excepciones de la máquina virtual. En el ejemplo, `throw new IllegalArgumentException(...)` dentro de `raiz` es el acto de lanzar la excepción.

**Controlar** o **capturar** una excepción significa atraparla en un bloque `catch` para ejecutar código alternativo de manejo del error. En el ejemplo, el bloque `catch (IllegalArgumentException e)` del `main` es el que captura la excepción y muestra el mensaje al usuario. El flujo de ejecución continúa después del bloque `try-catch`.

**Propagar** una excepción significa que, cuando un método no la captura, la excepción "sube" automáticamente por la pila de llamadas hacia el método que invocó al actual, y así sucesivamente. Si imaginamos que `main` llama a un método `calcular`, y este a su vez llama a `raiz`, cuando `raiz` lanza la excepción y `calcular` no tiene un `try-catch`, la excepción se propaga a `main`. Los métodos por los que pasa la excepción se abandonan inmediatamente: todo el código que quedaba por ejecutar en ellos se omite. **No se reanudan después**; simplemente se descartan de la pila como si nunca hubieran terminado normalmente.

```java
public class Calculadora {

    public static double raiz(double n) {
        if (n < 0) {
            throw new IllegalArgumentException("Número negativo: " + n);
        }
        return Math.sqrt(n);
    }

    // Este método NO captura la excepción, así que se propaga
    public static void calcular() {
        double r = raiz(-9.0);
        // Esta línea NUNCA se ejecuta si raiz lanza excepción
        System.out.println("Resultado: " + r);
    }

    public static void main(String[] args) {
        try {
            calcular();
            // Esta línea tampoco se ejecuta si hay excepción
            System.out.println("Cálculo completado.");
        } catch (IllegalArgumentException e) {
            System.out.println("Error capturado en main: " + e.getMessage());
        }
        // La ejecución continúa aquí tras el catch
        System.out.println("El programa sigue.");
    }
}
```

En este ejemplo, `raiz` lanza la excepción, `calcular` no la captura (se interrumpe y se descarta de la pila), y finalmente `main` la captura. Solo el código posterior al bloque `try-catch` en `main` continúa ejecutándose.


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

### Respuesta

En C, si una función detecta un error, debe devolver un código de error y **cada** función intermedia de la cadena de llamadas tiene que comprobar y reenviar ese código manualmente hacia arriba. Esto produce código muy repetitivo lleno de `if (error) return error;` en cada nivel de la pila, lo que ensucia la lógica principal y es propenso a olvidos: si un programador olvida comprobar el valor de retorno en algún nivel intermedio, el error se pierde silenciosamente.

Con las excepciones, la propagación es automática. Si un método no captura una excepción, esta sube sola al método anterior en la pila de llamadas, sin que el programador tenga que escribir código de reenvío en los métodos intermedios. Esto significa que cada función solo necesita preocuparse del error si realmente le compete manejarlo; las demás simplemente dejan pasar la excepción de forma transparente.

Además, esta propagación natural garantiza que los errores **nunca se ignoran silenciosamente**. Si nadie captura la excepción, el programa termina mostrando información del error (traza de la pila), en lugar de continuar con datos corruptos. El resultado es un código más limpio, donde la lógica de negocio está separada del manejo de errores, y más robusto, porque el programador puede elegir el nivel exacto de la pila donde tiene sentido gestionar cada tipo de error.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

Sí, en lenguajes orientados a objetos como Java, las excepciones son objetos. Toda excepción es una instancia de una clase que hereda (directa o indirectamente) de `Throwable`. En la práctica, las excepciones más habituales heredan de `Exception` o de `RuntimeException`. Esto significa que una excepción posee atributos y métodos como cualquier otro objeto.

En términos de encapsulación, que las excepciones sean objetos permite empaquetar dentro de ellas toda la información relevante sobre el error: un mensaje descriptivo, la traza de la pila de llamadas, e incluso datos adicionales personalizados (como el valor erróneo que provocó el fallo). Frente al enfoque de C, donde solo se dispone de un entero o un valor centinela, aquí se transporta un paquete completo de información que facilita enormemente la depuración y el manejo del error.

Efectivamente, se pueden crear excepciones personalizadas definiendo nuevas clases que extiendan `Exception` o `RuntimeException`. Esto es muy útil para representar errores específicos del dominio de la aplicación. Por ejemplo:

```java
public class NumeroNegativoException extends Exception {
    private double numero;

    public NumeroNegativoException(double numero) {
        super("Número negativo no permitido: " + numero);
        this.numero = numero;
    }

    public double getNumero() {
        return numero;
    }
}
```

De este modo, el llamador que capture esta excepción puede acceder no solo al mensaje, sino también al número concreto que causó el error mediante `getNumero()`, aprovechando las ventajas de la encapsulación.


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

Cualquier objeto excepción en Java lleva consigo, como mínimo, tres piezas de información esenciales. La primera es el **mensaje descriptivo** (`getMessage()`), una cadena de texto que explica qué ocurrió, por ejemplo: `"Número negativo no permitido: -9.0"`. La segunda es la **traza de la pila de llamadas** (*stack trace*), accesible con `printStackTrace()` o `getStackTrace()`, que indica exactamente en qué clase, método y línea de código se produjo el error, y todo el camino de llamadas que llevó hasta ese punto.

La tercera es la **causa** (`getCause()`), que permite encadenar excepciones: si una excepción fue provocada por otra de nivel inferior, se puede almacenar la original como causa. Además, como se vio en la pregunta anterior, al ser objetos, se pueden añadir atributos propios adicionales con información de contexto específica del dominio.

En C, cuando se maneja un error mediante un código de retorno o un valor centinela, no se dispone de ninguna de esta información: solo se sabe que hubo un error (un `-1` o un `1`), pero no se conoce en qué punto exacto del código se originó ni cuál fue la secuencia de llamadas que llevó hasta allí. El objeto excepción en Java encapsula toda esta información y la transporta automáticamente hasta el manejador, lo que facilita enormemente la depuración y el diagnóstico de problemas.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

Sí, se pueden tener múltiples bloques `catch` asociados a un mismo bloque `try`. Cada bloque `catch` especifica un tipo de excepción diferente que se desea capturar, lo que permite dar un tratamiento distinto a cada tipo de error. Es importante ordenar los bloques `catch` de más específico a más general, ya que Java evalúa los bloques en orden y ejecuta el primero cuyo tipo coincida con la excepción lanzada.

Solo se ejecuta **un único** bloque `catch` por cada excepción lanzada: el primero que coincida con el tipo de la excepción. Si se coloca primero un `catch` genérico (por ejemplo, `catch (Exception e)`), los bloques más específicos que vengan después nunca se alcanzarán, y de hecho el compilador de Java genera un error en esa situación.

```java
try {
    double resultado = Calculadora.raiz(-9.0);
    System.out.println(resultado);
} catch (IllegalArgumentException e) {
    System.out.println("Argumento inválido: " + e.getMessage());
} catch (ArithmeticException e) {
    System.out.println("Error aritmético: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Error genérico: " + e.getMessage());
}
```

En este ejemplo, si `raiz` lanza una `IllegalArgumentException`, solo se ejecuta el primer `catch`. Los demás se ignoran. Si lanzase una excepción de otro tipo no cubierta por los dos primeros `catch`, la capturaría el tercero al ser `Exception` la clase padre de todas las excepciones.


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

Java proporciona el bloque `finally` precisamente para este propósito. El código dentro de `finally` se ejecuta **siempre**, tanto si el bloque `try` se completó con normalidad como si se lanzó una excepción (capturada o no). Esto lo convierte en el lugar ideal para liberar recursos como ficheros abiertos, conexiones de red o de base de datos, etc.

El bloque `finally` puede combinarse con `catch` (para manejar la excepción y además ejecutar código de limpieza) o puede usarse directamente con `try` sin ningún `catch` (cuando no se desea capturar la excepción, pero sí garantizar la liberación de recursos antes de que siga propagándose).

**Ejemplo con `catch` y `finally`:**

```java
import java.io.*;

public class EjemploFinally {
    public static void main(String[] args) {
        FileReader fichero = null;
        try {
            fichero = new FileReader("datos.txt");
            // Operaciones de lectura...
            System.out.println("Fichero leído correctamente.");
        } catch (FileNotFoundException e) {
            System.out.println("Error: fichero no encontrado.");
        } finally {
            if (fichero != null) {
                try {
                    fichero.close();
                } catch (IOException e) {
                    System.out.println("Error al cerrar el fichero.");
                }
            }
            System.out.println("Bloque finally ejecutado.");
        }
    }
}
```

**Ejemplo con `try-finally` sin `catch`:**

```java
import java.io.*;

public class EjemploFinallySinCatch {
    public static void leerFichero() throws FileNotFoundException {
        FileReader fichero = null;
        try {
            fichero = new FileReader("datos.txt");
            // Operaciones de lectura...
        } finally {
            if (fichero != null) {
                try {
                    fichero.close();
                } catch (IOException e) {
                    System.out.println("Error al cerrar el fichero.");
                }
            }
            System.out.println("Recurso liberado, la excepción sigue propagándose.");
        }
    }
}
```

En el segundo ejemplo, si se produce una `FileNotFoundException`, el bloque `finally` se ejecuta igualmente para cerrar el recurso, y a continuación la excepción continúa propagándose hacia el método llamador, ya que no se ha capturado.


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Sí, el bloque `finally` puede ir sin `catch`. Una construcción `try-finally` (sin ningún `catch`) es perfectamente válida en Java. Se utiliza cuando no se desea manejar la excepción en ese método, pero sí se necesita garantizar que se ejecute un código de limpieza antes de que la excepción se propague.

El bloque `finally` se ejecuta **siempre**, sin importar lo que suceda dentro del `try`: se ejecuta si el `try` termina normalmente, si se lanza una excepción (sea capturada o no), e incluso si hay un `return` dentro del `try`. En este último caso, el valor de retorno se calcula, luego se ejecuta el bloque `finally`, y finalmente se devuelve el valor.

```java
public class EjemploReturn {
    public static int metodo() {
        try {
            System.out.println("Dentro del try");
            return 42;
        } finally {
            System.out.println("Finally se ejecuta ANTES de que el return se complete");
        }
    }

    public static void main(String[] args) {
        int valor = metodo();
        System.out.println("Valor devuelto: " + valor);
    }
}
// Salida:
// Dentro del try
// Finally se ejecuta ANTES de que el return se complete
// Valor devuelto: 42
```

Como se puede comprobar, aunque el `return 42` aparece antes del `finally`, Java garantiza que el bloque `finally` se ejecute antes de que el método realmente devuelva el control al llamador. Es una garantía muy fuerte que hace de `finally` el lugar idóneo para liberar recursos.


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

Las excepciones **controladas** (*checked exceptions*) son aquellas que el compilador obliga a gestionar: el programador debe capturarlas con `try-catch` o declararlas en la firma del método con `throws`. Heredan de `Exception` pero **no** de `RuntimeException`. Ejemplos típicos son `IOException`, `FileNotFoundException` o `SQLException`. Las excepciones **no controladas** (*unchecked exceptions*) son aquellas que el compilador no obliga a capturar ni a declarar. Heredan de `RuntimeException` (o de `Error`). Ejemplos típicos son `NullPointerException`, `IllegalArgumentException`, `ArrayIndexOutOfBoundsException` o `ArithmeticException`.

El papel de `RuntimeException` es actuar como clase base de todas las excepciones no controladas. Cualquier excepción que la extienda no requiere ser declarada ni capturada obligatoriamente. La filosofía detrás de esta distinción es que las excepciones no controladas suelen representar errores de programación (bugs) que deberían corregirse en el código, mientras que las controladas representan condiciones externas que el programa no puede evitar y debe estar preparado para manejar.

**Situaciones donde se prefiere una excepción controlada:**
- Intentar abrir un fichero que podría no existir (`FileNotFoundException`).
- Realizar una conexión de red que podría fallar (`IOException`).
- Consultar una base de datos que podría no estar disponible (`SQLException`).
- Parsear una fecha con un formato que depende de la entrada del usuario (`ParseException`).

**Situaciones donde se prefiere una excepción no controlada:**
- Recibir un argumento `null` cuando no se espera (`NullPointerException`).
- Recibir un parámetro fuera de rango válido (`IllegalArgumentException`).
- Acceder a un índice fuera de los límites de un array (`ArrayIndexOutOfBoundsException`).
- Intentar usar un objeto en un estado inválido (`IllegalStateException`).

Como ejemplo propio, se podría crear una excepción controlada `FicheroConfiguracionException extends Exception` para cuando falla la lectura de un fichero de configuración, y una excepción no controlada `ValorNegativoException extends RuntimeException` para cuando un método recibe un valor negativo que jamás debería haberse pasado.


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

La palabra clave `throws` se coloca en la firma de un método para declarar que dicho método puede lanzar uno o varios tipos de excepciones controladas que no va a capturar internamente. Con `throws`, el método informa al compilador (y al programador que lo use) de que la excepción puede propagarse hacia arriba, y que será responsabilidad del llamador gestionarla.

Es una alternativa a capturar la excepción con `try-catch` porque, cuando el compilador detecta que un método utiliza código que puede lanzar una excepción controlada, exige una de dos cosas: o bien se captura la excepción dentro del método (con `try-catch`), o bien se declara con `throws` para delegar la responsabilidad al método que lo invoque. Ambas opciones satisfacen al compilador.

Esto resulta muy útil cuando un método no tiene el contexto adecuado para decidir qué hacer con el error. Por ejemplo, un método que lee un fichero puede no saber si debe mostrar un mensaje al usuario, reintentar la lectura o terminar el programa. En ese caso, lo más razonable es declarar `throws IOException` y dejar que sea el método llamador —que sí tiene ese contexto— quien tome la decisión.


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

A continuación se muestra un método `leerFichero` que abre un fichero y lee su primera línea, pero que no desea manejar la excepción `FileNotFoundException` ni `IOException`. Mediante `throws` se declara que estas excepciones pueden propagarse al método llamador. Sin embargo, se utiliza `finally` para garantizar que el recurso se cierra siempre, haya error o no.

```java
import java.io.*;

public class LectorFichero {

    public static String leerPrimeraLinea(String ruta) throws IOException {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader(ruta));
            return reader.readLine();
        } finally {
            if (reader != null) {
                reader.close();
            }
        }
    }

    public static void main(String[] args) {
        try {
            String linea = leerPrimeraLinea("datos.txt");
            System.out.println("Primera línea: " + linea);
        } catch (IOException e) {
            System.out.println("Error al leer el fichero: " + e.getMessage());
        }
    }
}
```

El método `leerPrimeraLinea` declara `throws IOException` (que es la clase padre de `FileNotFoundException`), por lo que no necesita `catch`. Si el fichero no existe, la excepción se propaga al `main`, donde sí se captura. El bloque `finally` se ejecuta en cualquier caso, asegurando que el `BufferedReader` se cierra correctamente tanto si la lectura fue exitosa como si se produjo una excepción.


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, es sintácticamente válido poner excepciones no controladas como `RuntimeException` o `IllegalArgumentException` en la cláusula `throws` de un método. El compilador no genera ningún error por ello. Sin embargo, hacerlo **no obliga** al llamador a capturarlas, ya que siguen siendo excepciones no controladas independientemente de que aparezcan en `throws`.

El método llamador no está obligado a poner `try-catch` en ese caso, porque el compilador no lo exige para excepciones no controladas. Podría hacerlo voluntariamente si desea manejar esa situación, pero no hay ninguna imposición.

El sentido de declarar una excepción no controlada en `throws` es puramente **documental**: sirve para advertir explícitamente al programador que use ese método de que puede producirse esa excepción bajo ciertas condiciones. Es una forma de hacer visible en la firma del método un posible error, aunque no se fuerce su captura. En la práctica, para este propósito suele preferirse documentar las excepciones no controladas en el Javadoc del método (con la etiqueta `@throws`) en lugar de incluirlas en la cláusula `throws` de la firma.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Se recomienda usar excepciones **controladas** cuando la situación de error es **externa** al programa y el llamador **puede y debería** hacer algo al respecto: un fichero que no existe, una conexión de red que falla, un formato de datos inválido proporcionado por el usuario, etc. Son situaciones que pueden ocurrir incluso en un programa correctamente escrito. En cambio, se recomiendan excepciones **no controladas** cuando el error representa un **bug** o una violación de precondiciones que el programador debería haber evitado: pasar `null` donde no se espera, un índice fuera de rango, un argumento fuera del dominio válido, etc.

No todos los lenguajes ofrecen ambas opciones. De hecho, Java es uno de los pocos lenguajes que implementa excepciones controladas. Lenguajes como C#, Python, Kotlin, Scala, Ruby o JavaScript solo tienen excepciones no controladas: el compilador nunca obliga a capturar ni declarar ninguna excepción. En C++ existe una sintaxis para especificaciones de excepción, pero fue deprecada y eliminada en estándares modernos.

En los lenguajes que solo ofrecen una opción, la más habitual son las **excepciones no controladas**. La tendencia general en la industria es dejar la decisión de capturar o no en manos del programador, sin imposiciones del compilador. De hecho, incluso en el ecosistema Java, muchos frameworks modernos (como Spring) prefieren usar excepciones no controladas para simplificar el código.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Sí, tiene perfecto sentido lanzar excepciones dentro de un bloque `catch`. Es una práctica habitual cuando se desea **transformar** una excepción de bajo nivel en otra más significativa para el contexto de la aplicación. Por ejemplo, se puede capturar una `SQLException` y lanzar en su lugar una `ServicioNoDisponibleException` personalizada, de modo que las capas superiores no necesiten conocer detalles de la base de datos.

También se puede **relanzar la misma excepción** capturada, usando simplemente `throw e;`. Esto tiene sentido cuando se desea realizar alguna acción en el `catch` (por ejemplo, registrar el error en un log o liberar un recurso) pero no se quiere "tragarse" la excepción, sino dejar que siga propagándose hacia arriba para que otro nivel la maneje definitivamente.

**Ejemplo 1: Lanzar una excepción diferente dentro del `catch`:**

```java
public class ServicioUsuarios {

    public static String buscarUsuario(int id) throws ServicioException {
        try {
            // Simulación de consulta a base de datos
            throw new java.sql.SQLException("Conexión perdida");
        } catch (java.sql.SQLException e) {
            // Se transforma en una excepción de nivel de servicio
            throw new ServicioException("No se pudo buscar el usuario con id: " + id, e);
        }
    }
}
```

**Ejemplo 2: Relanzar la misma excepción tras registrar el error:**

```java
public class Procesador {

    public static void procesar(String datos) {
        try {
            if (datos == null) {
                throw new IllegalArgumentException("Los datos no pueden ser null");
            }
            // Procesamiento...
        } catch (IllegalArgumentException e) {
            System.err.println("LOG: Error detectado - " + e.getMessage());
            throw e; // Se relanza la misma excepción tras hacer logging
        }
    }
}
```

En el primer caso se crea una excepción nueva de más alto nivel manteniendo la original como causa. En el segundo, se re-lanza exactamente la misma excepción después de registrar el error, para que el llamador pueda manejarla.


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

Cuando una excepción es la **causa** de otra, significa que el error original de bajo nivel provocó un error de más alto nivel. Java permite encadenar excepciones pasando la excepción original como parámetro al constructor de la nueva. Así se conserva toda la información del error original (su mensaje, su traza de pila) dentro de la nueva excepción. Se accede a la causa mediante el método `getCause()`.

Esto es especialmente útil en arquitecturas por capas: la capa de servicio no debería exponer excepciones de la base de datos (`SQLException`) a la capa de presentación, pero tampoco debería perder la información del error original. Encapsulando la excepción de bajo nivel como causa de una excepción de alto nivel, se consigue lo mejor de ambos mundos.

```java
// Excepción personalizada de alto nivel
public class RepositorioException extends Exception {
    public RepositorioException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

// Clase que usa la excepción
public class RepositorioUsuarios {

    public static String buscar(int id) throws RepositorioException {
        try {
            // Simulación: la base de datos lanza una excepción de bajo nivel
            throw new java.sql.SQLException("Timeout de conexión");
        } catch (java.sql.SQLException e) {
            throw new RepositorioException("Error al buscar usuario con id " + id, e);
        }
    }

    public static void main(String[] args) {
        try {
            buscar(42);
        } catch (RepositorioException e) {
            e.printStackTrace();
        }
    }
}
```

Cuando una excepción con causa se imprime por pantalla (con `printStackTrace()`), **sí se ve** la cadena completa. La salida muestra primero la excepción de alto nivel con su traza, y a continuación una línea `Caused by:` seguida de la excepción original con su propia traza. Esto es extremadamente útil para depuración, ya que permite ver tanto el error que percibe la capa de alto nivel como el error técnico real que lo causó.

