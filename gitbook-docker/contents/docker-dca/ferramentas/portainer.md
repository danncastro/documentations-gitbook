---
description: >-
  O Portainer é uma ferramenta de container-as-a-service a qual fornece um
  dashboard e diversas funções de administração do Docker e do Docker Swarm.
---

# Portainer

{% embed url="https://www.portainer.io/" %}
portainer.io
{% endembed %}

### Instalação

Iremos instalar o portainer em nosso cluster local, para isto acesse a máquina manager e execute os seguintes comandos:

```bash
curl -fsSL https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
docker stack deploy -c portainer-agent-stack.yml portainer
```

Acesse a interface do portainer no endereço [http://master.docker-dca.example:9000](http://master.docker-dca.example:9000)

Crie o usuário admin com a senha `dannielcastro@docker.exemple`

### Dashboard

No dashboard do Portainer, temos acesso a todas as informações do nosso cluster e os endpoints.

Clique no endpoint `primary` para visualizar mais configurações do node

### App Templates

Em App templates temos acesso a uma biblioteca de stacks e containers para efetuarmos deploys.

Podemos também criar custom templates com arquivos de stack para que seja fácil o deploy de novas aplicações.

Vamos efetuar o deploy de uma stack WordPress, para isto clique em `Wordpress`

De um nome a stack e coloque a senha para o banco de dados como `dannielcastro@docker.exemple` e clique em `Deploy the stack`

### Stacks

Em stacks conseguimos visualizar todas as stacks criadas e gerenciar as stacks que foram criadas pelo portainer.

Ao clicar em uma stack, por exemplo a `Wordpress`, teremos acesso a informações dos services e também a possibilidade de editar o deploy, fazer scale e editar as portas publicadas.

### Services

Em services temos acesso a todos os services bem como a possibilidade de atualizar e editar os serviços

### Containers

Em containers, temos acesso a todos os containers bem como a informação de onde ele está sendo executado

### Images

Em images podemos visualizar todas as imagens, efetuar o pullm de imagens diretamente do [Dockerhub](https://hub.docker.com/) e até construir novas imagens.

### Networks

Em Networks, podemos visualizar, criar e remover as redes do nosso cluster.

### Volumes

Em volumes, podemos visualizar, criar e remover os volumes do nosso cluster

### Configs e Secrets

Em configs e secrets podemos visualizar, criar e remover informações sensíveis (secrets) ou configurações não sensíveis (configs) em nosso cluster

### Swarm

Em Swarm podemos ver o status do nosso cluster e as configurações dos nossos nodes.

Clicando em `Go to cluster visualizer` temos uma exibição visual dos containers que estão sendo executados em cada um dos nodes

### Settings

Em settings podemos criar usuários, roles e teams, configurar endpoints e registries, alterar configurações de autenticação e visualizar informações sobre o portainer

> Algumas configurações como `roles` e `oAuth` só estão disponíveis na versão `Business Edition` que é a versão paga da plataforma.
