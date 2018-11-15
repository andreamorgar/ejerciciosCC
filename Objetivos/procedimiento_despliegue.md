### PASOS SEGUIDOS PARA EL DESPLIEGUE

A continuación se encuentran enumerados los pasos que he seguido para realizar el despliegue.

### 1. Registro en Travis
El primer paso necesario es disponer de ua cuenta en Travis. Podemos iniciar sesión directamente con nuestro usuario de Github. De esta forma, podremos gestionar el testeo de los repositorios que tengamos en dicha cuenta.

### 2. Activar el repositorio de nuestro proyecto en Travis, para poder pasar los tests.
Simplemente buscamos desde la plataforma Travis el repositorio que queremos testear, y lo seleccionamos.

### 3. Crear .travis.yml
Para poder hacer el testeo del repositorio, necesitamos crear el archivo [.travis.yml](https://github.com/andreamorgar/ProyectoCC/blob/master/.travis.yml) y completarlo con mis especificaciones. Para ello he seguido los pasos de la [documentación](https://docs.travis-ci.com/user/languages/python/). Este fichero, lo ponemos en la ruta raiz de nuestro repositorio.

### 4. Tenemos que ver como se despliega en HEROKU
Para ello, seguimos la [documentación](https://docs.travis-ci.com/user/deployment/heroku/) oficial que encontramos en Travis.

### 5. Instalo los clientes.
 - [Cliente heroku](https://devcenter.heroku.com/articles/heroku-cli)
 - [Cliente travis](https://github.com/travis-ci/travis.rb#installation). En este caso, hay que tener en cuenta que debemos tener instalado [Ruby](https://www.ruby-lang.org/es/) en nuestro ordenador. En mi caso, no lo tenía, por lo que tuve que proceder con la [instalación](http://www.ruby-lang.org/en/downloads/). Para ello, simplemente lo instalamos desde el gestor de paquetes. Además, para un correcto funcionamiento, hay que asegurarse de que la versión utilizada sea superior a la 2.0.

### 6.  Instalo Cliente travis
Una vez tenemos instalado Ruby, podemos proceder con la instalación de Travis. Para ello intentamos la orden que viene en la documentación:
~~~
$ gem install travis -v 1.8.9 --no-rdoc --no-ri
~~~
Sin embargo, no funciona, y siguiendo las correcciones [aquí](https://github.com/travis-ci/travis.rb/issues/391) y [aquí](https://github.com/travis-ci/travis.rb#ubuntu), vemos que hay un problema con las dependencias, y por ello instalo la siguiente versión de Ruby.
~~~
$ sudo apt-get install ruby2.3-dev (porque yo tengo ruby2.3)
~~~

Al repetir la orden correspondiente, podemos continuar la instalación.

### 7. Intento realizar encriptación mediante Travis

Para ello, utilizamos la siguiente orden, tal y como viene indicado en los pasos de la documentación de Travis que estamos siguiendo.
~~~
encrypt $(heroku auth:token) --add deploy.api_key
~~~
Sin embargo, por alguna razón, no consigo que funcione este método, por lo que intento una forma automática de lanzar los test antes del despliegue en heroku. Por tanto, ya no hace falta que introduzca dicha encriptación en .travis.yml, tal y como se indica en la documentación.


### 8. Creo el fichero requeriments.txt
Con este fichero, podremos instalar mediante pip todo lo necesario para que funcione nuestro servicio. Evitando usar la orden "pip freeze", he utilizado el paquete [pipreqs](https://github.com/bndr/pipreqs), que genera el fichero _requeriments.txt_ que se puede ver [aquí](https://github.com/andreamorgar/ProyectoCC/blob/master/requirements.txt)


### 9. Fichero procfile
Para ello sigo las indicaciones de [aquí](https://devcenter.heroku.com/articles/python-gunicorn). Como se está indicando que utilicemos guinicorn, debemos instalarlo propiamente. [Guincorn](https://gunicorn.org/) es un servidor HTTP para Python, que es compatible con Flask, lo que me permite su utilización. Este hecho provoca que tenga que volver a actualizar mi fichero _requeriments.txt_, ya que debe añadir esta nueva incorporación.

### 10. Fichero runtime.txt
Necesitamos también crear un fichero [runtime.txt](https://github.com/andreamorgar/ProyectoCC/blob/master/runtime.txt), para especificarle a Heroku la versión de Python con la que debe ejecutar los ficheros. En este caso, es importante destacar un error que tuve, ya que Heroku solo es compatible con las versiones de Python 3.6.6. y 3.7.0. En mi caso, yo trabajaba con la versión 3.5.2, lo que impedía que se realizara de forma correcta el despliegue. Viendo las posibilidades de corrección que me proporcionaba Heroku mediante la terminal, simplemente tenía que cambiar la versión en runtime.txt para poder continuar, teniendo en cuenta que después debería comprobar que los tests seguían funcionando a pesar de este cambio en la versión. De esta forma, he podido comprobar, cómo los tests no solo comprobaban que el código estaba correcto, sino que también sirve para asegurar el funcionamiento de mi proyecto en el despliegue.

Se puede acceder a mi fichero runtime.txt desde [aquí](https://github.com/andreamorgar/ProyectoCC/blob/master/runtime.txt).

### 11. Desplegar en heroku
Para ello, seguimos los pasos indicados [aquí](https://devcenter.heroku.com/articles/getting-started-with-python#deploy-the-app). Las tres órdenes importantes a realizar son las siguientes:
~~~
heroke create
git push heroku master
~~~
Con dichas órdenes, estamos creando un proyecto de despliegue en Heroku cuando hacemos push del repositorio que queremos desplegar. Es muy útil que te permita ver los [logs](https://devcenter.heroku.com/articles/getting-started-with-python#view-logs), porque te das cuenta de lo que realmente está pasando, de si se ha realizado correctamente el despliegue y, en caso de que no, qué es lo que ha ocurrido. Por ejemplo, a mí me ha servido para poder averiguar problemas que me daba al desplegar, como puede ser que hayas ocupado todas las facilidades de la cuenta gratuita de Heroku.


### 12. Conexión con travis, para que nuestro proyecto no se despliegue antes de pasar los tests
Desde nuestra cuenta en Heroku, nos vamos a la pestaña de Deploy, y seleccionamos la opción de *Enable automatic deploys*, para que con cada push a nuestro repositorio, se pueda desplegar automáticamente en Heroku. Para que los test se realicen previo al despliegue, tenemos que seleccionar la opción de no desplegar hasta que se ejecuten de forma correcta los tests de travis. Todo este proceso lo realizo siguiendo las instrucciones de [aqui](https://medium.com/@felipeluizsoares/automatically-deploy-with-travis-ci-and-heroku-ddba1361647f)

### 13. Hacemos $ heroku login, que nos manda a la web a hacer login
~~~
$ heroku git:remote -a <nombre proyecto>
~~~

[Aquí](https://devcenter.heroku.com/articles/git) se puede ver más o menos todos los pasos que hemos ido siguiendo a lo largo del proceso.


### Otros errores
Por último faltaría un problema con la distribución en directorios de mis archivos, que impide que se ejecuten mis tests al no estar en la misma ruta que los ficheros a testear.
Este problema lo he solucionado como se indica [aquí](https://stackoverflow.com/questions/1732438/how-do-i-run-all-python-unit-tests-in-a-directory), con la orden:
$python -m unittest discover test/


### Una vez seguidos todos estos pasos, se obtiene un correcto despliegue del servicio REST que hemos realizado.
