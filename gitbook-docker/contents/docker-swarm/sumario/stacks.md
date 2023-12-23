---
description: >-
  Quando executamos o Docker em modo swarm, podemos utilizar o comando docker
  stack deploy para fazer o deploy de uma aplicação completa no swarm.
---

# Stacks

Uma stack é uma pilha de serviços trabalhando em conjunto.

O comando `deploy` aceita um descritivo de stack no formato de um arquivo compose.

> Para trabalharmos com stacks, precisamos utilizar o arquivo compose com sua versão 3 ou superior.

Para entender o funcionamento do stack, iremos efetuar o deploy do webserver que anteriormente subimos com o docker-compose.

Vamos começar com o básico.

Primeiramente crie uma pasta para armazenarmos arquivos do stack e o arquivo `webserver.yml` onde iremos definir nosso compose

```bash
mkdir -p ~/stack
cd ~/stack
```

```yml
version: '3.9'

services:
  webserver:
    image: registry.docker-dca.example:5000/nginx
    hostname: webserver
    ports:
      - 80:80
```

Para fazer o deploy podemos utilizar a opção `--compose-file` ou `-c`

```bash
docker stack deploy --compose-file webserver.yml nginx-webserver
docker stack ls 
```

Verifique os status do stack através do comando `docker stack services`

```bash
docker stack services nginx-webserver
```

Veja que o serviço foi iniciado com o mode `replicated` com uma replica apenas.

Podemos ver os status das tasks do service através do comando `docker stack ps`

```bash
docker stack ps nginx-webserver
```

Agora que sabemos onde nossa task está sendo executada, podemos acessar a mesma através do endereço do servidor .

http://master.docker-dca.example

vamos alterar nosso serviço agora para executar 2 replicas adicionando opções de deploy a mesma.

```bash
vim webserver.yml
```

```yml
version: '3.9'

services:
  webserver:
    image: registry.docker-dca.example:5000/nginx
    hostname: webserver
    ports:
      - 80:80
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
```

Atualize a stack ae verifique os status:

```bash
docker stack deploy -c webserver.yml nginx-webserver
docker stack services nginx-webserver
docker stack ps nginx-webserver
```

Podemos ver que nossa stack está sendo executada agora nos nós node01 e node02.

Vamos explorar agora o deploy do tipo global, para isto vamos alterar nosso arquivo compose

```bash
vim webserver.yml
```

```yml
version: '3.9'

services:
  webserver:
    image: registry.docker-dca.example:5000/nginx
    hostname: webserver
    ports:
      - 80:80
    deploy:
      mode: global
      restart_policy:
        condition: on-failure
```

Para alterar nosso serviço para global, precisamos primeiramente destruir o mesmo e subi-lo novamente.

```bash
docker stack rm nginx-webserver
docker stack deploy -c webserver.yml nginx-webserver
docker stack services nginx-webserver
docker stack ps nginx-webserver
```

Note que agora ao utilizar o mode como global, temos nossa task executando exatamente 1 container em cada servidor, este tipo de utilização é muito util quando precisamos garantir que todos os containers possuam por exemplo um determinado container de monitoramento.

Vamos remover nosso serviço e alterar alguns outros parâmetros:

```bash
docker stack rm nginx-webserver
vim webserver.yml
```

```yml
version: '3.9'

services:
  webserver:
    image: registry.docker-dca.example:5000/nginx
    hostname: webserver
    deploy:
      mode: replicated
      replicas: 4
      placement:
        constraints:
          - node.role==manager 
      restart_policy:
        condition: on-failure
```

Utilizamos as placements constraints para indicar as preferencias de onde queremos executar nossos containers, desta maneira podemos atrelar eles para serem executados apenas em nós do tipo manager, ou até mesmo em nós que estão executando uma determinada versão de sistema operacional

```bash
docker stack deploy -c webserver.yml nginx-webserver
docker stack services nginx-webserver
docker stack ps nginx-webserver
```

Vamos alterar agora para executarmos 5 replicas apenas nos nós que sejam workers e estejam rodando o ubuntu 18.04

Para isto iremos adicionar uma label no node01

```bash
docker node update --label-add os=ubuntu18.04 node01.docker-dca.example
vim webserver.yml
```

```yml
version: '3.9'

services:
  webserver:
    image: registry.docker-dca.example:5000/nginx
    hostname: webserver
    deploy:
      mode: replicated
      replicas: 4
      placement:
        constraints:
          - node.role==worker
          - node.labels.os==ubuntu18.04 
      restart_policy:
        condition: on-failure
```

```bash
docker stack deploy -c webserver.yml nginx-webserver
docker stack services nginx-webserver
docker stack ps nginx-webserver
```

Existem diversas outras propriedades que podemos utilizar com o compose, podemos verificar todas no [Docker Compose Reference File - Deploy](https://docs.docker.com/compose/compose-file/compose-file-v3/#deploy)

Vamos agora derrubar nosso webserver e criar um compose para subir o wordpress.

<pre class="language-bash"><code class="lang-bash"><strong>docker stack rm webserver
</strong>vim wordpress.yml
</code></pre>

```yml
version: "3.9"

volumes:
  mysql_db:
    driver: trajano/nfs-volume-plugin
    driver_opts:
      device: master.docker-dca.example:/home/vagrant/mysql_db
      nfs_opts: hard,proto=tcp,nfsvers=3,intr,nolock 


networks:
  wp_overlay:


services:
  wordpress:
    image: registry.docker-dca.example:5000/wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: caiodelgadonew@youtube
      WORDPRESS_DB_NAME: wordpress
    networks:
      - wp_overlay
    deploy:
      mode: replicated
      replicas: 2
      restart_policy:
        condition: on-failure

  db:
    image: registry.docker-dca.example:5000/mysql:5.7
    volumes:
      - mysql_db:/var/lib/mysql
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: caiodelgadonew@youtube
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    networks:
       - wp_overlay
    deploy:
      mode: replicated
      replicas: 1
      restart_policy:
        condition: on-failure
```

Antes de executar os containers, vamos criar o volume\_nfs na máquina master

```bash
$ mkdir ~/mysql_db
$ echo "/home/vagrant/mysql_db/ 10.20.20.0/24(no_root_squash,rw)" | sudo tee -a /etc/exports 
$ sudo systemctl restart nfs-server
```

Vamos fazer o deploy da stack:

```bash
$ docker stack deploy -c wordpress.yml wordpress-stack
$ docker stack services wordpress-stack
$ docker stack ps wordpress-stack
```

Acesse o navegador e configure a webpage

> Note que podemos acessar o site de qualquer endereço do cluster, uma vez que temos um vip configurado.

http://master.docker-dca.example:8080/

```bash
Titulo do Site: Wordpress - Stack
Nome de Usuário: caiodelgadonew
Senha: caiodelgadonew@youtube
email: caiodelgadonew@docker-dca.example
```

## Gerenciando Limites do container

Podemos também gerenciar um limite/reserva de recursos para o container através do parâmetro `resources` no compose.

```bash
$ vim wordpress.yml
```

```yml
version: "3.9"

volumes:
  mysql_db:
    driver: trajano/nfs-volume-plugin
    driver_opts:
      device: master.docker-dca.example:/home/vagrant/mysql_db
      nfs_opts: hard,proto=tcp,nfsvers=3,intr,nolock

networks:
  wp_overlay:


services:
  wordpress:
    image: registry.docker-dca.example:5000/wordpress
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: caiodelgadonew@youtube
      WORDPRESS_DB_NAME: wordpress
    networks:
      - wp_overlay
    deploy:
      mode: replicated
      replicas: 2
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "1"
          memory: 60M
        reservations:
          cpus: "0.5"
          memory: 30M

  db:
    image: registry.docker-dca.example:5000/mysql:5.7
    volumes:
      - mysql_db:/var/lib/mysql
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: caiodelgadonew@youtube
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    networks:
       - wp_overlay
    deploy:
      mode: replicated
      replicas: 1 
      restart_policy:
        condition: on-failure
```

Vamos fazer o deploy da stack:

```bash
$ docker stack deploy -c wordpress.yml wordpress-stack
$ docker stack services wordpress-stack
$ docker stack ps wordpress-stack
$ docker service inspect wordpress-stack_wordpress --pretty
```

Para verificar o uso de memoria/cpu do container podemos utilizar o comando `docker stats`

> Execute em algum nó que esteja rodando o wordpress

```bash
$ docker stats
```

Vamos instalar na máquina master o apache benchmark para fazer um stress test no container.

```bash
$ sudo apt-get install apache2-utils -y
```

Execute o apache benchmark e acompanhe o uso de cpu/memoria do container nos node01 e 02

```bash
$ ab -n 10000 -c 100 http://master.docker-dca.example:8080/
```

Veja que o container não passa do limite de memória que especificamos. Vamos alterar o numero de replicas para 6 e executar novamente o apache benchmark

```bash
$ docker service scale wordpress-stack_wordpress=6
$ ab -n 10000 -c 100 http://master.docker-dca.example:8080/
```

Remova a stack

```bash
$ docker stack rm wordpress-stack
```

## Traefik com Stack (Extra)

Para finalizar, vamos fazer um deploy de uma stack do jogo "dockersupermario" com o traefik fazendo proxy reverso.

Primeiramente vamos criar a rede para o traefik.

```bash
$ docker network create -d overlay proxy
```

Vamos criar os arquivos compose para o container do supermario.

```bash
$ vim supermario.yml
```

```yml
version: "3.9"

networks:
  proxy:
    external: true

services:

  supermario:
    image: registry.docker-dca.example:5000/caiodelgadonew/docker-supermario
    networks:
      - proxy
    deploy:
      mode: replicated
      replicas: 1
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "1"
          memory: 100M
        reservations:
          cpus: "0.5"
          memory: 60M
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.game.rule=Host(`supermario.docker-dca.example`)"
        - "traefik.http.services.game.loadbalancer.server.port=8080"
```

Vamos criar agora a stack do Traefik

```bash
$ vim traefik.yml
```

```yml
version: '3.9'

networks:
  proxy:
    external: true

services:
  traefik:
    image: "registry.docker-dca.example:5000/traefik:v2.4"
    command:
      - --entrypoints.web.address=:80
      - --providers.docker.swarmMode=true
      - --providers.docker.exposedByDefault=false
      - --api
    ports:
      - 80:80
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - proxy
    deploy:
      mode: global
      placement:
        constraints:
          - node.role==manager
      restart_policy:
        condition: on-failure
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.traefik.rule=Host(`dashboard.docker-dca.example`)"
        - "traefik.http.routers.traefik.service=api@internal"
        - "traefik.http.services.traefik.loadbalancer.server.port=80"
```

Efetue o deploy das stacks

```bash
$ docker stack deploy -c supermario.yml supermario
$ docker stack deploy -c traefik.yml traefik
$ docker stack ls
$ docker stack services supermario
$ docker stack services traefik
```

Adicione a entrada dns em seu arquivo `/etc/hosts`

```bash
$ vim /etc/hosts
```

```bash
10.20.20.100    master.docker-dca.example supermario.docker-dca.example dashboard.docker-dca.example
```

Podemos também escalar nosso serviço

```bash
$ docker service scale supermario_supermario=6
$ docker stack services supermario
$ docker stack ps supermario 
```

No dashboard do traefik conseguimos ver a quantidade de nós e as informações do serviço

{% embed url="http://dashboard.docker-dca.example/" %}

Após o termino, remova as stacks

```bash
$ docker stack rm traefik
$ docker stack rm supermario
```

### \[QUIS]

1. Quantos nodes (servidores) são necessários para iniciar um cluster de swarm? \[x]&#x20;

```
1
```
