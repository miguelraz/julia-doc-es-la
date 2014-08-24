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

Há várias maneiras de chamar Julia e passar opções, semelhantes
àquelas disponívels para os programas ``perl`` e ``ruby``::

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

-  *Arrays* são indexados com colchetes, ``A[i,j]``.
-  A unidade imaginária ``sqrt(-1)`` é representada em Julia por
   ``im``.
-  Múltiplos valores são retornados e atribuídos com parênteses,
   ``return (a, b)`` e ``(a, b) = f(x)``.
-  Valores são passados e atribuídos por referência. Se uma função 
   modifica um *array*, as mudanças serão visíveis para quem chamou.
-  Julia tem *arrays* unidimensionais. Vetores-coluna são de tamanho 
   ``N``, não ``Nx1``. Por exemplo, ``rand(N)`` cria um array 
   unidimensional.
-  Concatenar escalares e *arrays* com a sintaxe ``[x,y,z]`` concatena
   na primeira dimensão ("verticalmente"). Para a segunda dimensão,
   ("horizontalmente"), use espaços, como em ``[x y z]``. Para 
   construir matrizes em blocos (concatenando nas duas primeiras 
   dimensões), é usada a sintaxe ``[a b; c d]`` para evitar confusão.
-  Dois-pontos ``a:b`` e ``a:b:c`` constroem objetos ``Range``. Para 
   construir um vetor completo, use ``linspace``, ou "concatene" o
   intervalo colocando-o em colchetes, ``[a:b]``.
-  Funções retornam valores usando a palavra-chave ``return``, ao 
   invés de por citações a seus nomes na definição da função (veja
   :ref:`man-return-keyword` para mais detalhes).
-  Um arquivo pode conter um número qualquer de funções, e todas as 
   definições vão ser visíveis de fora quando o arquivo for carregado.
-  Reduções como ``sum``, ``prod``, e ``max`` são feitas sobre cada 
   elemento de um *array* quando chamadas com um único argumento, como
   em ``sum(A)``.
-  Funções como ``sort`` que operam por padrão em colunas
   (``sort(A)`` é equivalente a ``sort(A,1)``) não possuem 
   comportamento especial para *arrays* 1xN; o argumento é retornado
   inalterado, já que a operação feita foi ``sort(A,1)``. Para ordenar
   uma matriz 1xN como um vetor, use ``sort(A,2)``.
-  Parênteses devem ser usados para chamar uma função com zero 
   argumentos, como em``tic()`` and ``toc()``.
-  Não use ponto-e-vírgula para encerrar declarações. Os resultados 
   de declarações não são automaticamente impressos (exceto no prompt 
   interativo), e linhas de código não precisam terminar com 
   ponto-e-vírgula. A função ``println`` pode ser usada para imprimir 
   um valor seguido de uma nova linha.
-  Se ``A`` e ``B`` são *arrays*, ``A == B`` não retorna um *array* de
   booleanos. Use ``A .== B`` no lugar. O mesmo vale para outros 
   operaores booleanos, ``<``, ``>``, ``!=``, etc.
-  Os elementos de uma coleção podem ser passados como argumentos para
   uma função usando ``...``, como em ``xs=[1,2]; f(xs...)``.
-  A função ``svd`` de Julia retorna os valores singulares como um
   vetor, e não como uma matriz diagonal.

Diferenças notáveis em relação a R
----------------------------------

Um dos objetivos de Julia é providenciar uma linguagem eficiente para
análise de dados e programação estatística. Para usuários de Julia 
vindos de R, estas são algumas diferenças importantes:

- Julia usa ``=`` para atribuição. Julia não provê nenhum outro 
  operador alternativo, como ``<-`` ou ``<-``.
- Julia constrói vetores usando colchetes. O ``[1, 2, 3]`` de Julia é
  o equivalente do ``c(1, 2, 3)`` de R.
- As operações matriciais de Julia são mais parecidas com a notação
  matemática tradicional do que as de R. Se ``A`` e ``B`` são matrizes,
  então ``A * B`` define a multiplicação de matrizes em Julia 
  equivalente à ``A %*% B`` de R. Em R, essa notação faria um produto
  de Hadamard (elemento a elemento). Para obter a multiplicação 
  elemento a elemento em Julia, você deve escrever ``A .* B``.
- Julia transpõe matrizes usando o operador ``'``. O ``A'`` em Julia é
  então equivalente ao ``t(A)`` de R.
- Julia não requer parênteses ao escrever condições ``if`` ou loops 
  ``for``: use ``for i in [1, 2, 3]`` no lugar de ``for (i in c(1, 2, 3))``
  e ``if i == 1`` no lugar de ``if (i == 1)``.
- Julia não trata os números ``0`` e ``1`` como booleanos. Você não
  pode escrever ``if (1)`` em Julia, porque condições ``if` só aceitam
  booleanos. No lugar, escreva ``if true``.
- Julia não provê funções ``nrow`` e ``ncol``. Use ``size(M, 1)`` no 
  lugar de ``nrow(M)`` e ``size(M, 2)`` no lugar de ``ncol(M)``.
- A SVD de Julia não é reduzida por padrão, diferentemente de R. Para
  obter resultados semelhantes aos de R, você deverá chamar ``svd(X, true)``
  em uma matrix ``X``.
- Julia é uma linguagem muito cautelosa em distinguir escalares, 
  vetores e matrizes. Em R, ``1`` e ``c(1)`` são iguais. Em Julia, 
  eles não podem ser usados um no lugar do outro. Uma consequência
  potencialmente confusa é que ``x' * y`` para vetores ``x`` e ``y``
  é um vetor de um elemento, e não um escalar. Para obter um escalar,
  use ``dot(x, y)``.
- As funções ``diag()`` e ``diagm()`` de Julia não são parecidas com 
  as de R.
- Julia não pode atribuir os resultados de chamadas de funções no lado
  esquerdo de uma operação: você não pode escrever ``diag(M) = ones(n)``
- Julia desencoraja popular o *namespace* principal com funções. A 
  maior parte das funcionalidades estatísticas para Julia é encontrada
  em `pacotes <http://docs.julialang.org/en/latest/packages/packagelist/>`_ 
  como o `DataFrames` e o `Distributions`.
	- Funções de distribuições são encontradas no `pacote Distributions <https://github.com/JuliaStats/Distributions.jl>`_
	- O `pacote DataFrames <https://github.com/HarlanH/DataFrames.jl>`_ provê *data frames*.
	- Fórmulas para GLM devem ser escapadas: use ``:(y ~ x)`` no lugar de ``y ~ x``.
- Julia provê enuplas e tabelas de espalhamento reais, mas as listas
  de R. Quando precisar retornar múltiplos itens, você tipicamente 
  deverá utilizar uma tupla: ao invés de ``list(a = 1, b = 2)``, use 
  ``(1, 2)``. 
- Julia encoraja a todos usuários escreverem seus próprios tipos. Os
  tipos de Julia são bem mais fáceis de se usar do que os objetos S3
  ou S4 de R. O sistema de *multiple dispatch* de Julia significa que
  ``table(x::TypeA)`` e ``table(x::TypeB)`` agem como ``table.TypeA(x)``
  e ``table.TypeB(x)`` em R.
- Em Julia, valores são passados e atribuídos por referência. Se uma
  função modifica um *array*, as mudanças serão visíveis no lugar de
  chamada.  Esse comportamento é bem diferente do de R, e permite que
  novas funções operem em grandes estruturas de dados de maneira muito
  mais eficiente.
- Concatenação de vetores e matrizes é feita usando ``hcat`` e ``vcat``,
  não ``c``, ``rbind`` e ``cbind``.
- Um objeto ``Range`` ``a:b`` em Julia não é uma forma abreviada de um
  vetor como em R, mas sim um tipo especializado de objeto que é 
  utilizado para iteração sem muito gasto de memória. Para um converter
  um ``Range`` em um vetor, você precisa cercá-lo por colchetes: ``[a:b]``.
- Julia tem várias funções que podem alterar seus argumentos. For 
  exemplo, há tanto ``sort(v)`` quanto ``sort!(v)``.
- Em R, eficiência requer vetorização. Em Julia, quase o contrário é
  verdadeiro: o código mais eficiente é frequentemente o desvetorizado.
- Diferentemente de R, não há avaliação preguiçosa [#Del-pt]_ [#Del-en]_
  em Julia. Para a maioria dos usuários, isso significa que há poucas
  expressões ou nomes de coluna sem aspas.
- Julia não possui tipo ``NULL``.
- Não há equivalente do ``assign`` ou ``get`` de R em Julia.


.. rubric:: Notas de rodapé

.. [#REPL-en] http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
.. [#Del-pt] http://pt.wikipedia.org/wiki/Avalia%C3%A7%C3%A3o_pregui%C3%A7osa
.. [#Del-en] http://en.wikipedia.org/wiki/Lazy_evaluation
