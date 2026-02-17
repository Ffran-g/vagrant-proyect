Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  # Ruta personalizada para las máquinas VirtualBox
  ruta_personalizada = "D:/FranGarciaEgea/Despliege/MV/Maquinas_Vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["setproperty", "machinefolder", ruta_personalizada]
  end

  # ====Jupiter - DNS Maestro + Router ====
  config.vm.define "jupiter" do |jupiter|
    jupiter.vm.hostname = "jupiter"
    jupiter.vm.network "private_network", ip: "10.0.0.1"

    jupiter.vm.provider "virtualbox" do |vb|
      vb.name = "jupiter-vm"
      vb.memory = 1024
      vb.cpus = 1
    end

    jupiter.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y bind9 bind9utils bind9-doc

      # Habilitar reenvío IP (router)
      echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
      sysctl -p

      systemctl enable bind9
      systemctl start bind9
    SHELL
  end

  # ==== Marte - Servidor Web + SSH + FTP ====
  config.vm.define "marte" do |marte|
    marte.vm.hostname = "marte"
    marte.vm.network "private_network", ip: "10.0.0.2"
    marte.vm.network "forwarded_port", guest: 80, host: 8080, autocorrect: true
    marte.vm.network "forwarded_port", guest: 22, host: 2222, autocorrect: true
    marte.vm.network "forwarded_port", guest: 21, host: 2121, autocorrect: true

    marte.vm.synced_folder "./app", "/var/www/html",
      owner: "www-data",
      group: "www-data",
      mount_options: ["dmode=775", "fmode=664"]

    marte.vm.provider "virtualbox" do |vb|
      vb.name = "marte-vm"
      vb.memory = 1024
      vb.cpus = 1
    end

    marte.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php vsftpd

      systemctl enable apache2
      systemctl start apache2

      systemctl enable vsftpd
      systemctl start vsftpd

      chown -R www-data:www-data /var/www/html
    SHELL
  end

  # ==== Cliente ====
  config.vm.define "cliente" do |cliente|
    cliente.vm.hostname = "cliente"
    cliente.vm.network "private_network", ip: "10.0.0.3"

    cliente.vm.provider "virtualbox" do |vb|
      vb.name = "cliente-vm"
      vb.memory = 1024
      vb.cpus = 1
    end

    cliente.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y dnsutils curl ftp openssh-client
    SHELL
  end

end
