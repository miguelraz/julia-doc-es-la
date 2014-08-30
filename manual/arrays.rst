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

NOTA: En el ejemplo anterior, ``x`` is declared as constant because type
inference in Julia does not work as well on non-constant global
variables.

The resulting array type is inferred from the expression; in order to control
the type explicitly, the type can be prepended to the comprehension. For example,
in the above example we could have avoided declaring ``x`` as constant, and ensured
that the result is of type ``Float64`` by writing::

    Float64[ 0.25*x[i-1] + 0.5*x[i] + 0.25*x[i+1] for i=2:length(x)-1 ]

Using curly brackets instead of square brackets is a shortand notation for an
array of type ``Any``::

    julia> { i/2 for i = 1:3 }
    3-element Any Array:
     0.5
     1.0
     1.5

.. _man-array-indexing:

Indexando
--------

The general syntax for indexing into an n-dimensional array A is::

    X = A[I_1, I_2, ..., I_n]

Donde cada I\_k puede ser:

1. Un valor escalar
2. A ``Range`` of the form ``:``, ``a:b``, or ``a:b:c``
3. An arbitrary integer vector, including the empty vector ``[]``
4. A boolean vector

The result X generally has dimensions
``(length(I_1), length(I_2), ..., length(I_n))``, with location
``(i_1, i_2, ..., i_n)`` of X containing the value
``A[I_1[i_1], I_2[i_2], ..., I_n[i_n]]``. Trailing dimensions indexed with
scalars are dropped. For example, the dimensions of ``A[I, 1]`` will be
``(length(I),)``. The size of a dimension indexed by a boolean vector
will be the number of true values in the vector (they behave as if they were
transformed with ``find``).

Indexing syntax is equivalent to a call to ``getindex``::

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

The general syntax for assigning values in an n-dimensional array A is::

    A[I_1, I_2, ..., I_n] = X

where each I\_k may be:

1. A scalar value
2. A ``Range`` of the form ``:``, ``a:b``, or ``a:b:c``
3. An arbitrary integer vector, including the empty vector ``[]``
4. A boolean vector

The size of X should be ``(length(I_1), length(I_2), ..., length(I_n))``, and
the value in location ``(i_1, i_2, ..., i_n)`` of A is overwritten with
the value ``X[I_1[i_1], I_2[i_2], ..., I_n[i_n]]``.

Index assignment syntax is equivalent to a call to ``setindex!``::

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

Arrays can be concatenated along any dimension using the following
syntax:

1. ``cat(dim, A...)`` — concatenate input n-d arrays along the dimension
   ``dim``
2. ``vcat(A...)`` — Shorthand for ``cat(1, A...)``
3. ``hcat(A...)`` — Shorthand for ``cat(2, A...)``
4. ``hvcat(A...)``

Concatenation operators may also be used for concatenating arrays:

1. ``[A B C ...]`` — calls ``hcat``
2. ``[A, B, C, ...]`` — calls ``vcat``
3. ``[A B; C D; ...]`` — calls ``hvcat``

Operadores y Funciones vectorizadas
-----------------------------------

The following operators are supported for arrays. In case of binary
operators, the dot version of the operator should be used when both
inputs are non-scalar, and any version of the operator may be used if
one of the inputs is a scalar.

1.  Unary Arithmetic — ``-``
2.  Binary Arithmetic — ``+``, ``-``, ``*``, ``.*``, ``/``, ``./``,
    ``\``, ``.\``, ``^``, ``.^``, ``div``, ``mod``
3.  Comparison — ``==``, ``!=``, ``<``, ``<=``, ``>``, ``>=``
4.  Unary Boolean or Bitwise — ``~``
5.  Binary Boolean or Bitwise — ``&``, ``|``, ``$``
6.  Trigonometrical functions — ``sin``, ``cos``, ``tan``, ``sinh``,
    ``cosh``, ``tanh``, ``asin``, ``acos``, ``atan``, ``atan2``,
    ``sec``, ``csc``, ``cot``, ``asec``, ``acsc``, ``acot``, ``sech``,
    ``csch``, ``coth``, ``asech``, ``acsch``, ``acoth``, ``sinc``,
    ``cosc``, ``hypot``
7.  Logarithmic functions — ``log``, ``log2``, ``log10``, ``log1p``
8.  Exponential functions — ``exp``, ``expm1``, ``exp2``, ``ldexp``
9.  Rounding functions — ``ceil``, ``floor``, ``trunc``, ``round``,
    ``ipart``, ``fpart``
10. Other mathematical functions — ``min``, ``max,`` ``abs``, ``pow``,
    ``sqrt``, ``cbrt``, ``erf``, ``erfc``, ``gamma``, ``lgamma``,
    ``real``, ``conj``, ``clamp``

Difundir ampliamente
---------------------

It is sometimes useful to perform element-by-element binary operations
on arrays of different sizes, such as adding a vector to each column
of a matrix.  An inefficient way to do this would be to replicate the
vector to the size of the matrix::

    julia> a = rand(2,1); A = rand(2,3);

    julia> repmat(a,1,3)+A
    2x3 Float64 Array:
     0.848333  1.66714  1.3262 
     1.26743   1.77988  1.13859

This is wasteful when dimensions get large, so Julia offers the
MATLAB-inspired ``bsxfun``, which expands singleton dimensions in
array arguments to match the corresponding dimension in the other
array without using extra memory, and applies the given binary
function::

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

Implementation
--------------

The base array type in Julia is the abstract type
``AbstractArray{T,n}``. It is parametrized by the number of dimensions
``n`` and the element type ``T``. ``AbstractVector`` and
``AbstractMatrix`` are aliases for the 1-d and 2-d cases. Operations on
``AbstractArray`` objects are defined using higher level operators and
functions, in a way that is independent of the underlying storage class.
These operations are guaranteed to work correctly as a fallback for any
specific array implementation.

The ``Array{T,n}`` type is a specific instance of ``AbstractArray``
where elements are stored in column-major order. ``Vector`` and
``Matrix`` are aliases for the 1-d and 2-d cases. Specific operations
such as scalar indexing, assignment, and a few other basic
storage-specific operations are all that have to be implemented for
``Array``, so that the rest of the array library can be implemented in a
generic manner for ``AbstractArray``.

``SubArray`` is a specialization of ``AbstractArray`` that performs
indexing by reference rather than by copying. A ``SubArray`` is created
with the ``sub`` function, which is called the same way as ``getindex`` (with
an array and a series of index arguments). The result of ``sub`` looks
the same as the result of ``getindex``, except the data is left in place.
``sub`` stores the input index vectors in a ``SubArray`` object, which
can later be used to index the original array indirectly.

``StridedVector`` and ``StridedMatrix`` are convenient aliases defined
to make it possible for Julia to call a wider range of BLAS and LAPACK
functions by passing them either ``Array`` or ``SubArray`` objects, and
thus saving inefficiencies from indexing and memory allocation.

The following example computes the QR decomposition of a small section
of a larger array, without creating any temporaries, and by calling the
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

`Matrices disperarsas <http://en.wikipedia.org/wiki/Sparse_matrix>`_ son
matrices que contienen suficientes ceros que almacenándolos en una estructura de datos 
especial supone un ahorro de espacio y tiempo de ejecución. Matrices
dispersas puede utilizarse cuando las operaciones en la representación disperar de una 
matriz conducen a considerables aumentos en el tiempo o el espacio, en comparación con
la realización de las mismas operaciones en una matriz densa..

Compressed Sparse Column (CSC) Storage
--------------------------------------

In julia, sparse matrices are stored in the `Compressed Sparse Column
(CSC) format
<http://en.wikipedia.org/wiki/Sparse_matrix#Compressed_sparse_column_.28CSC_or_CCS.29>`_. Julia
sparse matrices have the type ``SparseMatrixCSC{Tv,Ti}``, where ``Tv``
is the type of the nonzero values, and ``Ti`` is the integer type for
storing column pointers and row indices. 
::

    type SparseMatrixCSC{Tv,Ti<:Integer} <: AbstractSparseMatrix{Tv,Ti}
        m::Int                  # Number of rows
        n::Int                  # Number of columns
        colptr::Vector{Ti}      # Column i is in colptr[i]:(colptr[i+1]-1)
        rowval::Vector{Ti}      # Row values of nonzeros
        nzval::Vector{Tv}       # Nonzero values
    end

The compressed sparse column storage makes it easy and quick to access
the elements in the column of a sparse matrix, whereas accessing the
sparse matrix by rows is considerably slower. Operations such as
insertion of nonzero values one at a time in the CSC structure tend to
be slow. This is because all elements of the sparse matrix that are
beyond the point of insertion have to be moved one place over.

All operations on sparse matrices are carefully implemented to exploit
the CSC data structure for performance, and to avoid expensive operations.

Constructores de matrices dispersas
----------------------------------

The simplest way to create sparse matrices are using functions
equivalent to the ``zeros`` and ``eye`` functions that Julia provides
for working with dense matrices. To produce sparse matrices instead,
you can use the same names with an ``sp`` prefix:

::

    julia> spzeros(3,5)
    3x5 matriz dispersa con 0 nonzeros:

    julia> speye(3,5)
    3x5 matriz dispersa con 3 nonzeros:
        [1, 1]  =  1.0
        [2, 2]  =  1.0
        [3, 3]  =  1.0

The ``sparse`` function is often a handy way to construct sparse
matrices. It takes as its input a vector ``I`` of row indices, a
vector ``J`` of column indices, and a vector ``V`` of nonzero
values. ``sparse(I,J,V)`` constructs a sparse matrix such that
``S[I[k], J[k]] = V[k]``.

::

    julia> I = [1, 4, 3, 5]; J = [4, 7, 18, 9]; V = [1, 2, -5, 3];

    julia> sparse(I,J,V)
    5x18 sparse matrix with 4 nonzeros:
         [1 ,  4]  =  1
         [4 ,  7]  =  2
         [5 ,  9]  =  3
         [3 , 18]  =  -5

The inverse of the ``sparse`` function is ``findn``, which
retrieves the inputs used to create the sparse matrix.

::

    julia> findn(S)
    ([1, 4, 5, 3],[4, 7, 9, 18])

    julia> findn_nzs(S)
    ([1, 4, 5, 3],[4, 7, 9, 18],[1, 2, 3, -5])

Another way to create sparse matrices is to convert a dense matrix
into a sparse matrix using the ``sparse`` function:

::

    julia> sparse(eye(5))
    5x5 sparse matrix with 5 nonzeros:
        [1, 1]  =  1.0
        [2, 2]  =  1.0
        [3, 3]  =  1.0
        [4, 4]  =  1.0
        [5, 5]  =  1.0

You can go in the other direction using the ``dense`` or the ``full``
function. The ``issparse`` function can be used to query if a matrix
is sparse.

::

    julia> issparse(speye(5))
    true

Operaciones con una matriz dispersa
-----------------------------------

Las operaciones aritméticas sobre matrices dispersas también funcionan como lo hacen en 
matrices densas. Indexing of, assignment into, and concatenation of sparse
matrices work in the same way as dense matrices. Indexing operations,
especially assignment, are expensive, when carried out one element at
a time. In many cases it may be better to convert the sparse matrix
into ``(I,J,V)`` format using ``find_nzs``, manipulate the nonzeros or
the structure in the dense vectors ``(I,J,V)``, and then reconstruct
the sparse matrix.
