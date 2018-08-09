# DEMO PUPPET #

**Iniciar maquinas virtuales:**
```
vagrant up
```

**Configuración del servidor**

Cambiar hostname a puppet-master:
```
vagrant ssh puppet_master
sudo su
vi /etc/hostname #cambiar a puppet-master
exit
vagrant reload puppet_master
vagrant ssh puppet_master
sudo su
hostname #verificar cambio de hostname
```


Agregar repositorio para descarga de puppet:
```
curl -O https://apt.puppetlabs.com/puppetlabs-release-pc1-xenial.deb
dpkg -i puppetlabs-release-pc1-xenial.deb
apt-get update -y
```

Instalar puppet server:
```
apt-get install puppetserver -y
```

Configurar memoria RAM a 3gb:
```
vim /etc/default/puppetserver 
```

![captura de pantalla 2018-08-08 a la s 10 11 22 p m](https://user-images.githubusercontent.com/17281733/43913154-e70f0b68-9bc9-11e8-9bc3-9c01a59efdf1.png)



Habilitar puerto de puppet:
```
ufw allow 8140
```

Iniciar puppet y habilitar para iniciar en boot time:
```
systemctl start puppetserver
systemctl enable puppetserver
```

Comprobar estado:
```
systemctl status puppetserver
```

![captura de pantalla 2018-08-08 a la s 10 15 17 p m](https://user-images.githubusercontent.com/17281733/43913184-f9d2b3da-9bc9-11e8-8870-7b4d61d6d4f0.png)


Cambiar path:
```
vim ~/.bashrc
export PATH=$PATH:/opt/puppetlabs/bin/
source ~/.bashrc
```

**Configuración del cliente**

Cambiar hostname a puppet-client:
```
vagrant ssh puppet_client
sudo su
vi /etc/hostname #cambiar a puppet-client
exit
vagrant reload puppet_client
vagrant ssh puppet_client
sudo su
hostname #verificar cambio de hostname
```

Agregar repositorio para descarga de puppet:
```
curl -O https://apt.puppetlabs.com/puppetlabs-release-pc1-xenial.deb
dpkg -i puppetlabs-release-pc1-xenial.deb
apt-get update -y
```

Instalar puppet agent:
```
apt-get install puppet-agent -y
```

Iniciar puppet agent y habilitar para iniciar en boot time:
```
systemctl start puppet
systemctl enable puppet
```

Comprobar estado:
```
systemctl status puppet
```

Cambiar path:
```
vim ~/.bashrc
export PATH=$PATH:/opt/puppetlabs/bin/
source ~/.bashrc
```

**Configuración para comunicacion entre servidor y cliente**

En todas las maquinas, editar archivo /etc/hosts:
```
vim /etc/hosts/ #agregar: <ip-address-puppet-master> puppet
ping puppet
```

**Firma de certificados**

Desde el servidor, ver certificados pendientes de firmar:
```
puppet cert list --all
```

Firmar certificado del cliente:
```
puppet cert sign puppet-client
```

```
puppet cert list --all
```


**Crear modulo para configurar apache en el cliente**


Desde el servidor, descargar modulo de apache:
```
cd /etc/puppetlabs/code/environments/production
cd modules/
puppet module install puppetlabs-apache --version 3.2.0 #https://forge.puppet.com/puppetlabs/apache
```

Crear archivo que servira para decirle a puppet como debe estar la infraestructura:
```
cd ../manifests/ #/etc/puppetlabs/code/environments/production/manifests
vim site.pp
```

Agregar lineas:
```ruby
node 'puppet-client' {
    include apache
}
```

**Solicitar configuración desde el cliente**

Por defecto puppet agent pregunta por configuraciones cada 30 minutos (1800 segundos), como lo declara la variable runinterval:
```
puppet agent --configprint runinterval
```

Para no esperar los 30 minutos, hacer una solicitud de configuracion desde el cliente:
```
puppet agent -t --debug
```

puppet agent realiza la configuración requerida (apache) instalando todos los paquetes necesarios. Despues de unos segundos se puede comprobar la instalación de apache en el cliente. Para ello, digitar la direccion ip del cliente en el navegador:

![captura de pantalla 2018-08-08 a la s 11 36 17 p m](https://user-images.githubusercontent.com/17281733/43913755-8d3bf496-9bcb-11e8-91a2-cac7b79729f2.jpg)

# RECURSOS #
* open source modules - https://forge.puppet.com/