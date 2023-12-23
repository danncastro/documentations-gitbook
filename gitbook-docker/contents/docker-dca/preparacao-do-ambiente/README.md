---
description: >-
  Iremos instalar o Docker em máquinas virtuais, para isto utilizaremos uma
  solução chamada Vagrant somado ao Virtualbox, você pode utilizar a solução de
  virtualização que preferir
---

# Preparação do ambiente

{% hint style="warning" %}
Lembre-se de habilitar a virtualização Intel VT-x ou AMD SVM na UEFI/BIOS.
{% endhint %}

### Instalando o Vagrant e Virtualbox

**Para instalar o Virtualbox siga os passos:**

1. Acesse a página de [Downloads do Virtualbox](https://www.virtualbox.org/wiki/Downloads) e faça o download da versão correspondente ao seu sistema operacional.
2. Execute a instalação do pacote do Virtualbox.&#x20;
   1. Para Linux execute o programa de instalação de pacotes `sudo dpkg -i <pacote>.deb` para sistemas **Debian** ou `sudo rpm -i <pacote>.rpm`) para sistemas **RedHat**
   2. Para Windows, clique sob o instalador e avance até o final da instalação.&#x20;
   3. Para MacOS, clique sob o instalador e avance até o final da instalação.

**Para Instalar o vagrant siga os passos:**

1. Acesse a página de [Downloads do Vagrant](https://www.vagrantup.com/downloads.html) e faça o download da versão correspondente ao seu sistema operacional.
2. Execute a instalação do pacote do vagrant.&#x20;
   1. Para Linux execute o programa de instalação de pacotes (`sudo dpkg -i <pacote>.deb` para sistemas **Debian** ou `sudo rpm -i <pacote>.rpm`) para sistemas **RedHat**
   2. Para Windows, clique sob o instalador e avance até o final da instalação.
   3. Para MacOS, clique sob o instalador e avance até o final da instalação.
3. Após a instalação abra um terminal ou um prompt de comando e execute o comando `vagrant --version` para verificar se o pacote foi instalado com sucesso.

### Preparando o Ambiente

Após instalar o **Vagrant** e o **Virtualbox**, podemos criar um diretório com um arquivo **Vagrantfile.**

> O Vagrantfile é o arquivo do **Vagrant** responsável por criar nossa infraestrutura.

{% embed url="https://github.com/caiodelgadonew/docker-dca" %}
Caso queira utilizar os arquivos mais atualizados, basta clonar o repositório:&#x20;
{% endembed %}

```bash
mkdir ~/docker 
cd docker
vim Vagrantfile
```

Adicione o conteúdo ao arquivo Vagrantfile

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby  :

machines = {
  "master"   => {"memory" => "2048", "cpu" => "2", "ip" => "100", "image" => "ubuntu/bionic64"},
  "node01"   => {"memory" => "1024", "cpu" => "2", "ip" => "121", "image" => "ubuntu/bionic64"},
  "node02"   => {"memory" => "1024", "cpu" => "2", "ip" => "122", "image" => "centos/7"},
  "registry" => {"memory" => "2048", "cpu" => "2", "ip" => "150", "image" => "ubuntu/bionic64"}
}

Vagrant.configure("2") do |config|

  machines.each do |name, conf|
    config.vm.define "#{name}" do |machine|
      machine.vm.box = "#{conf["image"]}"
      machine.vm.hostname = "#{name}.docker-dca.example"
      machine.vm.network "private_network", ip: "192.168.0.#{conf["ip"]}"
      machine.vm.provider "virtualbox" do |vb|
        vb.name = "#{name}"
        vb.memory = conf["memory"]
        vb.cpus = conf["cpu"]
        vb.customize ["modifyvm", :id, "--groups", "/Docker-DCA"]
      end
      machine.vm.provision "shell", inline: "hostnamectl set-hostname #{name}.docker-dca.example"
      config.vm.provision "shell", inline: <<-SHELL
        HOSTS=$(head -n7 /etc/hosts)
        echo -e "$HOSTS" > /etc/hosts
        echo '192.168.0.100  master.docker.example' >> /etc/hosts
        echo '192.168.0.121  node01.docker.example' >> /etc/hosts
        echo '192.168.0.122  node02.docker.example' >> /etc/hosts
        echo '192.168.0.150  registry.docker.example' >> /etc/hosts
        SHELL
    end
  end
end
```

Para criar o ambiente do laboratório, execute o comando `vagrant up`, e o vagrant irá criar todas as máquinas virtuais bem como configurar os hostnames e endereços IPs&#x20;

Para se conectar as máquinas utilize o comando `vagrant ssh <host>` informando o nome do host a ser conectado, lembre-se de estar dentro da pasta com o Vagrantfile.&#x20;

Para desligar as máquinas execute o comando `vagrant halt`. Para destruir o ambiente execute o comando `vagrant destroy`.

Execute o comando `vagrant up` para criar nossa infraestrutura.

```bash
cd ~/docker
vagrant up
```

> Caso você queira saber mais sobre Vagrant para subir os laboratórios de estudo, basta clicar em [Vagrant-101](https://caiodelgado.dev/vagrant-101) e acessar o blog.

Adicione também as seguintes entradas ao arquivo `hosts` da sua máquina.

```bash
# Docker
192.168.200.10  master.docker.example
192.168.200.21  node01.docker.example
192.168.200.22  node02.docker.example
192.168.200.50  registry.docker.example
```

> Em máquinas **Linux** e **MacOS** o arquivo fica localizado em `/etc/hosts` Em máquinas **Windows** o arquivo fica localizado em `C:\Windows\System32\drivers\etc\hosts`
