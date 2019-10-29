# Arreglos, colecciones y colecciones genéricas

## Arreglos, colecciones y colecciones genéricas

## Introducción

Para muchas aplicaciones, puede que se necesite crear y administrar grupos de objetos relacionados. Existen dos formas de agrupar objetos: mediante la creación de matrices de objetos y con la creación de colecciones de objetos.

Las colecciones proporcionan una manera más flexible de trabajar con grupos de objetos. A diferencia de las matrices, el grupo de objetos con el que trabaja puede aumentar y reducirse de manera dinámica a medida que cambian las necesidades de la aplicación. Para algunas colecciones, puede asignar una clave a cualquier objeto que incluya en la colección para, de este modo, recuperar rápidamente el objeto con la clave.

Una colección es una clase, por lo que debe declarar una instancia de la clase para poder agregar elementos a dicha colección.

Si la colección contiene elementos de un solo tipo de datos, puede usar una de las clases del espacio de nombres [System.Collections.Generic](https://docs.microsoft.com/es-es/dotnet/api/system.collections.generic). Una colección genérica cumple la seguridad de tipos para que ningún otro tipo de datos se pueda agregar a ella. Cuando recupera un elemento de una colección genérica, no tiene que determinar su tipo de datos ni convertirlo.

## La Clase Array en C\#

El estudio de la clase Array en el lenguaje de programación C\#. Esta clase, como ya veremos, es una implementación particular de IList; tanto su versión no-genéria como genérica, dado que el acceso a los elementos se realiza a través de un índice. Aún sí, sabremos que no todos los métodos de estas clases tendrán un codificación específica, sino que se lanzará la excepción NotSupportedException.

La primera parte es una introducción acerca de los fundamentales. La segunda se enfocará en búsqueda y ordenamiento de elementos. La última tratará acerca de copiado, conversión y redimensionamiento de arreglos.

### Generalidades

### Jerarquía de herencia e implementación

La clase Array es una de las clases base que implementa las interfaces estándar de colección; i.e., IList, ICollection y IEnumerable . Se trata de un tipo abstracto que provee métodos estáticos y de instancia para realizar operaciones sobre arreglos: creación, manipulación, búsqueda y ordenamiento.

Se sabe que uno de los principios de .NET Framework es la unificación de tipos de datos, y en este caso la clase Array provee un conjunto de métodos comunes para todos los diferentes tipos de datos adyacentes a los elementos de un arreglo.

Con relación a lo anterior, los arreglos son considerados una estructura de datos o colección esencial en el lenguaje de programación C\# y todos los demás lenguajes de programación compatibles con la CLR. En el artículo [Arreglos en C\#](https://ortizol.blogspot.com/2013/09/arreglos-en-c.html) se expone la sintaxis declarativa y de manipulación de arreglos sobre C\#.

### Sintetización de pseudo-subtipos

Otra característica interna y esencial es la sintetización de pseudo-subtipos de arreglos tanto para el tamaño o dimensión como para el tipo de dato subyacente. Por ejemplo, si se crea un arreglo con el tipo de dato string, las interfaces génericas que implementa Array cambiarán su tipo paramétrico a este mismo: IList&lt;string&gt;.

Adicionalmente la máquina virtual CLR asignará al arreglo recién creado un espacio contiguo en la memoria de trabajo. Aunque esto resulte eficiente en el acceso basado en índices, no se permite el redimensionamiento en el ciclo de ejecución del programa. El método Resize&lt;T&gt; \("Array.Resize\(T\)"\) permite cambiar el tamaño de un arreglo especificado por uno nuevo; sin embargo, cualquier otra referencia al arreglo anterior continuará sin ser modificada. Para solucionar esta carencia, se opta por el uso de colecciones dinámicas como List&lt;T&gt;.

### Tipos por referencia y por valor

El almacenamiento de los tipos por valor se computa a partir del tamaño del tipo dato; es decir, que si un arreglo de 5 elementos de tipo de dato int -4 bytes-, ocupará 20 bytes. Este no es el caso para los arreglos que localizan tipos por referencia: cada elemento del arreglo sólo ocupará el espacio dependiendo de la arquitectura del sistema: 4 bytes en un ambiente de 32 bits u 8 bytes en un ambiente de 64 bits.

para la ilustración gráfica de este modelo de almacenamiento:

![](.gitbook/assets/image%20%284%29.png)

Esta ilustración puede traducirse al siguiente código fuente:

```csharp
StringBuilder[] builders = new StringBuilder[5];  
builders[0] = new StringBuilder("builder1");  
builders[1] = new StringBuilder("builder2");  
builders[2] = new StringBuilder("builder3");  

long[] numbers = new long[3];  
numbers[0] = 12345;  
numbers[1] = 54321;
```

### Comparación y copiado

Todos los arreglos en su defecto son tipos por referencia a razón que Array es una clase. Si se tienen dos variables, digamos arreglo1 y arreglo2, entonces al realizar la siguiente asignación arreglo1 = arreglo2, ambas apuntarán al mismo arreglo en memoria.

En cuanto a la comparación del contenido de los arreglos -i.e., comparación estructural- el operador == y el método Equals producen false como resultado:

```csharp
object[] a1 = {"Balzac", 1799, true};  
object[] a2 = {"Balzac", 1799, true};  

Console.WriteLine(a1 == a2);  
Console.WriteLine(a1.Equals(a2));
```

Para realizar comparación estructural podemos usar un comparador de igualdad personalizado o por medio de la clase static StructuralComparisons \(disponible a partir de la versión 4.0 de .NET Framework\):

```csharp
IStructuralEquatable se1 = a1;  
Console.WriteLine(se1.Equals(a2, StructuralComparisons.StructuralEqualityComparer);
```

Esta sentencia da como resultado true, a razón de que el contenido de ambos arreglos es igual.

Por su parte, la operación de clonación, la cual se lleva a cabo con el método Clone, como en

```text
arregloB = arregloA.Clone()
```

genera una copia shallow: lo que quiere decir que sólo se copia la memoria que representa al arreglo y no los valores de los elementos y sus datos localizados en la memoria. La Figura 2 ilustra mejor este concepto a partir del siguiente código fuente:

```csharp
StringBuilder[] builders2 = builders;  
StringBuilder[] shalowClone = (StringBuilder[]) builders.Clone();
```

![](.gitbook/assets/image%20%282%29.png)

Para crear una copia del contenido y las referencias de un arreglo, se debe efectuar una copia de tipo deep. Para lograrlo es necesario iterar el arreglo y realizar una clonación por cada elemento. Esto también aplica para los demás tipos de colecciones de .NET Framework.

### Creación e Indexación

C\# provee diferentes alternativas para la creación y acceso por medio de índices. Una formá básica comprende el uso de esta sintaxis:

```csharp
int[] arregloEnteros = {2, 3, 5};  
Console.WriteLine(arregloEnteros[0]); // Primer elemento  
Console.WriteLine(arregloEnteros[arregloEnteros.Length - 1]); // Último elemento
```

Otra de las formas disponibles consiste en usar el método static CreateInstance:

![](.gitbook/assets/image%20%285%29.png)

Entonces el arreglo definido anteriormente también se puede escribir así:

```csharp
Array arregloEnteros = Array.CreateInstance(typeof(int), 3);  
arregloEnteros.SetValue(2, 0\);  
arregloEnteros.SetValue(3, 1\);  
arregloEnteros.SetValue(5, 2\);  
Console.WriteLine(arregloEnteros.GetValue(0)); // Primer elemento  
Console.WriteLine(arregloEnteros.GetValue(arregloEnteros.Length - 1)); // Último elemento
```

Nótese que el tipo de dato asociado al arreglo se define a través del operador typeof \([El Método GetType y el Operador typeof en C\#](https://ortizol.blogspot.com.co/2014/04/el-metodo-gettype-y-el-operador-typeof.html)\).

Independiente del mecanismo de inicialización de un arreglo que se use, cada uno de sus elementos se inicializan de manera automática.

### Enumeración

Para recorrer un arreglo es posible utilizar diferentes mecanismos. Partimos de esta definición:

```csharp
int[] arregloEnteros = {2, 3, 5};
```

### Ciclo for

```csharp
for(int i = 0; i &lt; arregloEnteros.Length; ++i){    Console.WriteLine(arregloEnteros[i]);}
```

Aquí el recorrido se realiza a partir de un índice: la variable entera i. El arreglo itera desde el primer elemento -0- hasta que la condición de continuación de ciclo no se cumpla -i == arregloEnteros.Length-

### El ciclo mejorado foreach

```csharp
foreach(int valor in arregloEnteros){    Console.WriteLine(valor)}
```

Este mecanismo es más simple que el anterior, pero no permite la modificación de los datos adyacentes al arreglo.

### El método de extensión ForEach

Con el método de extensión ForEach, se especifica el arreglo a ser recorrido y la acción a aplicar en cada elemento:

```csharp
Array.ForEach(arregloEnteros, Console.WriteLine);
```

### Longitud y Rango

### Longitud

Para obtener la longitud o tamaño de una dimensión de un arreglo se usan los métodos de instancia Array.GetLength y Array.GetLongLength.  
En lo que se refiere a las propiedades Length y LongLength, éstas obtienen la cantidad total de elementos en todas las dimensiones del arreglo.  
Otros métodos interesantes son:

* GetLowerBound: Obtiene el índice del primer elemento de la una dimensión especificada.
* GetUpperBound: Obtiene el índice del último elemento de la una dimensión especificada.

### Rango

El rango de un arreglo se obtiene a través de la propiedad Rank.

### **Conclusiones**

En esta primera parte comprendimos los principios de la clase Array: una clase unificadora para definir arreglos básicos, ya sea de tipos por valor o tipos por referencia. Estudiamos las formas de declarar y acceder los elementos: básada en índices y métodos static y de instancia. Más adelante nos concentramos entender cómo se recorre o enumera los elementos: for, foreach y ForEach. Al final, se reconocieron los métodos para obtener el tamaño y el número de dimensiones de un arreglo.

### Búsqueda

La clase Array ofrece tres clases de métodos de búsqueda para arreglos unidimensionales Método static sobrecargado **BinarySearch** : busca sobre un arreglo ordenado usando el algoritmo de búsqueda binaria. **Búsqueda locativa**: los métodos **IndexOf** y **LastIndex** recuperan el primer y último elemento de un arreglo. **Búsqueda por predicados**: los métodos **Find**, **FindLast**, **FindIndex**, **FindLastIndex**, **FindAll**, **Exists** y **TrueForAll** realizan una búsqueda partiendo de un predicado lógico de tipo Predicate. Cuando no se encuentra un elemento en un arreglo usando cualquiera de los métodos de búsqueda, éstos retornan -1 o el valor por defecto del tipo paramétrico -0 para valores numéricos o null para string, por ejemplo-.

Para los métodos basados en el algoritmo de búsqueda binaria, el cual es de alto desempeño, el requisito indispensable es que los elementos estén ordenados.

Para la búsqueda locativa, se efectúa una enumeración de los elementos y se obtiene el índice del primer u último elemento que coincide con un valor particular.

A través de un delegado \(Que se verá mas adelante en la materia\) o una expresión lambda \(se hablará de ellas más adelante en la materia\) es posible establecer un predicado para la búsqueda de uno o más valores en un arreglo. La forma sintáctica que tiene un predicado es :

```csharp
public delegate bool Predicate<T>(T object)

Un ejemplo de uso básico podría ser:

static void Main()
{
    string[] tresMaestros = { "Balzac", "Dickens", "Dostoievski"};
    string encontrado = Array.Find(tresMaestros, ContieneO);

    Console.WriteLine(encontrado); 
}

static bool ContieneO(string escritor)
{
    return escritor.Contains("o");
}
```

Otra forma simplificada:

```csharp
static void Main()
{
    string[] tresMaestros = { "Balzac", "Dickens", "Dostoievski"};
    string encontrado = Array.Find(tresMaestros, delegate(string escritor){return escritor.Contains("o");});

    Console.WriteLine(encontrado); 
}
```

Aunque con una expresión lambda se obtiene una versión más simple:

```csharp
static void Main()
{
    string[] tresMaestros = { "Balzac", "Dickens", "Dostoievski"};
    string encontrado = Array.Find(tresMaestros, n => n.Contains("o"));

    Console.WriteLine(encontrado); 
}
```

\[Nota: se recomienda leer la documentación para comprender los demás métodos que aceptan un predicado para efecutar una búsqueda.\]

### Ordenamiento

Existen 17 versiones sobrecargadas del método Sort en la Array. Una forma básica de uso sobre un único arreglo podría ser:


```csharp
int[] numeros = {5, 2, 3, 7};
Array.Sort(numeros);    // {2, 3, 6, 7}

```


O usar la versión que acepta dos arreglos:

```csharp
int[] numeros = {5, 2, 3, 7};
string[] tresMaestros = { "Dostoievski", "Dickens", "Balzac"};
Array.Sort(numeros, tresMaestros);    // {2, 3, 6, 7}, { "Balzac", "Dickens", "Dostoievski"
```

Vale agregar que existen otras versiones sobrecargadas que facilitan especificar un delegado de comparación para la personlización la forma en que se debe llegar la comparación:

public delegate int Comparison(T x, T y)

### Conclusiones

Hemos explorado las formas esenciales para buscar y ordernar los elementos en una colección de tipo Array. Estos métodos comprenden operaciones fundamentales para manipular elementos de datos para solución de problemas.

El siguiente artículo se estudiará cómo revertir, copiar, convertir y redimensionar un arreglo de tipo Array.

