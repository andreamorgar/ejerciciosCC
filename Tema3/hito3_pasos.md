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
Para entender bien el funcionamiento de un playbook de ansible, y sobretodo, qué hace exactamente y de qué forma, podemos consultar el apartado correspondiente en la guía oficial [aquí](https://docs.ansible.com/ansible/2.7/user_guide/playbooks_intro.html). De esta página, es de donde nos basaremos para llevar a cabo este apartado.

- Lo primero: python3 [aquí](https://medium.com/@perwagnernielsen/ansible-tutorial-part-2-installing-packages-41d3ab28337d)
