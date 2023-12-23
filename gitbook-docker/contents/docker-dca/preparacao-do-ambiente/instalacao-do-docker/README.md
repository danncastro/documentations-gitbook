---
description: Existem duas maneiras de instalar o Docker
---

# Instalação do Docker

* Script de Conveniência
* Maneira Tradicional

Iremos efetuar a instalação da maneira tradicional nas máquinas `master` e `node02` e com o script de conveniência nas máquinas `node01` e `registry`.

### Instalando Docker no Ubuntu

Primeiramente vamos acessar a máquina `master`

```bash
cd ~/docker
vagrant ssh master
```

Uma vez conectado na máquina docker, execute os seguintes comandos:

```bash
sudo apt update && sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common \
    bash-completion -y
```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

```bash
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

```bash
sudo apt update && sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Após a conclusão da instalação, podemos configurar agora nosso usuário para fazer parte do grupo `docker`, isso garantirá que possamos executar os comandos do docker sem a necessidade de elevar os privilégios.

```bash
sudo usermod -aG docker $USER
```

Vamos também instalar o recurso de `Bash Completion` através do comando:

```bash
sudo curl https://raw.githubusercontent.com/docker/machine/v0.16.0/contrib/completion/bash/docker-machine.bash -o /etc/bash_completion.d/docker-machine
```

Saia do terminal e inicie uma nova sessão e o usuário já poderá executar o comando como super user.

```bash
exit
vagrant ssh master
```

### Instalando Docker no CentOS

Abra um novo terminal e acesse a máquina `node02`

```bash
cd ~/docker
vagrant ssh node02
```

Uma vez conectado na máquina docker, execute os seguintes comandos:

```bash
sudo yum install -y yum-utils curl vim bash-completion
```

```bash
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

```bash
sudo yum install docker-ce docker-ce-cli containerd.io
```

Nos sistemas RHEL, precisamos habilitar e iniciar o serviço após a instalação do mesmo

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Após a conclusão da instalação, podemos configurar agora nosso usuário para fazer parte do grupo `docker`, isso garantirá que possamos executar os comandos do docker sem a necessidade de elevar os privilégios.

```bash
sudo usermod -aG docker $USER
```

Vamos também instalar o recurso de `Bash Completion` através do comando:

```bash
sudo curl https://raw.githubusercontent.com/docker/machine/v0.16.0/contrib/completion/bash/docker-machine.bash -o /etc/bash_completion.d/docker-machine
```

Saia do terminal e inicie uma nova sessão e o usuário já poderá executar o comando como super user.

```bash
exit
vagrant ssh node02
```

### Instalando Docker através do script de Conveniência.

Os passos a seguir devem ser executados nas máquinas `node01` e `registry`, não esqueça de abrir um terminal novo para cada máquina e executar o comando `vagrant ssh <host>`

A Docker disponibiliza um script de conveniência, que trata-se de uma maneira simples e rápida para instalar o Docker para ambientes de desenvolvimento, este script faz a validação da distribuição Linux bem como instala os pacotes necessários para o funcionamento do Docker.

Para instalar o Docker através do script de conveniência basta executar o comando:

```bash
sudo curl -fsSL https://get.docker.com | bash
```

Nos sistemas **RHEL**, precisamos habilitar e iniciar o serviço após a instalação do mesmo

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Após a conclusão da instalação, podemos configurar agora nosso usuário para fazer parte do grupo `docker`, isso garantirá que possamos executar os comandos do docker sem a necessidade de elevar os privilégios.

```bash
sudo usermod -aG docker $USER
```

Vamos também instalar o recurso de `Bash Completion` através do comando:

```bash
sudo curl https://raw.githubusercontent.com/docker/machine/v0.16.0/contrib/completion/bash/docker-machine.bash -o /etc/bash_completion.d/docker-machine
```

### Teste de Execução

Para garantirmos que o docker foi instalado corretamente e está funcional, podemos rodar nosso primeiro container e verificar o retorno na tela.

```bash
$ docker container run --rm -it hello-world
```

### Docker Compose

O Docker Compose não é instalado por padrão no Linux, então você deve instalá-lo por fora. Para tal, baixe-o na sua versão mais atual, que pode ser visualizada no seu GitHub, executando o comando abaixo:

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

Após isso, dê permissão de execução para o docker-compose:

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

Pronto, o Docker Compose já está instalado no seu Linux!
