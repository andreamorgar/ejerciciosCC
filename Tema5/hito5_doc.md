# Hito 5 : Orquestación de máquinas virtuales



## Usar Vagrant desde Azure
Nos creamos un nuevo directorio en el repositorio de ejercicios, que he llamado `tema 5`.
En este repositorio ejecutamos la siguiente orden para poder crear el fichero Vagrantfile y comenzar a trabajar con vagrant.
~~~
$ vagrant init
~~~

### Vagrantfile
Tras ejecutar esta orden en el directorio en el que queramos trabajar con Vagrant, se nos creará el fichero *Vagrantfile*. Al realizar dicha orden, se nos crea un contenido por defecto, pero en este caso, lo borraremos y lo sustituiremos por uno apropiado para poder trabajar con Azure.

##### Configuración para una máquina en Azure

Lo primero es descargar el plugin de Azure, y así poder configurar todo para poder trabajar con Vagrant en Azure. Siguiendo los pasos vistos [aquí](https://github.com/Azure/vagrant-azure), podemos obtener este plugin con las siguientes órdenes:

~~~
$ vagrant box add azure https://github.com/azure/vagrant-azure/raw/v2.0/dummy.box --provider azure
$ vagrant plugin install vagrant-azure
~~~

Una vez dispongamos del plugin, procedemos a configurar Vagrantfile. Para ello,

<!-- partiremos del siguiente *Vagrantfile*, que contiene lo mínimo para crear una máquina virtual en Azure con Vagrant. Este código se puede consultar [aquí](https://github.com/Azure/vagrant-azure).

En concreto, deberemos especificar el valor de los siguientes parámetros:
- `az.tenant_id`
- `az.client_id`   ID del cliente
- `az.client_secret `
- `az.subscription_id ` ID de la suscripción: -->



Tenemos que descargar el plugin de ansible y configurar todo para poder trabajar con vagrant en azure. Seguimos los pasos vistos [aquí](https://github.com/Azure/vagrant-azure), y creamos el fichero de ansible tal y como viene especificado. Sin embargo, nos podemos ver con un problema inicial, y es que indica que no se le ha facilitado un ID de suscripción, ni tampoco más valores.


Esto se debe a que, previamente, nosotros debemos exportar como variables de entorno esos valores, porque sino no se van a detectar, tal y como podemos ver en este [otro tutorial secundario](https://blog.scottlowe.org/2017/12/11/using-vagrant-with-azure/).


~~~
$ export AZURE_TENANT_ID=d0904206-39a9-4ae1-993d-1d53b040e8f1
$ export AZURE_CLIENT_ID=50e16052-46f5-4385-8ba5-05260dbf025c
$ export AZURE_CLIENT_SECRET=39ce4796-5095-43e7-87a1-83f8bf7878a1
$ export AZURE_SUBSCRIPTION_ID=35e636c2-564f-4b49-8147-2735efe662a7
~~~


>Lo adecuado sería tenerlas en bashrc o algo así, para poder usar siempre eso y no estar continuamente declarando las variables de entorno. Revisar esto.




##### Otros parámetros en Vagrantfile
Además, le podemos añadir una serie de líneas que nos permitan especificar aspectos concretos de la máquina virtual que queremos crear, como puede ser el nombre de la máquina, el grupo de recursos asociado, o la región a utilizar. Concretamente, especificaremos todos los parámetros con los que hemos ido trabajando hasta ahora:
- Imagen de Ubuntu Server con la versión 16.04
- Grupo de recursos en la región **Francia Central**
- Tamaño de la máquina virtual **Basic_A0**
- Abrimos el **puerto 80**, que es desde el que ejecutamos la aplicación.


~~~
require 'vagrant-azure'
Vagrant.configure('2') do |config|
  config.vm.box = 'azure'

  # Usamos una clave ssh local para conectar al box de vagrant remoto
  config.ssh.private_key_path = '~/.ssh/id_rsa'
  config.vm.provider :azure do |az, override|

    az.tenant_id = ENV['AZURE_TENANT_ID']
    az.client_id = ENV['AZURE_CLIENT_ID']
    az.client_secret = ENV['AZURE_CLIENT_SECRET']
    az.subscription_id = ENV['AZURE_SUBSCRIPTION_ID']

    az.vm_image_urn = 'Canonical:UbuntuServer:16.04-LTS:latest'
    az.vm_name = 'ubuntuandrea1'
    az.vm_size = 'Standard_B1s'
    az.resource_group_name = 'resourcegroup1'
    az.location = 'francecentral'
    az.tcp_endpoints = 80
  end
end
~~~







### Ansible

#### Uso de roles

Podemos realizar la configuración de ansible  la configuración en un mismo fichero. Esto esta bien si escribimos el playbook para un único despliegue o la configuración es simple. Sin embargo, para escenarios mas complejos es mejor utilizar roles, donde podremos moldear más a nuestro gusto las configuraciones.


Los roles, nos permiten crear un playbook con una mínima configuración y definir toda la complejidad y lógica de las acciones a más bajo nivel.

https://www.ncora.com/blog/como-se-usan-los-roles-y-playbooks-en-ansible/


## MONGO


https://docs.ansible.com/ansible/latest/plugins/lookup/mongodb.html
