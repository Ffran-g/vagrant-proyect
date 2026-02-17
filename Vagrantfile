Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  
  # Variable con la ruta donde se guardarán los archivos de la MV (VDI, etc.)
  ruta_personalizada = "D:/FranGarciaEgea/Despliege/MV/Maquinas_Vagrant"

  # Configuración global para VirtualBox
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["setproperty", "machinefolder", ruta_personalizada]
  end

  # ===== Servidor DNS (Apache + PHP) =====
  config.vm.define "dns" do |dns|
    dns.vm.hostname = "dns"
    dns.vm.network "private_network", ip: "192.168.56.10"
    dns.vm.network "forwarded_port", guest: 80, host: 8081, autocorrect: true
    dns.vm.network "forwarded_port", guest: 22, host: 2223, autocorrect: true

    dns.vm.synced_folder "./app", "/var/www/html",
      owner: "www-data",
      group: "www-data",
      mount_options: ["dmode=775", "fmode=664"]

    dns.vm.provider "virtualbox" do |vb|
      vb.name = "dns-jammy64-vm"
      vb.memory = 1024
      vb.cpus = 1
    end

    dns.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php
      systemctl enable apache2
      systemctl start apache2
      chown -R www-data:www-data /var/www/html
    SHELL
  end

  # ===== Cliente 1 =====
  config.vm.define "cliente" do |cliente|
    cliente.vm.hostname = "cliente"
    cliente.vm.provider "virtualbox" do |vb|
      vb.name = "cliente-vm"
      vb.memory = 1024
    end
  end

  # ===== Cliente 2 =====
  config.vm.define "cliente2" do |cliente2|
    cliente2.vm.hostname = "cliente2"
    cliente2.vm.provider "virtualbox" do |vb|
      vb.name = "cliente2-vm"
      vb.memory = 1024
    end
  end
end