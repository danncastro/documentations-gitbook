---
description: Começaremos alterando nosso Vagrantfile para as configurações mínimas.
---

# Instalando o MKE

```bash
$ vim Vagrantfile
```

```hcl
machines = {
   "master"   => {"memory" => "8096", "cpu" => "2", "ip" => "100", "image" => "ubuntu/bionic64"},
   "node01"   => {"memory" => "4096", "cpu" => "2", "ip" => "110", "image" => "ubuntu/bionic64"},
   "node02"   => {"memory" => "4096", "cpu" => "2", "ip" => "120", "image" => "centos/7"},
   "registry" => {"memory" => "4096", "cpu" => "2", "ip" => "200", "image" => "ubuntu/bionic64"}
}
```

Recrie as máquinas através do comando `vagrant up` e faça a instalação do Docker em todas as máquinas

Agora que temos nossas máquinas disponíveis vamos instalar o **MKE** na máquina master, para isto iremos acessar a máquina e executar o container do `mirantis/ucp` para verificar a ultima versão disponível

```bash
vagrant ssh master
docker container run --rm -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock mirantis/ucp --version
```

> Como se trata de uma solução de gerenciamento, precisamos que o `docker.sock` esteja exposto para o container.

```bash
docker run --rm -it \
        -v /var/run/docker.sock:/var/run/docker.sock \
        mirantis/ucp \
         version 3.4.6 (1b0bd58)
```

Agora que temos a versão atual do nosso **UCP** podemos executar a instalação do mesmo. Como o _UCP_ precisa de diversos outros containers, é uma boa prática ter todas as imagens já baixadas na máquina que será o manager, podemos fazer isto através do comando:

```bash
docker container run --rm -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock mirantis/ucp:3.4.6 images --pull missing
```

> O processo é um pouco demorado pelo tamanho e quantidade de imagens que serão baixadas, note também que estamos fixando a versão do UCP na execução, isto é altamente necessário.

Após a conclusão do processo podemos executar a instalação do UCP através do comando

```bash
$  docker container run --rm -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock mirantis/ucp:3.4.6 install --host-address 192.168.0.100 --interactive
```

Vamos agora responder algumas perguntas e aguardar a instalação:

```bash
Admin Username: admin
Admin password: dannielcastro@docker.exemple
Confirm Admin password: dannielcastro@docker.exemple
Additional Aliases:    <ENTER>
```

A instalação estará completa ao exibir a mensagem:

```bash
INFO[0121] All Installation Steps Completed             
```

### Acessando o Dashboard

Para acessar o dashboard vamos abrir o browser no endereço [https://master.docker-dca.example](https://master.docker-dca.example) e conectar com o usuário e senha que criamos

Após o login podemos enviar uma licença, requisitar uma licença de teste ou pular. Vamos clicar em `Skip For Now`

{% hint style="info" %}
Para solicitar uma licença de teste é preciso entrar em contato com a Mirantis através do website
{% endhint %}

{% embed url="https://www.mirantis.com/contact/" %}

Será exibido o dashboard do **MKE**, e agora precisamos entender o funcionamento do mesmo.

No dashboard temos um menu a esquerda onde temos as opções:

* **admin** - Configurações de perfil, administrador, sobre e support bundle
* **Dashboard** - Tela inicial com informações do Swarm, Kubernetes e recursos.
* **Access Control** - Controle de acesso para Organizações e Times, Usuários, Regras e Concessões
* **Shared Resources** - Collections, Stacks, Containers, Imagens, Nós
* **Kubernetes** - Todos os recursos e configurações do Kubernetes
* **Swarm** - Todos os recursos e configurações do Swarm.
