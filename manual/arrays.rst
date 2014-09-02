.. _man-arrays:

*********
 Arreglos   
*********

Julia, como la mayoría de lenguajes de computación técnica, 
La mayoría de los lenguajes de computación técnica ponen mucha 
atención a su implementación de arreglos a expensas de otros contenedores. 
Julia no trata a los arreglos de ninguna manera especial. La librería de
arreglos se implementa casi por completo en sí mismo en Julia, y deriva 
su rendimiento del compilador, al igual que cualquier otro tipo de código 
escrito en Julia.

Un arreglo es una colección de objetos almacenados en una rejilla 
de dimensión múltiple. En el caso más general, un arreglo puede contener
objetos de tipo ``Cualquiera``. Para la mayoría de los fines de cálculo,
los arreglos deben contener objetos de un tipo más específico, como por
ejemplo `` float64 `` o `` `` Int32.

En general, a diferencia de muchos otros lenguajes de computación técnica, 
Julia no espera que los programas que se escriban en un estilo vectorizado 
para el rendimiento. El compilador de Julia utiliza la inferencia de tipos
y genera código optimizado para la indexación de arreglo escalar, permitiendo 
que los programas sean escritos en un estilo que es conveniente y fácil de leer, 
sin sacrificar el rendimiento, y el uso a veces de menos memoria.

En Julia, todos los argumentos de las funciones son pasados por referencia. Algunos
lenguajes de computación técnica pasan arreglos por valor, y esto es conveniente 
en muchos casos. En Julia, las modificaciones realizadas a los arreglos de entrada 
dentro de una función serán visibles en la función de los padres. La biblioteca 
completa de arreglo de Julia asegura que las entradas no se modifican con funciones 
de la biblioteca. El código de usuario, si es necesario exhibir un comportamiento
similar, debe tener cuidado de crear una copia de entradas que puede modificar.


Funciones básicas
---------------

1. ``ndims(A)`` — el número de dimensiones de A
2. ``size(A,n)`` — el tamaño de A en una particular dimensión
3. ``size(A)`` — una tupla que contiene las dimensiones de A
4. ``eltype(A)`` — el tipo de elementos que contiene en A 
5. ``length(A)`` — o número de elementos en A
6. ``nnz(A)`` — o número de valores diferentes de cero en A
7. ``stride(A,k)`` — the size of the stride along dimension k
8. ``strides(A)`` — una tupla de las distancias de índices lineales entre elementos adyacentes en cada dimensión

Construcción e Inicialización 
-------------------------------

Se facilitan muchas funciones para construir e inicializar arrays. En
la siguiente lista de funciones, llamadas con un argumento ``dims...``
pueden tomar o bien una única tupla de dimensión dims o una serie de 
tamaños de dimensión pasada como un número variable de argumentos.

1.  ``Array(type, dims...)`` — array denso no inicializado
2.  ``cell(dims...)`` — an uninitialized cell array (heterogeneous
    array)
3.  ``zeros(type, dims...)`` — array de tipo especificado inicializado a cero
4.  ``ones(type, dims...)`` — array de tipo especificado inicializado a unos
5.  ``trues(dims...)`` — array ``Bool`` con valores a ``true``
6.  ``falses(dims...)`` — array ``Bool`` con valores a ``false``
7.  ``reshape(A, dims...)`` — array con los mismos datos que el pasado como argumento,
    pero con diferentes dimensiones
8.  ``copy(A)``  — copia ``A``
9.  ``deepcopy(A)`` — copia ``A``, copiando sus elementos recursivamente
10. ``similar(A, element_type, dims...)`` — array sin inicializar del mismo tipo 
    que el pasado (denso, disperso, etc.), pero con el tipo de elementos y dimensiones
    especificadas. El segundo y tercer argumento son opcionales, por defecto (si se omiten)
    asigna el tipo de elemento y dimensiones de ``A``.
11. ``reinterpret(type, A)`` — array con los mismos datos binarios que array pasado como argumento, 
    pero con el tipo de elemento especificado.
12. ``rand(dims)`` — array de aleatorios ``Float64`` uniformemente distribuidos en el rango [0,1)
13. ``randf(dims)`` — array de aleatorios ``Float32`` uniformemente distribuidos en el rango [0,1)
14. ``randn(dims)`` — array de aleatorios ``Float64`` distribuidos mediante una normal con media 0 y 
    desviación estándar de 1
15. ``eye(n)`` — matriz identidad de tamaño nxn
16. ``eye(m, n)`` — matriz identidad de tamaño mxn
17. ``linspace(start, stop, n)`` — vector de ``n`` elementos linealmente espaciados desde ``start`` a ``stop``.
18. ``fill!(A, x)`` — llena el array ``A`` con el valor ``x``

La última función, ``fill!``, es diferente en tanto que modifica un 
array existente en lugar de construir uno nuevo. Como convención, 
las funciones con esta propiedad tienen identificadores con un signo 
de exclamación como sufijo. Estas funciones a veces son denominadas 
funciones "modificadoras" o "in-place".


Comprensiones
-------------

Comprensiones proporcionan una manera general y de gran alcance para la construcción de matrices.
La sintaxis de comprensión es similar a establecer la notación de la construcción en 
matemáticas::

    A = [ F(x,y,...) for x=rx, y=ry, ... ]

El significado de esta forma es que  ``F(x,y,...)`` es evaluado com las 
variables ``x``, ``y``, etc. tomando en cada valor en su lista de valores.
Los valores pueden ser especificados como cualquier objeto iterable, pero será
comúnmente rangos como ``1:n`` o ``2:(n-1)``, o explícitamente arreglos de
valores como  ``[1.2, 3.4, 5.7]``. EL resultado es u arreglo N-d denso array con
dimensiones que son la concatenación de las dimensiones de los rangos de variables
``rx``, ``ry``, etc. y cada  ``F(x,y,...)`` evaluación devuelve un escalar. 

El ejemplo siguiente se calcula una media ponderada del elemento
actual y su vecino de la izquierda y derecha a lo largo de una cuadrícula 1-d.

::

    julia> const x = rand(8)
    8-element Float64 Array:
     0.276455
     0.614847
     0.0601373
     0.896024
     0.646236
     0.143959
     0.0462343
     0.730987

    julia> [ 0.25*x[i-1] + 0.5*x[i] + 0.25*x[i+1] for i=2:length(x)-1 ]
    6-element Float64 Array:
     0.391572
     0.407786
     0.624605
     0.583114
     0.245097
     0.241854

NOTA: En el ejemplo anterior, ``x`` está declarada como constante
porque la inferencia de tipos en Julia no funciona tan bien en las
variables globales no constantes.

El tipo de matriz resultante se deduce de la expresión; para controlar 
el tipo explícitamente, tel tipo se puede anteponer a la comprensión. Por ejemplo,
en el ejemplo anterior podríamos haber evitado declarar ``x`` como constante, y aseguró 
que el resultado es de tipo  ``Float64`` por escrito::

    Float64[ 0.25*x[i-1] + 0.5*x[i] + 0.25*x[i+1] for i=2:length(x)-1 ]

Usando llaves en lugar de corchetes es una notación abreviada para una matriz 
de tipo ``Any``::

    julia> { i/2 for i = 1:3 }
    3-element Any Array:
     0.5
     1.0
     1.5

.. _man-array-indexing:

Indexando
--------

La sintaxis general para 
La sintaxis general para la indexación en un arreglo n-dimensional  A es ::

    X = A[I_1, I_2, ..., I_n]

Donde cada I\_k puede ser:

1. Un valor escalar
2. Un ``Range`` de la forma ``:``, ``a:b``, o ``a:b:c``
3. Un vector entero arbitrario, incluyendo el vector vacío `` [] ``
4. Un vector booleano

El resultado X generalmente tiene dimensiones
``(length(I_1), length(I_2), ..., length(I_n))``, con locación
``(i_1, i_2, ..., i_n)`` de X que contiene el valor
``A[I_1[i_1], I_2[i_2], ..., I_n[i_n]]``. Trailing dimensions indexed with
scalars are dropped. Por ejemplo, las dimensiones de ``A[I, 1]`` serán
``(length(I),)``. El tamaño de una dimensión indexada por un vector 
booleano será el número de valores verdaderos en el vector (que se 
comportan como si se transformaron con `find```).

Sintaxis  de indexación es equivalente a una llamada a `` getindex`` ::

    X = getindex(A, I_1, I_2, ..., I_n)

Ejemplo::

    julia> x = reshape(1:16, 4, 4)
    4x4 Int64 Array
    1 5 9 13
    2 6 10 14
    3 7 11 15
    4 8 12 16

    julia> x[2:3, 2:end-1]
    2x2 Int64 Array
    6 10
    7 11

Asignación
----------

La sintaxis general para la asignación de valores en una matriz n-dimensional A es ::

    A[I_1, I_2, ..., I_n] = X

donde cada  I\_k puede ser:

1. Un valor escalar
2. Un ``Range`` de la forma ``:``, ``a:b``, o ``a:b:c``
3. Un vector entero arbitrario, incluyendo el vector vacío ``[]``
4. Un vector booleano

El tamaño de X debería ser ``(length(I_1), length(I_2), ..., length(I_n))``, y
el valor de locación ``(i_1, i_2, ..., i_n)`` de A es sobreescrito con el valor
``X[I_1[i_1], I_2[i_2], ..., I_n[i_n]]``.

La sintaxis de asignación de índices es equivalente a una llamada a `` setindex! `` ::

      A = setindex!(A, X, I_1, I_2, ..., I_n)

Ejemplo::

    julia> x = reshape(1:9, 3, 3)
    3x3 Int64 Array
    1 4 7
    2 5 8
    3 6 9

    julia> x[1:2, 2:3] = -1
    3x3 Int64 Array
    1 -1 -1
    2 -1 -1
    3 6 9

La concatenación
----------------

Las matrices pueden ser concatenados a lo largo de cualquier dimensión 
con la siguiente sintaxis:

1. ``cat(dim, A...)`` — concatenar la entrada de n-d arreglos largo de la dimensión
   ``dim``
2. ``vcat(A...)`` — Abreviatura de ``cat(1, A...)``
3. ``hcat(A...)`` — Abreviatura de ``cat(2, A...)``
4. ``hvcat(A...)``

Operadores de concatenación también se pueden utilizar para concatenar arreglos:

1. ``[A B C ...]`` — calls ``hcat``
2. ``[A, B, C, ...]`` — calls ``vcat``
3. ``[A B; C D; ...]`` — calls ``hvcat``

Operadores y Funciones vectorizadas
-----------------------------------

Las siguientes operaciones son soportadas por los arreglos. En el caso de operaciones 
binarias, the dot version of the operator should be used when both
inputs are non-scalar, y cualquier versión del operador puede ser usado 
Si uno de las entradas es un escalar. 


1.  Unary Arithmetic — ``-``
2.  Aritmética binaria — ``+``, ``-``, ``*``, ``.*``, ``/``, ``./``,
    ``\``, ``.\``, ``^``, ``.^``, ``div``, ``mod``
3.  Comparación — ``==``, ``!=``, ``<``, ``<=``, ``>``, ``>=``
4.  Unary Boolean or Bitwise — ``~``
5.  Binary Boolean or Bitwise — ``&``, ``|``, ``$``
6.  Funciones trigonométricas — ``sin``, ``cos``, ``tan``, ``sinh``,
    ``cosh``, ``tanh``, ``asin``, ``acos``, ``atan``, ``atan2``,
    ``sec``, ``csc``, ``cot``, ``asec``, ``acsc``, ``acot``, ``sech``,
    ``csch``, ``coth``, ``asech``, ``acsch``, ``acoth``, ``sinc``,
    ``cosc``, ``hypot``
7.  Funciones logarítmicas — ``log``, ``log2``, ``log10``, ``log1p``
8.  Funciones exponenciales — ``exp``, ``expm1``, ``exp2``, ``ldexp``
9.  Funciones de redondeo — ``ceil``, ``floor``, ``trunc``, ``round``,
    ``ipart``, ``fpart``
10. Otras funciones matemáticas — ``min``, ``max,`` ``abs``, ``pow``,
    ``sqrt``, ``cbrt``, ``erf``, ``erfc``, ``gamma``, ``lgamma``,
    ``real``, ``conj``, ``clamp``

Difundir ampliamente
---------------------

A veces es útil para realizar operaciones binarias elemento-por-elemento
en arreglos de diferentes tamaños, como la adición de un vector a cada
columna de una matriz.  Una forma ineficiente de hacer esto sería de
replicar el vector con el tamaño de la matriz ::

    julia> a = rand(2,1); A = rand(2,3);

    julia> repmat(a,1,3)+A
    2x3 Float64 Array:
     0.848333  1.66714  1.3262 
     1.26743   1.77988  1.13859

This is wasteful when dimensions get large, así que Julia ofrece 
``bsxfun`` inspirado en MATLAB, que amplía las dimensiones simples 
en argumentos de matrices para que coincida con la dimensión
correspondiente en la otra matriz sin utilizar más memoria, 
y se aplica la función binaria dada::

    julia> bsxfun(+, a, A)
    2x3 Float64 Array:
     0.848333  1.66714  1.3262 
     1.26743   1.77988  1.13859

    julia> b = rand(1,2)
    1x2 Float64 Array:
     0.629799  0.754948

    julia> bsxfun(+, a, b)
    2x2 Float64 Array:
     1.31849  1.44364
     1.56107  1.68622

Implementación
--------------

El tipo de matriz base de de Julia es el tipo abstracto
``AbstractArray{T,n}``. Se parametrizada por el número de dimensiones
``n`` y el tipo de elemento ``T``. ``AbstractVector`` y
``AbstractMatrix`` son alias para los casos 1-D y 2-D. Operaciones en
objetos ``AbstractArray`` son definidos usando definidas con operadores
y funciones de nivel superior, de una manera que es independiente 
de la clase de almacenamiento subyacente.
Estas operaciones están garantizados para funcionar correctamente como
un mensaje para cualquier implementación específica de un arreglo.

El tipo ``Array{T,n}`` es una instancia específica de ``AbstractArray``
donde los elementos se almacenan en orden por columnas. ``Vector`` y
``Matrix`` son aliases para los casos 1-d y 2-d. Las operaciones 
específicas tales como la indexación escalar, asignaciones, y algunas 
otras operaciones de almacenamiento específica básicos son todo lo que
tiene que ser implementado para `` Array``, de modo que el resto de la
biblioteca matriz puede ser implementado de una manera genérica
para `` AbstractArray``.

``SubArray`` es una  especialización de  ``AbstractArray`` que realiza 
la indexación por referencia en lugar de copiando. ``SubArray`` es creado
con la función ``sub``,que se llama del mismo modo que `` getindex`` (con un 
arreglo y una serie de argumentos índice). Los resultados de  ``sub`` muestran
los mismos resultados de  ``getindex``, excepto los datos se dejan en su lugar.
``sub`` almacena los vectores de índice de entrada en un objeto `` SubArray``, que más
tarde se pueden usar para indexar la matriz original indirectamente.

``StridedVector`` y ``StridedMatrix`` son definidos los alias convenientes 
para hacer posible que Julia llame a un rango más amplio de funciones BLAS y
LAPACK haciéndolas pasar ya sea por objetos `` Array`` o `` SubArray``, y por 
lo tanto el ahorro de las ineficiencias de la indexación y la asignación de memoria.

El siguiente ejemplo calcula la descomposición QR de una pequeña sección de una grande arreglo
, sin crear ningún temporal, and by calling the
appropriate LAPACK function with the right leading dimension size and
stride parameters.

.. code-block:: jlcon

    julia> a = rand(10,10)
    10x10 Float64 Array:
     0.763921  0.884854   0.818783   0.519682   …  0.860332  0.882295   0.420202
     0.190079  0.235315   0.0669517  0.020172      0.902405  0.0024219  0.24984
     0.823817  0.0285394  0.390379   0.202234      0.516727  0.247442   0.308572
     0.566851  0.622764   0.0683611  0.372167      0.280587  0.227102   0.145647
     0.151173  0.179177   0.0510514  0.615746      0.322073  0.245435   0.976068
     0.534307  0.493124   0.796481   0.0314695  …  0.843201  0.53461    0.910584
     0.885078  0.891022   0.691548   0.547         0.727538  0.0218296  0.174351
     0.123628  0.833214   0.0224507  0.806369      0.80163   0.457005   0.226993
     0.362621  0.389317   0.702764   0.385856      0.155392  0.497805   0.430512
     0.504046  0.532631   0.477461   0.225632      0.919701  0.0453513  0.505329
    
    julia> b = sub(a, 2:2:8,2:2:4)  
    4x2 SubArray of 10x10 Float64 Array:
     0.235315  0.020172
     0.622764  0.372167
     0.493124  0.0314695
     0.833214  0.806369
    
    julia> (q,r) = qr(b);
    
    julia> q
    4x2 Float64 Array:
     -0.200268   0.331205
     -0.530012   0.107555
     -0.41968    0.720129
     -0.709119  -0.600124
    
    julia> r
    2x2 Float64 Array:
     -1.175  -0.786311
      0.0    -0.414549

*******************
 Matrices dispersas
*******************

`Matrices dispersas <http://en.wikipedia.org/wiki/Sparse_matrix>`_ son
matrices que contienen suficientes ceros que almacenándolos en una estructura de datos 
especial supone un ahorro de espacio y tiempo de ejecución. Matrices
dispersas puede utilizarse cuando las operaciones en la representación disperar de una 
matriz conducen a considerables aumentos en el tiempo o el espacio, en comparación con
la realización de las mismas operaciones en una matriz densa..

Columna dispersa comprimido (CSC) Almacenamiento
------------------------------------------------

En Julia, matrices dispersas se almacenan en la `Columna dispersa comprimido
(CSC) format
<http://en.wikipedia.org/wiki/Sparse_matrix#Compressed_sparse_column_.28CSC_or_CCS.29>`_. En Julia 
las matrices dispersas tienen el tipo  ``SparseMatrixCSC{Tv,Ti}``, donde ``Tv``
es el tipo de los valores distintos de cero, y `` Ti`` es el tipo entero
para almacenar punteros de columna y índices de fila. 
::

    type SparseMatrixCSC{Tv,Ti<:Integer} <: AbstractSparseMatrix{Tv,Ti}
        m::Int                  # Número de filas
        n::Int                  # Número de columnas
        colptr::Vector{Ti}      # Columna i es en colptr[i]:(colptr[i+1]-1)
        rowval::Vector{Ti}      # Valores de fila distintos de cero
        nzval::Vector{Tv}       # Valores distintos de cero
    end

El almacenamiento columna dispersa comprimido hace que sea fácil y rápida para
acceder a los elementos en la columna de una matriz dispersa, mientras que el acceso
a la matriz dispersa por filas es considerablemente más lento. Operaciones 
tales como la inserción de los valores distintos de cero de uno en uno en 
la estructura CSC tienden a ser lentos. Esto es por que todos los elementos 
de la matriz dispersa que están más allá del punto de inserción que tenga
que mover un solo lugar más..

Todas las operaciones sobre matrices dispersas se implementan cuidadosamente 
para aprovechar la estructura de datos de CSC para el funcionamiento, y evitar costosas operaciones.

Constructores de matrices dispersas
----------------------------------

El camino simple para crear matrices son usando funciones
equivalentes a las funciones  ``zeros``  ``eye`` que Julia dispone
para trabajar con matrices densas. Para producir matrices dispersas en lugar,
puede utilizar las mismos nombres con un prefijo `` sp``:

::

    julia> spzeros(3,5)
    3x5 matriz dispersa con 0 nonzeros:

    julia> speye(3,5)
    3x5 matriz dispersa con 3 nonzeros:
        [1, 1]  =  1.0
        [2, 2]  =  1.0
        [3, 3]  =  1.0

La función ``sparse`` es a menudo una forma práctica para construir
matrices dispersas. Toma como entrada un vector `` I`` de índices fila, un
vector de índices columna ``J``, y un vector  ``V`` de valores nonzero. 
``sparse(I,J,V)`` construye una matriz dispersa tal como
``S[I[k], J[k]] = V[k]``.

::

    julia> I = [1, 4, 3, 5]; J = [4, 7, 18, 9]; V = [1, 2, -5, 3];

    julia> sparse(I,J,V)
    5x18 sparse matrix with 4 nonzeros:
         [1 ,  4]  =  1
         [4 ,  7]  =  2
         [5 ,  9]  =  3
         [3 , 18]  =  -5

La inversa de la fución ``sparse`` es ``findn``, que recupera
las entradas que se utilizan para crear la matriz dispersa.

::

    julia> findn(S)
    ([1, 4, 5, 3],[4, 7, 9, 18])

    julia> findn_nzs(S)
    ([1, 4, 5, 3],[4, 7, 9, 18],[1, 2, 3, -5])

Otra forma de crear matrices dispersas es convertir una matriz densa
en una matriz dispersa usando la función ``sparse``:

::

    julia> sparse(eye(5))
    5x5 sparse matrix with 5 nonzeros:
        [1, 1]  =  1.0
        [2, 2]  =  1.0
        [3, 3]  =  1.0
        [4, 4]  =  1.0
        [5, 5]  =  1.0

Puedes ir en la otra dirección utilizando  ``dense`` o la función ``full``.
La función ``issparse`` puede ser utilizado para consultar si una matriz 
es dispersa.

::

    julia> issparse(speye(5))
    true

Operaciones con una matriz dispersa
-----------------------------------

Las operaciones aritméticas sobre matrices dispersas también funcionan como lo hacen en 
matrices densas. Indexación de, asignación en, y concatenación de matrices dispersas 
funcionan de la misma manera que las matrices densas. Operaciones de indexación,
en especial la asignación, son costosos, cuando se lleva a cabo uno de los elementos
a la vez. En muchos casos, puede ser mejor convertir la matriz dispersa en el 
formato ``(I,J,V)`` usando ``find_nzs``, manipular los nonzeros o la estructura
en los vectores densos ``(I,J,V)``, y luego reconstruir la matriz dispersa.
