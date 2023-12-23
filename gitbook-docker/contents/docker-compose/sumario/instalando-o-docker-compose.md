---
description: Para instalar o Docker Compose basta executar os comandos da documentação
---

# Instalando o Docker Compose

{% embed url="https://docs.docker.com/compose/install/" %}
Documentação Official do Docker
{% endembed %}

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## Criando Composes

Crie um diretório para armazenarmos nosso compose

```bash
mkdir ~/compose
cd ~/compose
```

Copie o Dockerfile do nosso webserver para a pasta compose e crie o arquivo docker-compose.yml

```bash
cp ~/dockerfiles/webserver/Dockerfile .
vim docker-compose.yml
```

```yml
version: '3'

services:
  webserver:
    build: .
    hostname: webserver
    ports:
      - 80:80
    restart: always
```

### **Parâmetros do docker-compose.yml**

* **version** - define a versão do arquivo compose
* **services** - define a seção de serviços
* **build** - define o local do Dockerfile
* **hostname** - define o hostname do container
* **ports** - define quais portas serão publicadas
* **restart** - define a politica de restart

#### Execute a criação do ambiente

```bash
docker-compose up -d
```

_O comando docker-compose up com o parâmetro **-d** inicia o compose de forma **Detached**_

> **IMPORTANTE:** por padrão o comando docker-compose procura por um arquivo docker-compose.yml no diretório em que o comando foi executado, é possível informar o arquivo compose a ser utilizado através da opção **-f \<caminho\_do\_compose>**

Podemos acessar a aplicação no navegador pelo endereço http://node01.docker-dca.example

Para listar os containers criados pelo compose podemos utilizar o parâmetro **ps**

```bash
docker-compose ps
```

Caso seja necessário parar o container criado com o compose podemos utilizar o parâmetro **stop**

```bash
docker-compose stop
docker-compose ps
```

Para iniciar novamente o compose podemos utilizar o parâmetro **start**

```bash
docker-compose start
docker-compose ps
```

Para destruir o ambiente criado através do docker-compose utilizamos o parâmetro **down**

```bash
docker-compose down
docker-compose ps
```

Vamos agora criar uma página web para ser exibida em nosso container através do docker-compose, crie a pasta html e o arquivo index.html

```bash
mkdir /html
echo "<h1> Curso Docker DCA 100% Gratuito - youtube.com/caiodelgadonew </h1>" > html/index.html
```

#### Edite o compose e adicione o mapeamento de volume:

```bash
vim docker-compose.yml
```

```yml
version: '3'

services:
  webserver:
    build: .
    hostname: webserver
    ports:
      - 80:80
    restart: always
    volumes:
      - $PWD/html:/var/www/html
```

_O parâmetro **$PWD** referencia o Parent Working Directory (diretório atual)_

Através deste mapeamento de volumes estamos substituindo o conteúdo do caminho /var/www/html por /root/compose/

Execute a criação do ambiente

```bash
docker-compose up -d
```

Acesse a aplicação no navegador pelo endereço http://node01.docker-dca.example

Para destrua o ambiente criado

```bash
docker-compose down
```

### Compose Multi-containers

A principal funcionalidade de um compose é subir serviços multi-containers como, por exemplo, um serviço de wordpressm onde será executado um container para a aplicação e um segundo container para o banco de dados. Isto é possível com a definição de múltiplos segmentos dentro de um docker-compose

Crie o arquivo wordpress-compose.yml

```bash
vim wordpress-compose.yml
```

```yml
version: '3'

volumes:
  mysql_db:

services:
  wordpress:
    image: wordpress
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: caiodelgadonew@youtube
      WORDPRESS_DB_NAME: wordpress
  db:
    image: mysql:5.7
    restart: always
    volumes:
      - mysql_db:/var/lib/mysql
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: caiodelgadonew@youtube
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
```

#### Execute a criação do ambiente

```bash
docker-compose -f wordpress-compose.yml up -d
docker-compose -f wordpress-compose.yml ps
```

_O parâmetro **-f** especifica o arquivo **.yml** a ser utilizado com o compose_

Acesse o endereço http://node01.docker-dca.example e configure o wordpress

```bash
Titulo do Site: Wordpress - caiodelgadonew
Nome de Usuário: caiodelgadonew
Senha: caiodelgadonew@youtube
email: caiodelgadonew@docker-dca.example
```

Vamos alterar o tema do Wordpress, para isto clique em **Aparência** e em seguida em **Temas**

Selecione um tema e clique em **Ativar**

Para visualizar o tema clique em **Wordpress - caiodelgadonew**

Podemos verificar os logs do compose

```bash
docker-compose -f wordpress-compose.yml logs
```

Para visualizar os logs em tempo real podemos utilizar o parâmetro **-f**

```bash
docker-compose -f wordpress-compose.yml logs -f
```

Pressione **\<CTRL> + \<C>** para sair

Destrua o ambiente

```bash
docker-compose -f wordpress-compose.yml down
```

_Note que ao destruir o compose os volumes continuam persistentes no disco, sendo possível recriar a aplicação com o mesmo conteúdo_

Tente acessar o website e verifique que o mesmo está indisponível

Execute a criação do ambiente novamente

```bash
docker-compose -f wordpress-compose.yml up -d
```

Acesse o endereço http://node01.docker-dca.example/ e verifique que os dados persistiram na aplicação

Destrua o ambiente e remova o volume criado

```bash
docker-compose -f wordpress-compose.yml down
docker volume rm compose_mysql_db
```
