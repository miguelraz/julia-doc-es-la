.. _man-getting-started:

***********
 Comenzando  
***********

La instalación de Julia es directa, sea  utilizando arquivos binários pre-compilados, sea
compilando el código-fuente. Baje e instale Julia siguiendo las 
instrucciones (em inglés) en `http://julialang.org/downloads/ <http://julialang.org/downloads/>`_.

La manera más facil de aprender y experimentar con Julia es iniciando una sesión interactiva (também
conecida como *read-eval-print loop* ou *"repl"* [#REPL-en]_)::

    $ julia
                   _
       _       _ _(_)_     |
      (_)     | (_) (_)    |  A fresh approach to technical computing.
       _ _   _| |_  __ _   |
      | | | | | | |/ _` |  |  Version 0 (pre-release)
      | | |_| | | | (_| |  |  Commit 61847c5aa7 (2011-08-20 06:11:31)*
     _/ |\__'_|_|_|\__'_|  |
    |__/                   |

    julia> 1 + 2
    3

    julia> ans
    3


Para cerrar la sesión interactiva, digite ``^D```- la tecla *Ctrl* 
en conjunto la tecla ``d`` - o digite ``quit()``. Cuando utilize 
Julia en modo interativo, ``julia`` muestra un *banner* y espera al 
usuário que digite un comando. Una vez que el usuário digitó el comando,
como `1 + 2`, y presione *enter*, la sesión interativa calcula la 
expresión y muestra el resultado. Si una expresión es insertada en una 
sesión interativa con un punto-y-vírgula al final, su resultado será
calculado, pero no será mostrado. La varible ``ans`` almacena el resultado 
de la última expresión calculada, después de haber sido mostrado o no.

Para calcular expresiones escritas en un archivo ``file.jl``, luego digite
``include("file.jl")``.

Para rodar código en un archivo de manera no-interactiva, ustede puede
pasar el nombre del archivo como el primer argumento en la llamada de Julia::

    $ julia script.jl arg1 arg2...

Como muestra el ejemplo, los argumentos de la línea de comando subsequentes
son tomados como argumentos para el programa ``script.jl``, pasado en la
constante global ``ARGS``. ``ARGS`` es también definida cuando el código
del *script* es dado usando la opción de la linea de comando ``-e`` (vea la 
salida de ayuda de ``julia`` abajo). Por ejemplo, para apenas imprimir
los argumentos dados a un *script*, ustedes puede hacer::

    $ julia -e 'for x in ARGS; println(x); end' foo bar
    foo
    bar

Ou pode colocar esse código em um *script* e rodá-lo::

    $ echo 'for x in ARGS; println(x); end' > script.jl
    $ julia script.jl foo bar
    foo
    bar

Hay varias formas de llamar a Julia e pasar opciones, semejantes
a aquellas disponibles para los programas ``perl`` y ``ruby``::

    julia [options] [program] [args...]
     -v --version             Display version information
     -q --quiet               Quiet startup without banner
     -H --home=<dir>          Load files relative to <dir>
     -T --tab=<size>          Set REPL tab width to <size>

     -e --eval=<expr>         Evaluate <expr>
     -E --print=<expr>        Evaluate and show <expr>
     -P --post-boot=<expr>    Evaluate <expr> right after boot
     -L --load=file           Load <file> right after boot
     -J --sysimage=file       Start up with the given system image file

     -p n                     Run n local processes
     --machinefile file       Run processes on hosts listed in file

     --no-history             Don't load or save history
     -f --no-startup          Don't load ~/.juliarc.jl
     -F                       Load ~/.juliarc.jl, then handle remaining inputs

     -h --help                Print this message


Tutorial
---------

Algunas guias paso a paso están disponibles online:

- `Comenzando con Julia para usuarios de MATLAB <http://www.ime.unicamp.br/~ra092767/tutoriais/julia/>`_
- `Foro Julia Tutorials (en inglés) <http://forio.com/julia/tutorials-list>`_
- `Tutorial para Homer Reid's numerical analysis class (en inglés) <http://homerreid.ath.cx/teaching/18.330/JuliaProgramming.shtml#SimplePrograms>`_

Diferencias notables en relación al MATLAB
------------------------------------------

Usuarios de MATLAB pueden encontrar una sintaxis familiar en Julia, pero 
no es de ninguna manera un clon de MATLAB: hay grandes diferencias
sintácticas y funcionales. 
sintáticas e funcionais. A continuación se presentan algunas
advertencias importantes que puedan confundir a los usuarios Julia
acostumbrados con MATLAB:

-  *Arrays* son indexados con corchetes, ``A[i,j]``.
-  La unidad imaginaria ``sqrt(-1)`` es representada en Julia por
   ``im``.
-  Múltipless valores son devueltos y asignados con paréntesis,
   ``return (a, b)`` e ``(a, b) = f(x)``.
-  Los valores se transmiten y se asignan por referencia. Si una función 
   modifica un *array*, los cambios serán visibles para quien llamó.
-  Julia tiene *arrays* unidimensionales. Vectores-columna son de tamaño 
   ``N``, não ``Nx1``. Por ejemplo, ``rand(N)`` crea un array 
   unidimensional.
-  Concatenar escalares y *arrays* con una sintaxis ``[x,y,z]`` concatena
   la primera dimensión ("verticalmente"). Para la segunda dimensión,
   ("horizontalmente"), use espacios, como en ``[x y z]``. Para 
   construir matrices en bloques (concatenando las dos primeras
   dimensiones), es usada una sintaxis ``[a b; c d]`` para evitar confusión.
-  Dos-puntos ``a:b`` e ``a:b:c`` construyen objetos ``Range``. Para 
   construir un vector completo, use ``linspace``, o "concatene" o
   en el intervalo colocando en corchetes, ``[a:b]``.
-  Funciiones devuelven valores usando a palavra-clave ``return``, en  
   vez de colocar citas a sus nombres en la definición de la función (vea
   :ref:`man-return-keyword` para mas detalles).
-  Un archivo puede almacenar un número cualquiera de funciones, y todas las 
   definiciones van a ser visibles para fuera cuando el archivo fuera cargado.
-  Reducciones como ``sum``, ``prod``, e ``max`` son hechas sobre cada 
   elemento de un *array* cuando son llamadas con un único argumento, como
   en ``sum(A)``.
-  Funciones como ``sort`` que operan de forma estándar  en columnas
   (``sort(A)`` es equivalente a ``sort(A,1)``) no poseen un
   comportamiento especial para *arrays* 1xN; el argumento es retornado
   inalterado,  ya que la operación  hecha fue ``sort(A,1)``. Para ordenar
   una matriz 1xN como um vetor, use ``sort(A,2)``.
-  Paréntesis deben  ser usados para chamar una función  con cero 
   argumentos, como en``tic()`` y ``toc()``.
-  No use ponto-e-vírgula para cerrar declaraciones. Los resultados 
   de declaraciones no son automaticamente  impresos (exceto no prompt 
   interativo), y lineas de código no precisan terminar con
   ponto-y-vírgula. A función  ``println`` puede ser usada para imprimir 
   un valor seguido de uma nueva línea.
-  Se ``A`` e ``B`` são *arrays*, ``A == B`` não retorna um *array* de
   booleanos. Use ``A .== B`` no lugar. O mesmo vale para outros 
   operaores booleanos, ``<``, ``>``, ``!=``, etc.
-  Los elementos de uma colección pueden ser passados como argumentos para
   uma função usando ``...``, como em ``xs=[1,2]; f(xs...)``.
-  La función  ``svd`` de Julia retorna los valores singulares como un
   vector, y no como uma matriz diagonal.

Diferencias notables en relación al R
--------------------------------------
Uno de los objetivos de Julia es proporcionar un lenguaje eficaz para 
el análisis de datos y programación estadística. Para los usuarios de Julia 
procedentes R, estas son algunas diferencias importantes:

- Julia usa ``=`` para atrbuit. Julia proporciona ningún otro
  operador alternativo, como ``<-`` o ``<-``.
- Julia construye vectores usando corchetes. O ``[1, 2, 3]`` de Julia es
  equivalente a ``c(1, 2, 3)`` del R.
- Las operaciones con matrices en Julia son más afines a la notación
  matemática tradicional que los del R. Si ``A`` e ``B`` son matrices,
  entonces ``A * B`` define una multiplicación de matrices en Julia 
  equivalente a ``A %*% B`` de R. En R, esta notación haría un producto
  de Hadamard (elemento a elemento). Para la multiplicación
  elemento a elemento em Julia, usted debe escribir ``A .* B``.
- Julia transpone matrices utilizando el operador ``'``. O ``A'`` en Julia es
  entonces equivalente a ``t(A)`` del R.
- Julia no requiere paréntesis al escribir condiciones ``if`` o loops 
  ``for``: use ``for i in [1, 2, 3]`` en lugar de ``for (i in c(1, 2, 3))``
  y ``if i == 1`` en lugar de ``if (i == 1)``.
- Julia no trata los números ``0`` e ``1`` como booleanos. No
  puede escribir ``if (1)`` en Julia, porque condiciones ``if` solo aceptan
  booleanos. En lugar, escriba ``if true``.
- Julia no proporciona funciones ``nrow`` y ``ncol``. Usar ``size(M, 1)`` en 
  lugar de ``nrow(M)`` e ``size(M, 2)`` en lugar de ``ncol(M)``.
- La SVD de Julia no se reduce de forma predeterminada, a diferencia deR. Para
  obtener resultados similares a los de R, debe llamar a ``svd(X, true)``
  en una matriz ``X``.
- Julia es un lenguaje muy prudente en distinguir escalar, 
  vectores y matrices. En R, ``1`` y ``c(1)`` son iguales. En Julia, 
  no se pueden utilizar en el lugar de otro. Una consecuencia
  potencialmente confuso es que ``x' * y`` para vectores ``x`` y ``y``
  es un vector de un elemento, y no un escalar. Para obtener un escalar,
  puede usar ``dot(x, y)``.
- Las funciones ``diag()`` e ``diagm()`` de Julia no son parecidas con 
  las del R.
- Julia no puede asignar los resultados de las llamadas de función en el lado
  izquierdo de una operación: no puede escribir ``diag(M) = ones(n)``
- Julia desincentiva al popular *namespace* principal con funciones. La 
  mayor parte de las funcionalidades estadísticas para Julia es encontrada
  en `paquetes <http://docs.julialang.org/en/latest/packages/packagelist/>`_ 
  como el `DataFrames` y `Distributions`.
	- Funciones de distribuciones son encontradas en el `paquete Distributions <https://github.com/JuliaStats/Distributions.jl>`_
	- El `paquete DataFrames <https://github.com/HarlanH/DataFrames.jl>`_ pruebe *data frames*.
	- Fórmulas para GLM deben ser escapadas: use ``:(y ~ x)`` en lugar de ``y ~ x``.
- Julia provê enuplas e tabelas de espalhamento reais, mas as listas
  de R. Quando precisar retornar múltiplos itens, você tipicamente 
  deverá utilizar uma tupla: ao invés de ``list(a = 1, b = 2)``, use 
  ``(1, 2)``. 
- Julia encoraja a todos usuários escreverem seus próprios tipos. Os
  tipos de Julia são bem mais fáceis de se usar do que os objetos S3
  ou S4 de R. O sistema de *multiple dispatch* de Julia significa que
  ``table(x::TypeA)`` e ``table(x::TypeB)`` agem como ``table.TypeA(x)``
  e ``table.TypeB(x)`` em R.
- En Julia, valores son passados y atribuídos por referencia. Se una
  función  modifica un *array*, las modificaciones  serán  visibles en lugar de la
  llamada.  Ese comportamiento es bien diferente en el R, e permite que
  nuevas  funciones operen en grandes estruturas de dados de maneira muito
  mais eficiente.
- Concatenação de vetores e matrizes é feita usando ``hcat`` e ``vcat``,
  não ``c``, ``rbind`` e ``cbind``.
- Un objeto ``Range`` ``a:b`` en Julia no es una forma abreviada de un
  vector como en R, pero si un tipo especializado de objeto que es
  utilizado para iteración sin tener que gastar una gran cantidad de memoria. Para convertir
  un ``Range`` en un vector, es necesario rodearlo con corchetes: ``[a:b]``.
- Julia tiene varias funciones que pueden modificar sus argumentos. Por 
  ejemplo, hay tanto ``sort(v)`` como ``sort!(v)``.
- En R, eficiencia requiere vectorización. En Julia, casi lo contrario es
  cierto: el código mas eficiente es a menudo desvetorizado.
- A diferencia de R, no hay una evaluación perezosa [#Del-pt]_ [#Del-en]_
  en Julia. Para la mayoría de los usuarios, ello  significa que hay pocas
  expresiones o nombres de columna sin las comillas..
- Julia no posee un tipo ``NULL``.
- No hay un equivalente de ``assign`` o ``get`` de R en Julia.


.. rubric:: Notas al pie

.. [#REPL-en] http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
.. [#Del-pt] http://pt.wikipedia.org/wiki/Avalia%C3%A7%C3%A3o_pregui%C3%A7osa
.. [#Del-en] http://en.wikipedia.org/wiki/Lazy_evaluation
