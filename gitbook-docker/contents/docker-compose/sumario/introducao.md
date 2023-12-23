---
description: >-
  Ferramenta desenvolvida para ajudar a definir e compartilhar aplicativos com
  vários contêineres.
---

# Introdução

Com o compose, podemos criar um arquivo YAML para definir os serviços e com um único comando, pode rodar todos os contêineres ou para-los.

## O que é o Compose

Compose é uma ferramenta para definir e executar aplicações multi-containers em Docker. Com m simples comando é possível indicar todos os serviços através de sua configuração.

O Compose funciona em todos os ambientes: produção, homologação, desenvolvimento, teste e fluxos de integração contínua.

### Etapas de um Compose

Utilizar o Compose é um processo de três etapas:

1. Definir seu ambiente com um Dockerfile para que ele possa ser reproduzido em qualquer lugar.
2. Definir os serviços que serão executados em um arquivo `docker-compose.yml`
3. Executar o comando docker-compose up

### docker-compose.yml

O arquivo **docker-compose.yml** define a estrutura e como serão executados os containers.

É importante notar que as versões do arquivo docker-compose podem não ser compatíveis com determinados parâmetros e até mesmo versões diferentes da Docker Engine.

Podemos visualizar as referências do arquivo Compose diretamente na documentação oficial do Docker na seção [Compose File Reference](https://docs.docker.com/compose/compose-file)

O `docker-compose.yml` é apresentado da seguinte maneira:

```yml
version: '3'

volumes:
  logvolume01: {}

networks:
  app-net:

services:
  web:
    build: .
    ports:
      - 5000:5000
    volumes:
      - logvolume01:/var/log
    network:
      - app_net

  redis:
    image: redis
    network:
      - app_net
```

Obrigatoriamente o `docker-compose.yml` deve começar indicando qual a versão do compose a ser utilizada, em seguida podemos definir os serviços, volumes e redes não importando a ordem. Cada container a ser executado é uma nova seção do grupo **services**.

## Instalação

```bash
apt-get install -y docker-compose
```

### _**Criando um arquivo compose de exemplo**_

```bash
vi docker-compose.yml
```

```yml
version: '3.8'
services:
    mysqlsrv:
        image: mysql:5.7
        environment:
            MYSQL_ROOT_PASSWORD: "Senha123"
            MYSQL_DATABASE: "testedb"
        ports:
            - "3306:3306"
        volumes:
            - /data/mysql-C:/var/lib/mysql
        networks:
            - minha-rede
    
    adminer:
        image:  adminer
        ports:
            - 8080:8080
        networks:
            - minha-rede

networks:
    minha-rede:
        driver: bridge
```

```bash
docker-compose up -d
```

```bash
docker logs id-container
docker node ls
```

```bash
docker swarm join-token manager
docker service create --name web-server --replicas 3 -p 80:80 httpd
docker service ps web-server
docker node update --availability drain instancia
```

### Limpando a bagunça

**Apagando containeres que já morreram**

```conf
docker rm -v $(docker ps -a -q -f status=exited)
```

**Apagando imagens soltas**

```conf
docker rmi $(docker images -f dangling=true -q)
```

**Limapndo volumes esquecidos**

```bash
docker run -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker:/var/lib/docker -rm martin/docker-cleanup-volumes
```

### \[QUIS]

1. O que é Dockerfile?

```bash
Arquivo de texto que contém instruções para criar (build) uma imagem.
```
