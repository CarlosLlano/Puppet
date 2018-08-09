# DEMO PUPPET #

**Iniciar maquinas virtuales:**
```
vagrant up
```

**Configuracion del servidor**

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

Crear un FQDN (fully qualified Domain name):
```
ifconfig #determinar ip address
vi /etc/hosts #agregar linea: <ip-address> puppet-master.local puppet-master
hostname -f #comprobar fqdn
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

Cambiar path:
```
vim ~/.bashrc
export PATH=$PATH:/opt/puppetlabs/bin/
source ~/.bashrc
```

**Configuracion del cliente**

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

Crear un FQDN (fully qualified Domain name):
```
ifconfig #determinar ip address
vi /etc/hosts #agregar linea: <ip-address> puppet-client.local puppet-client
hostname -f #comprobar fqdn
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

**Configuracion para comunicacion entre servidor y cliente**

En el servidor (puppet_master):
```
vim /etc/hosts/ #agregar: <ip-address-client> puppet-client.local puppet-client
ping puppet-client.local
```

En el cliente (puppet_client):
```
vim /etc/hosts/ #agregar: <ip-address-master> puppet-master.local puppet-master
ping puppet-master.local
```

**Firma de certificados**

Desde el cliente:
```
puppet agent -t  --server=puppet-master.local
```

Desde el servidor, ver certificados pendientes de firmar:
```
puppet cert list --all
```

Firmar certificado del cliente:
```
puppet cert sign puppet-client.local
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
vim site.pp
```

Agregar lineas:
```ruby
node 'puppet-client.local' {
    include apache
}
```

**Solicitar configuracion desde el cliente**
```
puppet agent -t  --server=puppet-master.local --debug
```