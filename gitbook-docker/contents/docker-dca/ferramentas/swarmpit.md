---
description: >-
  O Swarmpit.io é uma ferramenta de dashboard focada em Docker Swarm, simples e
  intuitivo de se utilizar e simples de instalar.
---

# Swarmpit

{% embed url="https://swarmpit.io/" %}
swarmpit.io
{% endembed %}

### Instalação

Podemos executar o Swarmpit através do comando:

```bash
docker run -it --rm \
  --name swarmpit-installer \
  --volume /var/run/docker.sock:/var/run/docker.sock \
swarmpit/install:1.9
```

Mas iremos executar o Swarmpit através do deploy de uma stack, dessa forma os serviços serão distribuídos da maneira correta entre os nós.

No [play with docker](https://www.docker.com/play-with-docker/), execute o seguinte comando em um nó manager.

```bash
curl -fsSL https://raw.githubusercontent.com/swarmpit/swarmpit/master/docker-compose.yml -o swarmpit.yml
```

```bash
docker stack deploy -c swarmpit.yml swarmpit
docker stack services swarmpit
docker service logs -f swarmpit_app
```

> O service `swarmpit_app` demora alguns minutos para ser executado.

Após o service estar funcional, clique na porta 888 para acessar o dashboard do Swarmpit e crie o primeiro usuário `admin` com a senha `dannielcastro@docker.exemple` e clique em `CREATE ADMIN`

Após a criação do Usuário admin você conseguirá visualizar o dashboard com todas as informações do nosso cluster bem como um overview dos recursos.

### Dashboard

No dashboard do Swarmpit temos informações dos nós em execução bem como a utilização dos recursos

### Registries

Nos registries podemos conectar a registries como:

* Dockerhub
* Registry v2
* Amazon ECR
* Azure ACR
* Gitlab registry

vamos fazer um deploy de um registry v2 rapidamente em nosso PWD através do comando na máquina manager1:

```bash
docker container run -dit --name registry -p 5000:5000 registry:2
```

1. Selecione `Registry v2` na lista de registries e clique em `Next`
2. Preencha com o endereço `http://<Endereço IP>:5000` e clique em `Next`

> Substitua o IP do manager

3. Marque `Make account Public` e clique em `Finish`

### Stacks

Em Stacks podemos visualizar todas as stacks em execução em nosso cluster, podemos também clicar em `New Stack` para fazer o deploy de uma nova stack

De um nome a stack e insira o conteúdo da stack, como por exemplo o `supermario` e clique em `deploy`

```bash
version: "3.8"

services:

  supermario:
    image: danncastro/docker-supermario
    ports:
     - 8600:8080
    deploy:
      mode: replicated
      replicas: 3
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "1"
          memory: 100M
        reservations:
          cpus: "0.5"
          memory: 60M
```

Podemos editar nosso service `on-the-fly` clicando em `edit`

Vamos alterar a linha `replicas: 3` para `replicas: 8` e clicar em deploy

Podemos verificar que nosso service será atualizado, clique no service para visualizar os status do serviço

### Services

Na visão de services, temos todas as informações relacionadas a um serviço, bem como a possibilidade de criar um novo serviço, editar e visualizar os logs

### Tasks

Na visão de tasks, podemos ter acesso a todas as tasks bem como qual nó esta sendo executada a task, uso de cpu e memória.

### Networks

Na parte de Networks podemos visualizar e criar novas redes

### Nodes

Em nodes podemos visualizar os recursos e informações de cada nó do nosso cluster

### Volumes

Em volumes podemos visualizar e criar novos volumes

### Secrets

Em secrets podemos visualizar e criar novos secrets

### Config

Em config podemos armazenar informações não confidenciais como arquivos de configuração.
