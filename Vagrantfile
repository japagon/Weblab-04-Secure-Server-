# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "debian/bullseye64"
  
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.linked_clone = true
  end

  ####################################################################
  # dns.sistema.sol
  ####################################################################
  config.vm.define "dns" do |dns|
    dns.vm.provider "virtualbox" do |vb|
      vb.name = "http-lab-2-dns"
      vb.memory = "256"
    end
    dns.vm.hostname = "dns.sistema.sol"    
    dns.vm.network :private_network, ip: "192.168.56.100", hostname: true

    dns.vm.provision "shell", inline: <<-SHELL
      # instalar paquetes
      apt-get update
      apt-get install -y bind9 bind9-utils apache2-utils

      # copiar ficheros
      mkdir -p /etc/bind
      mkdir -p /var/lib/bind
      cp -f /vagrant/config/dns/named /etc/default/named
      cp -f /vagrant/config/dns/named.conf.options /etc/bind/named.conf.options
      cp -f /vagrant/config/dns/named.conf.local /etc/bind/named.conf.local
      cp -f /vagrant/config/dns/db.sistema.sol /var/lib/bind/db.sistema.sol
      cp -f /vagrant/config/dns/db.192.168.56 /var/lib/bind/db.192.168.56

      # reiniciar servicio
      chown -R bind:bind /var/lib/bind
      systemctl restart bind9

      echo "nameserver 127.0.0.1" > /etc/resolv.conf
    SHELL
  end

  ####################################################################
  # tierra.sistema.sol
  ####################################################################
  config.vm.define "tierra" do |tierra|
    tierra.vm.provider "virtualbox" do |vb|
      vb.name = "http-lab-2-tierra"
      vb.memory = "256"
    end
    tierra.vm.hostname = "tierra.sistema.sol"
    tierra.vm.network :private_network, ip: "192.168.56.101", hostname: true
    
    tierra.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 apache2-utils
      cp -f /vagrant/config/tierra/apache2.conf /etc/apache2/apache2.conf

      # TODO: Configurar sitio virtual
      cp -f /vagrant/config/tierra/discovery.sistema.sol.conf /etc/apache2/sites-available/discovery.sistema.sol.conf

      # Copiar los recursos de nuestra web al Document Root
      # TODO
      mkdir -p /var/www/discovery.sistema.sol
      cp -r /vagrant/config/tierra/discovery.sistema.sol/* /var/www/discovery.sistema.sol/

      # Habilitar/deshabilitar sitios virtuales      
      # TODO
      a2dissite 000-default
      a2ensite discovery.sistema.sol.conf

      # Habilitar módulos necesarios
      # TODO
      a2enmod auth_digest
      a2enmod authz_groupfile

      # Copiar ficheros de configuración de la autenticación
      # TODO
      htpasswd -cb /etc/apache2/.htpasswd_basic arturo arturo
      htpasswd -b /etc/apache2/.htpasswd_basic ana ana
      htpasswd -b /etc/apache2/.htpasswd_basic maria maria
      printf "ventas: arturo\\ndesarrollo: ana\\n" > /etc/apache2/.htgroups
      echo "commander:astronauts:19c426e5b25e9bd568eaa17b5f0a6503" > /etc/apache2/.htpasswd_digest

      systemctl restart apache2
      echo "nameserver 192.168.56.100" > /etc/resolv.conf
      echo "search sistema.sol" >> /etc/resolv.conf

      # TODO: Configuración SSL para Weblab 4
      # Habilitar el módulo SSL de Apache
      a2enmod ssl 

      # Crear certificado autofirmado para discovery.sistema.sol
      openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /etc/ssl/private/apache-selfsigned.key \
        -out /etc/ssl/certs/apache-selfsigned.crt \
        -subj "/C=ES/ST=Granada/L=Granada/O=SRI/OU=Weblab4/CN=discovery.sistema.sol" 

      # Copiar y habilitar el nuevo sitio SSL
      cp /vagrant/config/tierra/discovery-ssl.conf /etc/apache2/sites-available/
      a2ensite discovery-ssl.conf 

    SHELL
  end
end