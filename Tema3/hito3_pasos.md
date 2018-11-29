1. Instalar ansible
pip install paramiko PyYAML jinja2 httplib2 ansible

2. Me ha dicho que dentro de mi entorno no tengo la versión más actualizda de pip, así que la actualizo. pip install --upgrade pip

3. Instalo virtualbox, pero me da problemas pq me pide que quite la seguridad de mi SO. Por eso, sigo los pasos aqui:
https://stegard.net/2016/10/virtualbox-secure-boot-ubuntu-fail/. Faltan pasos 5,6,y 7 (?))))



https://app.vagrantup.com/boxes/search?utf8=%E2%9C%93&sort=downloads&provider=&q=ubuntu

Instalar Vagrant https://howtoprogram.xyz/2016/07/23/install-vagrant-ubuntu-16-04/


sudo apt-get update
sudo apt-get install vagrant
vagrant -- version  (sale vagrant 1.8.1)


Nos vamos a ejercicios (mi carpeta de github) desde local, y abrimos una nueva carpeta, que se va a llamar tema3.
Entramos a esa carpeta y hacemos vagrant init






Vamos a crear una máquina virtual por defecto, del nombre que nosotros queramos. En este caso voy a utilizar la versión LTS de ubuntu server 16.04, y vamos a escribir en VagrantFile de la misma forma que lo vimos en el seminario. Es decir, estamos creando una máquina default.

Con vagrant estamos configurando la maquina virtual que vamos a utilizar inicialmente.



### Instalación de Ansible
El primer paso es instalar ansible en la máquina con la que estemos trabajando. Para poder disponer de ansible podemos instalarla desde dos formas principales:
- Utilizar el gestor de paquetes *apt-get*, tal y como se puede ver indicado [aquí](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-16-04).
- Instalarlo mediante *pip*. En este ejercicio, vamos a seguir esta segunda forma, ya que como se vio en el seminario de Ansible de la asignatura, el instalar Ansible mediante *pip* tiene sus ventajas. Esto se debe a que te instala, de forma automática, otros modulos necesarios, como por ejemplo para trabajar con YAML (lo necesitaremos más tarde). Podemos ver cómo realizar la instalación [aquí](https://docs.ansible.com/ansible/2.7/installation_guide/intro_installation.html#latest-releases-via-pip)

En la siguiente imagen podemos ver cómo se ha llevado a cabo la instalación de ansible, versión 2.7.2. (AQUI IMAGEN DE instalacionansible.png)

### Fichero de ansible.cfg
- Primero indicamos a false que no se haga la comprobación de claves del host, para evitar los problemas de Man in The Middle, que SSH no haga la comprobación de clave, y podamos entrar con diferentes nombres y mac.
- Estamos diciendole cual va ser el nombre de fichero de host. Tendremos un fichero *ansible.host* con el que vamos a trabajar y vamos a definir las máquinas con las que estemos.

### Fichero ansible_hosts
Los aspectos importantes de este fichero son:
- Indicar el puerto
- Indicar bien la ruta hacia la máquina y private_key

### Funcionamiento de la MV
Para ver si la máquina virtual que hemos creado a través de Vagrant está operativa, podemos hacer un simple ping, y de esta forma comprobarlo. En la siguiente figura, podemos ver cómo realmente funciona. Sin embargo, en la primera orden ejecutada, podemos ver cómo estamos haciendo ping a todas las máquinas virtuales. En nuestro caso, tenemos únicamente una, por lo tanto la orden ejecutada es equivalente a hacer ping directamente de nuestra máquina. Lo hacemos también, y vemos como efectivamente obtenemos igual resultado.

![Ping a la máquina](https://raw.githubusercontent.com/andreamorgar/ejerciciosCC/master/images/pingMaquina.png)



### Provisionamiento
Vamos a instalar en la máquina virtual todo aquello que necesitemos. Para ello, podemos consultar la guía oficial [aquí](https://docs.ansible.com/ansible/2.7/scenario_guides/guide_vagrant.html), concretamente el apartado Vagrant Setup. Aquí se muestra un ejemplo de cómo podemos modificar el fichero VagrantFile para provisionar una única máquina. Para ello, vamos a usar el Playbook.

#### Añadimos provisionamiento a VagrantFile

El fichero, con el añadido, sería tal y como se muestra a continuación.
~~~
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = "ubuntuAndrea"

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "playbook.yml"
  end
end
~~~
Podemos destacar dos aspectos principales:
- La sección de provisionamiento hace referencia a un playbook de ansible al que en el fichero *VagrantFile* hemos llamado *playbook.yml*.
- Vagrant ejecutará el fichero de provisionamiento  que hemos definido una vez que la máquina virtual arranca y tiene acceso a SSH.
- El hecho de tener activada la opción verbose va a provocar que se nos muestre la totalidad del comando del ansible playbook que utilicemos. Todavía no sabemos qué nos mostrará, entonces la voy a dejar de momento, y más adelante ya veremos si se quita del fichero o no.


#### Fichero playbook.yml
Para entender bien el funcionamiento de un playbook de ansible, y sobretodo, qué hace exactamente y de qué forma, podemos consultar el apartado correspondiente en la guía oficial [aquí](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_intro.html). Además, se sugiere consultar este otro [enlace](https://github.com/ansible/ansible-examples), pues contiene una serie de ejemplos y buenas prácticas que se pueden llevar a cabo. De estos dos enlaces, es de donde nos basaremos para llevar a cabo este apartado.

- Lo primero: python3 [aquí](https://medium.com/@perwagnernielsen/ansible-tutorial-part-2-installing-packages-41d3ab28337d)


En primer lugar instalamos git, ya que nos hace falta para poder acceder a nuestro proyecto desde la máquina virtual que hemos creado mediante Vagrant. Para ello, modificamos el fichero *playbook.yml* tal y como se muestra a continuación:
~~~
---
- hosts: all
  become: yes
  tasks:
    - name: Instala git
      apt: pkg=git state=present
~~~
Como se puede ver, simplemente hemos utilizado el gestor de paquetes para instalar git.







De momento, tenemos hasta este punto un único playbook genérico. Para su creación, me he inspirado en el tutorial al que se puede acceder desde [aquí](https://medium.com/@perwagnernielsen/ansible-tutorial-part-2-installing-packages-41d3ab28337d).

Hemos utilizado una máquina Debian con Python 3, que ha sido escogida debido a que de esta forma, no solo contamos con Python instalado en la máquina, sino que por defecto ya trae consigo Python 3, tal y como se puede consultar [aquí](https://linuxconfig.org/how-to-change-default-python-version-on-debian-9-stretch-linux).

En primer lugar, el archivo playbook.yml va a representar únicamente a aquellas cosas genéricas que queramos instalar en una máquina virtual. Por tanto, tendríamos que instalar dos cosas indispensables para nuestro servicio web de la práctica anterior, las cuáles obtendremos a través del gestor de paquetes **apt**:
- **Git**: sin git, entre otras cosas, no podemos hacer clone de nuestro repositorio, por lo que es esencial en este caso.

- **Pip**: necesario porque voy a instalar los requerimientos necesarios para poder mi proyecto en la máquina de esa forma. Como estamos trabajando con Python 3, queremos pip 3 concretamente.

A continuación podemos ver
~~~
---
- hosts: all
  become: yes
  tasks:
    - name: Install base packages
      apt: name={{ item }} state=present
      with_items:
        - git
        - python3-pip
      tags:
        - packages
~~~

#### Siguiendo las buenas prácticas....

A pesar de que hay múltiples fuentes que defienden que un playbook debe ser un proceso cerrado (como por ejemplo [aquí](https://serverfault.com/questions/750856/how-to-run-multiple-playbooks-in-order-with-ansible)), esta afirmación no es compartida por el estándar de buenas prácticas de Ansible.

Si consultamos la guía de buenas prácticas de Ansible, podemos encontrar una sección llamada *Creating Reusable Playbooks*, a la cuál podemos acceder desde [aquí](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse.html). En ella podemos ver, que ess preferible reutilizar distintos playbooks, en lugar de empezar con uno de la forma que vimos arriba. Por ello, la parte específica de lo que queremos ejecutar, la vamos a especificar en un segundo playbook, que contendrá todos aquellos específicos para poder ejecutar y desplegar nuestro proyecto:

- **git clone**: para poder descargar nuestro repositorio en la máquina virtual.

- **Dependencias específicas de la aplicación**: en este caso, aquello que sea imprescindible para el correcto funcionamiento del servicio web.


Por tanto utilizaremos un nuevo fichero, al que hemos llamado *especific_playbook.yml*,
el cuál se encargará de incorporar aquellos aspectos esenciales.


#### Fichero especific_playbook.yaml

Para git clone: https://docs.ansible.com/ansible/2.5/modules/git_module.html
