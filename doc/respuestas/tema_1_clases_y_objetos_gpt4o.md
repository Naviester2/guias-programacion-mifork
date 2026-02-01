<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

### En programación orientada a objetos suelen destacarse cuatro características básicas que permiten organizar el software alrededor de “objetos” (datos + operaciones) en lugar de centrarse solo en funciones sueltas, como suele ocurrir en C. La idea general es agrupar el estado y el comportamiento relacionados, y definir reglas claras de uso y extensión del código.

### Encapsulación: consiste en encerrar datos y métodos que trabajan con esos datos dentro de una misma unidad (la clase) y controlar el acceso al estado interno. Así se evita que el resto del programa modifique variables “a lo bruto” y se obliga a usar operaciones bien definidas (similar a ocultar detalles en un .c y exponer solo lo necesario en un .h, pero aplicado por objeto).
### Abstracción: implica quedarse con lo esencial y ocultar complejidad innecesaria. Se define qué puede hacer un objeto (su interfaz) sin exigir conocer cómo lo hace por dentro. Esto ayuda a pensar en “qué” se necesita (p. ej., “calcular área”) sin depender del “cómo” (fórmulas, validaciones, representación interna).
### Herencia: permite crear clases nuevas a partir de otras, reutilizando atributos y métodos comunes y añadiendo o modificando lo específico. Se usa para modelar relaciones del tipo “es un/una” (por ejemplo, Círculo es una Figura), reduciendo duplicación y facilitando extender familias de tipos.
### Polimorfismo: posibilita que una misma operación (por ejemplo, dibujar() o area()) se comporte de forma distinta según el tipo concreto del objeto que la ejecuta. En la práctica, se puede escribir código que trabaje con una referencia “general” (como Figura) y, al llamarse a un método, se ejecute la versión apropiada (Círculo, Rectángulo, etc.), lo que mejora la flexibilidad y el mantenimiento.

### En conjunto, estas características permiten que el código sea más modular y escalable: la encapsulación protege invariantes, la abstracción simplifica el uso, la herencia facilita reutilización y extensión, y el polimorfismo reduce condicionales y acoplamiento al permitir tratar objetos distintos de manera uniforme. Con base previa de C/C++ estructurado, puede verse como un paso desde “datos en structs + funciones externas” hacia “datos + funciones asociadas y controladas” con mecanismos formales para reutilizar y sustituir comportamientos.


## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

### Se pueden citar muchos lenguajes que soportan programación orientada a objetos, pero conviene mencionar algunos muy extendidos en docencia e industria. En general, “permitir POO” significa que el lenguaje ofrece mecanismos como clases, objetos, encapsulación y, en muchos casos, herencia y polimorfismo.
### Cuatro ejemplos populares son:

### Java: lenguaje muy usado en entornos académicos y empresariales, diseñado con POO como eje central.
### C++: evolución de C que añade clases y otras características de POO, manteniendo compatibilidad con programación de bajo nivel.
### Python: lenguaje de propósito general donde “todo es un objeto” en gran medida, con soporte de clases y herencia de forma sencilla.
### C#: lenguaje orientado a la plataforma .NET, con POO completa y una sintaxis cercana a Java/C++ en varios aspectos.


## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

### La programación estructurada es un paradigma que propone construir programas mediante estructuras de control bien definidas y un flujo de ejecución claro, evitando saltos arbitrarios (por ejemplo, el abuso de goto). Se basa en componer soluciones a partir de secuencia, selección (if/else, switch) e iteración (for, while), organizando el código en bloques y subrutinas para que el comportamiento sea más fácil de seguir, probar y depurar. En términos cercanos a C, la idea es “pensar en algoritmos y control de flujo”, dividiendo el problema en funciones que se llaman de manera ordenada.
### La programación modular va un paso más allá en organización: se centra en dividir el sistema en módulos relativamente independientes, cada uno con una responsabilidad concreta y una interfaz clara hacia el exterior. Un módulo agrupa código y, a menudo, datos relacionados, procurando alta cohesión (lo del módulo está relacionado entre sí) y bajo acoplamiento (depende lo mínimo posible del resto). En C esto suele verse como separar en ficheros .c/.h, exponiendo solo lo necesario mediante funciones y tipos, y ocultando detalles internos para facilitar mantenimiento y reutilización.
### Ambos paradigmas son “anteriores” a la POO en el sentido de que no requieren objetos para aportar orden: la programación estructurada disciplina el cómo se escribe el flujo del programa, y la modular disciplina el cómo se organiza el programa en piezas. La POO suele integrar estas ideas y añadir otra capa: además de modularizar por funcionalidades, se permite modelar el software alrededor de entidades con estado y comportamiento (clases/objetos) con encapsulación, herencia y polimorfismo. En resumen: estructurada = control de flujo limpio; modular = separación en componentes con interfaces; POO = módulos centrados en objetos con mecanismos extra para encapsular y extender comportamientos.

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

### En POO, un objeto suele definirse por tres elementos: identidad, estado y comportamiento. Esta triple idea sirve para distinguir “qué cosa es”, “qué datos guarda” y “qué sabe hacer”, separando de forma natural la información interna de las operaciones que se realizan sobre ella.
### La identidad es lo que permite diferenciar un objeto de otro, incluso si ambos tienen los mismos valores en sus datos. Por ejemplo, dos “cuentas” con el mismo saldo pueden seguir siendo objetos distintos porque representan entidades diferentes (dos instancias separadas). La identidad se relaciona con el hecho de que un objeto existe como unidad propia en memoria y puede ser referenciado de manera única.
### El estado corresponde a los atributos o datos internos del objeto (por ejemplo, saldo, titular, limite). Es el conjunto de valores que describen la situación actual del objeto en un momento dado y que cambia a lo largo del tiempo cuando se ejecutan operaciones. Para alguien que viene de C, se parece a los campos de un struct, pero en POO se entiende que ese estado está “protegido” y se accede a él de forma controlada.
### El comportamiento son los métodos u operaciones que el objeto ofrece (por ejemplo, ingresar(), retirar(), consultarSaldo()). Estas operaciones definen cómo se modifica o consulta el estado y qué reglas se aplican (validaciones, invariantes, etc.). En comparación con C, puede verse como “funciones asociadas a un struct”, con la diferencia de que el lenguaje y el diseño fomentan que dichas operaciones formen parte del propio objeto y sean la vía normal para interactuar con sus datos.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

### Una clase es una plantilla o molde que define cómo serán ciertos elementos del programa: qué datos (atributos) tendrán y qué operaciones (métodos) podrán realizar. Sirve para describir un “tipo” de entidad a nivel conceptual y de código, de manera que se puedan crear múltiples elementos con la misma estructura y comportamiento general. En términos cercanos a C, puede compararse a un struct junto con el conjunto de funciones que operan sobre ese struct, pero integradas y con control de acceso.
### Un objeto no es lo mismo que una clase: el objeto es el “resultado concreto” creado a partir de esa plantilla. La clase describe cómo es algo; el objeto es ese algo existiendo en memoria con valores específicos. Por ejemplo, una clase puede describir “CuentaBancaria”, mientras que un objeto concreto sería “la cuenta de Ana con saldo 1200”, con su propio estado. Por ello, la clase es una definición; el objeto es una entidad con identidad y estado en un instante dado.
### Una instancia es, precisamente, un objeto creado a partir de una clase. En la práctica, “objeto” e “instancia” se usan muchas veces como sinónimos, aunque “instancia” enfatiza la relación de “pertenencia” a una clase (es una instancia de esa clase). Así, al crear varios objetos de la misma clase, se obtienen varias instancias independientes: comparten la misma definición (métodos y atributos), pero no comparten necesariamente los mismos valores.
### No todos los lenguajes orientados a objetos manejan el concepto de clase de la misma manera, e incluso algunos no lo usan como base. Existen lenguajes basados en prototipos (como JavaScript en su modelo clásico) donde los objetos pueden crearse a partir de otros objetos y heredarse propiedades sin necesidad de una “clase” como molde formal. Aun así, muchos lenguajes populares de POO sí giran alrededor de clases (Java, C++, C#), porque facilitan la definición de tipos, la organización del código y mecanismos como la encapsulación y el polimorfismo.


## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

### Los objetos (entendidos como “instancias con estado”) pueden almacenarse en distintas zonas de memoria según el lenguaje y el modo de creación. De forma clásica se habla de pila (stack) para almacenamiento automático asociado a un ámbito (por ejemplo, variables locales) y de montón (heap) para almacenamiento dinámico cuya vida útil no depende directamente de un bloque de código. También existe almacenamiento estático/global, cuya duración suele abarcar toda la ejecución del programa. En muchos diseños, lo que se pasa y se guarda con frecuencia no es el objeto completo, sino una referencia/puntero al objeto; esa referencia puede estar en la pila u otras estructuras, mientras que el objeto real suele estar en el heap.
### No es igual en todos los lenguajes. En C++ puede haber objetos en la pila (creados como variables locales), en el heap (creados con new o contenedores que reservan dinámicamente) o en almacenamiento estático (globales o static). En Java, en el modelo mental habitual, los objetos se crean en el heap, y las variables locales suelen contener referencias (no el objeto “embebido”); aunque el compilador/JIT puede aplicar optimizaciones como escape analysis y “no materializar” ciertos objetos de la forma esperada, el programador no controla manualmente la zona de memoria. En lenguajes como Python, los objetos también residen típicamente en el heap y se manejan mediante referencias, mientras que detalles como el conteo de referencias o el recolector dependen del intérprete concreto.
### La recolección de basura (garbage collection, GC) es un mecanismo de gestión automática de memoria que detecta qué objetos ya no son accesibles desde el programa (es decir, ya no hay referencias “vivas” hacia ellos) y libera su memoria sin que se tenga que llamar explícitamente a free/delete. En lugar de depender del programador para decidir cuándo liberar, el entorno de ejecución mantiene información de referencias y ejecuta ciclos de limpieza: por ejemplo, algoritmos de marcado y barrido (mark & sweep) o enfoques generacionales que limpian con más frecuencia los objetos de vida corta. Esto reduce fugas de memoria por olvidos, pero introduce costes: pausas o trabajo extra del recolector y menor control directo sobre el momento exacto de liberación.
### En resumen, dónde “vive” un objeto depende del lenguaje y del mecanismo de creación: algunos permiten elegir (C++), otros abstraen el detalle y lo gestionan automáticamente (Java, Python). La GC se entiende como la contrapartida a la gestión manual: mejora seguridad y productividad al evitar muchos errores comunes, a cambio de aceptar que la liberación no es inmediata ni totalmente predecible y que el rendimiento puede verse afectado por el trabajo del recolector en determinados momentos.


## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

### Un método es una función asociada a una clase (y, por extensión, a sus objetos) que define una operación sobre el estado del objeto. A diferencia de una función “suelta” en C, un método se invoca “desde” un objeto (o desde la clase) y suele poder acceder directamente a los atributos de ese objeto. Conceptualmente puede verse como “una función que recibe implícitamente el objeto sobre el que actúa”: en C++ existe el puntero implícito this, y en Java existe la referencia implícita this, que representa el objeto actual.
### Además, suele distinguirse entre métodos de instancia y métodos estáticos. Un método de instancia necesita un objeto concreto para ejecutarse y puede leer/modificar su estado; un método estático pertenece a la clase como tal y no depende de un objeto específico (por tanto, no puede acceder a atributos de instancia sin una referencia explícita). Esta distinción ayuda a decidir si una operación forma parte del comportamiento de “cada objeto” o si es más bien una utilidad o regla común de la clase.
### La sobrecarga de métodos consiste en definir varios métodos con el mismo nombre dentro del mismo ámbito (normalmente la misma clase), pero con distinta lista de parámetros (número, tipos o, en algunos lenguajes, orden). El compilador elige cuál ejecutar en función de los argumentos usados en la llamada; por eso se considera un mecanismo de “polimorfismo en tiempo de compilación”. Como regla importante, no basta con cambiar solo el tipo de retorno para que sea una sobrecarga válida: debe diferir la firma por los parámetros.


## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

### class Punto {
###    // Visibilidad por defecto (package-private)
###    double x;
###    double y;
###
###    double calculaDistanciaAOrigen() {
###        return Math.sqrt(x * x + y * y);
###    }
### }
### public class Main {
###     public static void main(String[] args) {
###        Punto p = new Punto();
###        p.x = 3;
###        p.y = 4;
###
###        double d = p.calculaDistanciaAOrigen();
###        System.out.println("Distancia al origen: " + d);
###     }
### }


## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

### El punto de entrada típico de un programa en Java es el método `main` con la firma public `static void main(String[] args)` (también se admite String... args). Cuando se ejecuta una clase, la JVM busca un método `main` exactamente con esa forma para iniciar la ejecución; si no existe, no hay “arranque” automático del programa. En proyectos con varias clases, pueden existir varios `main`, y se elige cuál ejecutar indicando la clase concreta.
### La palabra clave `static` indica que un miembro (atributo, método o bloque de inicialización) pertenece a la clase y no a cada objeto individual. Esto implica que un método `static` puede llamarse sin crear una instancia (Clase.metodo()), y que un atributo `static` es único y compartido por todas las instancias (útil para contadores globales, configuraciones comunes, utilidades matemáticas, etc.). En `main` se usa `static` porque al inicio todavía no existe ningún objeto; se necesita una función invocable “desde la clase” para arrancar.
### `static` no se emplea solo en `main`: se usa con frecuencia en métodos auxiliares (por ejemplo, Math.sqrt), en constantes comunes, en métodos fábrica, en bloques `static { ... }` de inicialización y en clases anidadas `static`. La idea general es reservar `static` para lo que tenga sentido “a nivel de clase” (compartido o independiente de instancias) y evitarlo cuando se pretende que el comportamiento dependa del estado de cada objeto.
### La combinación `static final` se usa sobre todo para declarar constantes de clase: valores compartidos que no deben cambiar (`final` impide reasignar la variable, y `static` hace que exista una sola copia). Si además el valor es un literal simple (primitivo o String), puede convertirse en una constante de tiempo de compilación. También se usa `static final` para referencias únicas (por ejemplo, una instancia singleton), recordando que `final` fija la referencia, no “congela” necesariamente el objeto apuntado si este es mutable.

### Ejemplo:
### public class Ejemplo {
###    static final double PI = 3.141592653589793; // constante compartida
###
###    public static void main(String[] args) {
###        System.out.println(PI);
###    }
### }

## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

### El programa se compila desde línea de comandos usando `javac`, que traduce el fichero fuente .java a bytecode y genera uno o varios ficheros .class. Después se ejecuta usando `java`, que no “ejecuta el .java”, sino que carga la clase (por su nombre) desde el classpath y busca el método public static void main(String[] args). En un caso simple, situándose en la carpeta donde está Main.java, se compila y ejecuta con dos órdenes consecutivas.

### 1) Compilar (genera Main.class en el mismo directorio)
### javac Main.java

### 2) Ejecutar (se indica el nombre de la clase, sin .class)
### java Main

### Cuando hay paquetes (package algo;), el nombre a ejecutar debe ser el nombre cualificado y la estructura de carpetas debe coincidir con el paquete. Es habitual compilar enviando los .class a una carpeta de salida con -d, y luego ejecutar indicando el classpath con -cp (o -classpath). Así se separan fuentes y compilados de forma limpia, algo parecido a generar “artefactos” en una carpeta bin/ o out/.

### Estructura ejemplo:
### src/miapp/Main.java  (y dentro: package miapp;)

### Compilar todo a la carpeta out/
### javac -d out src/miapp/Main.java

### Ejecutar indicando classpath (raíz donde empieza el paquete)
### java -cp out miapp.Main

### Java se considera “compilado y ejecutado sobre una máquina virtual”: el código fuente se compila a bytecode (no a código máquina nativo) y ese bytecode lo ejecuta la JVM (Java Virtual Machine). La JVM es un entorno de ejecución que carga clases, verifica bytecode por seguridad, gestiona memoria (incluida recolección de basura), y ejecuta las instrucciones. En implementaciones modernas, la JVM suele usar JIT (Just-In-Time) para compilar en caliente partes del bytecode a código nativo y acelerar la ejecución, combinando portabilidad con buen rendimiento.
### El bytecode es un conjunto de instrucciones intermedias, independientes de la arquitectura (no son x86/ARM directamente), pensado para ser interpretado/optimizado por la JVM. Los ficheros .class son el “contenedor” binario donde se guarda ese bytecode junto con metadatos (nombre de clase, constantes, firmas de métodos, etc.). Por eso, el mismo .class puede ejecutarse en distintas plataformas (Windows, Linux, macOS) siempre que exista una JVM compatible, ya que la portabilidad la aporta la máquina virtual al “traducir” y ejecutar ese bytecode en cada sistema.


## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

### En el código de `Punto`, la palabra clave `new` sirve para crear (instanciar) un objeto de una clase. Al ejecutarse new Punto(), se reserva memoria para el nuevo objeto, se inicializan sus atributos con valores por defecto (por ejemplo, 0.0 para double) y, a continuación, se invoca un constructor para terminar la inicialización. El resultado de `new` es una referencia al objeto creado (no el objeto “copiado” en la variable), por eso luego se usa p.x, p.y o p.calculaDistanciaAOrigen() sobre esa referencia.
### Un constructor es un miembro especial de la clase que se usa para inicializar el objeto en el momento de su creación. En Java se reconoce porque tiene el mismo nombre que la clase y no tiene tipo de retorno (ni siquiera void). Puede haber varios constructores en la misma clase con distintas listas de parámetros (sobrecarga), permitiendo crear objetos con diferentes formas de inicialización. Si no se define ninguno, Java proporciona un constructor por defecto (sin parámetros) que deja los atributos con sus valores por defecto.
### Ejemplo de constructor en una clase `Empleado` con dni, nombre y apellidos (inicialización mediante parámetros):

### class Empleado {
###    String dni;
###    String nombre;
###    String apellidos;

###    // Constructor
###    Empleado(String dni, String nombre, String apellidos) {
###        this.dni = dni;
###        this.nombre = nombre;
###        this.apellidos = apellidos;
###    }

### }
### public class Main {
###    public static void main(String[] args) {
###        Empleado e = new Empleado("12345678A", "Iván", "Martínez Núñez");
###        System.out.println(e.nombre + " " + e.apellidos + " (" + e.dni + ")");
###    }
### }


## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

### La referencia `this` es una referencia implícita al objeto actual, es decir, a la instancia sobre la que se está ejecutando un método de instancia. Permite acceder a los atributos y métodos del propio objeto y resulta especialmente útil cuando hay ambigüedad entre un parámetro/local y un atributo con el mismo nombre, o cuando se quiere dejar explícito que se está trabajando con el estado interno de esa instancia (por ejemplo, this.x frente a x).
### No se llama igual en todos los lenguajes: en Java y C++ se usa `this`, mientras que en Python se emplea normalmente self (por convención, ya que se pasa como primer parámetro explícito), y en otros lenguajes puede variar (por ejemplo, `this` en C#, Me en Visual Basic .NET). La idea, sin embargo, es la misma: disponer de un “puntero/referencia” al receptor del método para operar sobre su estado.
### Ejemplo en la clase Punto: se usa `this` para (1) diferenciar atributos de parámetros en un constructor y (2) dejar claro que el cálculo usa los atributos del propio objeto.
### class Punto {
###    double x;
###    double y;

###    // Constructor con parámetros con el mismo nombre que los atributos
###    Punto(double x, double y) {
###        this.x = x; // this.x es el atributo; x es el parámetro
###        this.y = y;
###    }

###    double calculaDistanciaAOrigen() {
###        return Math.sqrt(this.x * this.x + this.y * this.y);
###    }
### }


## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

### Para calcular la distancia entre dos puntos se aplica la misma idea que para el origen, pero usando las diferencias entre coordenadas: si el punto actual es (this.x, this.y) y el punto recibido es (p.x, p.y), entonces dx = this.x - p.x y dy = this.y - p.y, y la distancia es sqrt(dx*dx + dy*dy). Al recibir un `Punto` como parámetro, el método puede operar con el estado del objeto actual mediante `this` y con el estado del otro punto mediante el parámetro.
### Conviene además contemplar un caso básico: si se pasa null como punto, la llamada no tiene sentido y se puede lanzar una excepción para evitar un error más confuso posteriormente. A continuación se muestra la clase `Punto` con el método nuevo distanciaA(Punto otro):

### class Punto {
###    double x;
###    double y;

###    Punto(double x, double y) {
###        this.x = x;
###        this.y = y;
###    }
###
###    double calculaDistanciaAOrigen() {
###        return Math.sqrt(this.x * this.x + this.y * this.y);
###    }
###
###    double distanciaA(Punto otro) {
###        if (otro == null) {
###            throw new IllegalArgumentException("El punto proporcionado no puede ser null");
###        }
###        double dx = this.x - otro.x;
###        double dy = this.y - otro.y;
###        return Math.sqrt(dx * dx + dy * dy);
###    }
### }

### public class Main {
###    public static void main(String[] args) {
###        Punto a = new Punto(3, 4);
###        Punto b = new Punto(6, 8);

###        System.out.println("Distancia a -> b: " + a.distanciaA(b));
###    }
### }


## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

### En Java siempre se pasa “por valor”, pero es importante distinguir qué valor se está copiando. Cuando se pasa un `Punto` a un método, lo que se copia es la referencia al objeto (una especie de “dirección” gestionada por la JVM), no una copia completa del objeto. Por eso, si dentro del método se modifican atributos del objeto referenciado (por ejemplo p.x o p.y), esos cambios se ven fuera del método, ya que se está actuando sobre el mismo objeto.
### Sin embargo, si dentro del método se hace que el parámetro “apunte” a otro objeto (reasignar la referencia), ese cambio no afecta a la variable original del llamador, porque solo se ha cambiado la copia local de la referencia. Es decir: se puede cambiar el contenido del objeto y eso se propaga; no se puede cambiar qué objeto referencia la variable del llamador desde dentro del método.
### En cambio, con un `int` (tipo primitivo) se copia el valor numérico al llamar al método. Si dentro del método se modifica ese parámetro, el cambio se queda en la variable local del método y no afecta al `int` original fuera. Para que el exterior “note” el cambio con primitivos, suele devolverse el nuevo valor (o usarse un contenedor/objeto), pero en una modificación directa del parámetro no hay efecto en el llamador.

### class Punto {
###    double x, y;
###    Punto(double x, double y) { this.x = x; this.y = y; }
### }

### class Demo {
###    static void cambiaAtributos(Punto p) {
###        p.x = 0;          // Modifica el MISMO objeto: se verá fuera
###        p.y = 0;
###    }

###    static void reasignaParametro(Punto p) {
###        p = new Punto(100, 100); // Solo cambia la COPIA local de la referencia
###        p.x = 200;               // Modifica el nuevo objeto, pero el llamador no lo verá
###     }
### }

### class DemoInt {
###    static void incrementa(int n) {
###        n = n + 1;  // Solo cambia la COPIA local del valor
###    }

###    public static void main(String[] args) {
###        int a = 5;
###        incrementa(a);
###        System.out.println(a); // Sigue siendo 5
###    }
### }


## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

### En Java, el método `toString()` es el mecanismo estándar para obtener una representación en texto de un objeto. Todas las clases lo heredan de java.lang.Object, y por defecto devuelve algo poco informativo (normalmente el nombre de la clase y un identificador). Al sobrescribir (override) `toString()`, se define una salida legible y útil para depuración, trazas o mensajes. Además, `toString()` se invoca implícitamente en muchos contextos, por ejemplo al hacer System.out.println(objeto) o al concatenar con una cadena ("..." + objeto), lo que hace muy práctico personalizarlo.
### La idea existe en muchos lenguajes, aunque cambie el nombre o la forma: en C# existe ToString(), en Python se usa __str__() (y __repr__() para una representación más “técnica”), y en C++ suele usarse la sobrecarga del operador << para std::ostream (o funciones equivalentes). El objetivo es el mismo: disponer de una forma consistente de convertir un objeto a texto para mostrarlo o registrarlo.
### Ejemplo de `toString()` en la clase `Punto` en Java (manteniendo los atributos con visibilidad por defecto):

### class Punto {
###    double x;
###    double y;

###    Punto(double x, double y) {
###        this.x = x;
###        this.y = y;
###    }

###    @Override
###    public String toString() {
###        return "Punto{x=" + this.x + ", y=" + this.y + "}";
###    }

###    double calculaDistanciaAOrigen() {
###        return Math.sqrt(this.x * this.x + this.y * this.y);
###    }
### }

### public class Main {
###    public static void main(String[] args) {
###        Punto p = new Punto(3, 4);
###        System.out.println(p);              // Llama implícitamente a `toString()`
###        System.out.println(p.toString());   // Llamada explícita a `toString()`
###    }
### }


## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?


### En C, un `struct` se parece a una clase en el sentido de que ambos permiten agrupar datos relacionados bajo un mismo “tipo”. De hecho, es habitual modelar “objetos” en C como un `struct` que guarda estado y un conjunto de funciones que operan sobre él, pasando un puntero al `struct` como primer parámetro (imitando lo que en POO sería el receptor del método, es decir, algo equivalente a this). Sin embargo, esa similitud es más de intención que de soporte real del lenguaje.
### Para que un `struct` fuese “como una clase” le faltan mecanismos del propio lenguaje que unan formalmente datos + comportamiento. En una clase, los métodos forman parte del tipo y se invocan como obj.metodo(); en C las funciones quedan externas y el compilador no impone que se usen (cualquiera puede acceder directamente a los campos del `struct` si los ve). También faltan herramientas de encapsulación (control de visibilidad como private/public) que permitan proteger invariantes: con `struct` no hay una forma estándar de impedir el acceso directo a los campos más allá de ocultar la definición en un .c y exponer un tipo opaco en el .h.
### Además, a un `struct` le faltan comodidades y garantías típicas de clases: constructores y destructores automáticos para inicialización/limpieza, herencia para reutilización estructurada, y polimorfismo integrado (por ejemplo, despacho dinámico) para que una misma operación se resuelva según el tipo real del “objeto”. En C se puede simular parte de esto con funciones, punteros a función (tablas tipo “vtable”) y disciplina de diseño, pero no viene incorporado ni comprobado por el compilador como parte del sistema de tipos.
### Por último, las “variables de ese tipo” ya son, en cierto modo, “instancias” en C (una variable `struct` Punto p; es un bloque de memoria con esos campos), pero el lenguaje no les da identidad y comportamiento como entidad cohesiva: son simplemente datos, y la “instanciación” no dispara lógica de construcción ni asocia métodos. En POO, una instancia es un objeto creado a partir de una clase con reglas de inicialización y una interfaz de uso; en C, para aproximarse a eso, habría que añadir convenciones (funciones init/destroy, ocultación de campos, y pasar explícitamente el “receptor” como parámetro) que en una clase están soportadas directamente por el lenguaje.


## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

### En C se puede “emular” una clase como Punto definiendo un struct para agrupar los datos (x e y) y escribiendo funciones que operen sobre ese struct. En lugar de invocar p.calculaDistanciaAOrigen(), se invoca una función del estilo Punto_calculaDistanciaAOrigen(&p). Para acercarse más al concepto de “objeto bien formado”, suele añadirse también una función de inicialización (una especie de “constructor manual”) como Punto_init(&p, x, y).
### Lo que en Java aparece como “magia” de this en realidad se transforma en C en un parámetro explícito: el “receptor del método” se pasa normalmente como puntero al struct. Es decir, this no desaparece: se convierte en el argumento que indica sobre qué instancia se está operando. El compilador de Java lo maneja de forma implícita; en C se escribe a mano (y por eso se ve más claramente). De forma habitual ese parámetro se nombra p, self o incluso this (en C no es palabra reservada), aunque suele preferirse un nombre como p/self para evitar confusión.

### /* punto.h */
### #ifndef PUNTO_H
### #define PUNTO_H

### typedef struct {
###    double x;
###    double y;
### } Punto;

### /* “Constructor” manual */
### void Punto_init(Punto *p, double x, double y);

### /* “Método” emulado: recibe el “this” explícito */
### double Punto_calculaDistanciaAOrigen(const Punto *p);

### #endif

### /* punto.c */
### #include "punto.h"
### #include <math.h>

### void Punto_init(Punto *p, double x, double y) {
###    p->x = x;
###    p->y = y;
### }

### double Punto_calculaDistanciaAOrigen(const Punto *p) {
###    return sqrt(p->x * p->x + p->y * p->y);
### }

### /* main.c */
### #include <stdio.h>
### #include "punto.h"

### int main(void) {
###    Punto p;
###    Punto_init(&p, 3.0, 4.0);

###    printf("Distancia al origen: %f\n", Punto_calculaDistanciaAOrigen(&p));
###    return 0;
### }

### En resumen: una “clase” en Java encapsula datos y métodos en una sola unidad; en C se separan en struct + funciones, y this pasa de ser implícito a ser el puntero (o dirección) que se entrega como argumento. Esa explicitud permite ver claramente qué objeto se está modificando o consultando, pero exige disciplina: si se accede directamente a p.x y p.y desde cualquier parte, no hay encapsulación real salvo que se oculten detalles (por ejemplo, usando un tipo opaco y solo funciones públicas).