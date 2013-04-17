<h3> La documentación puede ser visualizada online en <a url="https://readthedocs.org/projects/julia_es_pe/" >ReadTheDocs!</a> </h3>

<h1> README de la traducción castellana </h1>

Contribuyendo
Contribuciones son siempre bienvenidas, y puede ser realizadas de la siguientes formas, en el siguiente orden:

- Enviar un pull request directamente aqui en el GitHub. Esto puede ser realizado automaticamente si ud. posee una cuenta en el site, basta editar un archivo y enviar.
- Enviar un patch via diff o git por e-mail a lbenitesanchez@gmail.com
- Enviar un arquivo entero traducido al e-mail de arriba.

Recomendaciones
-------------

Para uniformizar y organizar la traducción, hay algunas recomendaciones abajo.
Todas ellas puede ser alteradas caso la mayoría llegue a un acuerdo, pero en general 
es bueno mantenerlas para garantizar un proyecto fácil de ser administrado y actualizado.

Las lineas deben preferentemente tener 71 carácteres o menos, para facilitar la edición
del texto en los celulares, tabletas, etc. Si dos líneas están en secuencia, ellas no van 
a poder ser unidas en el mismo párrafo automaticamente en la documentación final.

Términos en lengua extranjera deben estar en itálico.

En la documentación existen algunos links para Wikipédia en inglés. Esos links deben ser 
mantenidos y adicionados a los correspondientes Wikipédia castellana, preferentemente
en la forma de notas de pie de página.

Al traducir la parte de la documentación, preferentemente utilize la lista, en orden
alfabética de los términos en inglés, hay algunos términos técnicos que no pueden ser
traducidor al castellano.


| Inglés                               | Castellano                           |
|--------------------------------------|--------------------------------------|
| API                                  | *API*                                |
| features                             | características                      |
| departures                           | características                      |
| environment                          | entorno                              |
| just-in-time                         | *just-in-time*                       |
| multiple dispatch                    | *multiple dispatch*                  |
| performance                          | desempeño                            |
| prototyping                          | experimento                          |
| wrapper                              | *wrapper*                            |
| string                               | *string*                             |
| typing                               | tipeando                             |
| tuple                                | tuple                                |
| hash table                           | tabla de dispersión                  |

README de la Documentación de Julia
====================================

La documentación de Julia está escrita en reStructuredText, y una buena referencia 
es el capítulo [Documenting Python](http://docs.python.org/devguide/documenting.html)
do Python Developer's Guide.

Compilando la documentación
-------------------------

La documentación es compilada usando  [Sphinx](http://sphinx.pocoo.org/) e LaTeX.
En Ubuntu, ud. necesita tener los siguintes paquetes instalados:

    python-sphinx
    texlive
    texlive-latex-extra

Y luego compilar

    $ make helpdb.jl
    $ make html
    $ make latexpdf

Estrutura de los archivos
-------------------------

    conf.py             Configuración del Sphinx
    helpdb.jl           REPL help database
    sphinx/             Extensiones y plugins del Sphinx
    sphinx/jlhelp.py    Plugin Sphinx para compilar helpdb.jl
    stdlib/             Documentación de la biblioteca por defecto de Julia
    _themes/            Temas html do Sphinx
