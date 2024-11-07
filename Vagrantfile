# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  
  # Servidor Web 1 - Apache
  config.vm.define "web1" do |web1|
    web1.vm.box = "ubuntu/bionic64"
    web1.vm.network "private_network", ip: "192.168.33.10"
    web1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
    SHELL
    web1.vm.synced_folder "./web1", "/var/www/html"
  end
  
  # Servidor Web 2 - Apache
  config.vm.define "web2" do |web2|
    web2.vm.box = "ubuntu/bionic64"
    web2.vm.network "private_network", ip: "192.168.33.11"
    web2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
    SHELL
    web2.vm.synced_folder "./web2", "/var/www/html"
  end
  
  # Servidor de Balanceo de Carga - Nginx
  config.vm.define "loadbalancer" do |lb|
    lb.vm.box = "ubuntu/bionic64"
    lb.vm.network "private_network", ip: "192.168.33.12"
    lb.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    lb.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      cat <<EOF > /etc/nginx/conf.d/load_balancer.conf
      upstream backend {
        server 192.168.33.10;
        server 192.168.33.11;
      }
      
      server {
        listen 80;
        
        location / {
          proxy_pass http://backend;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
        }
      }
      EOF
      systemctl restart nginx
    SHELL
  end
end
