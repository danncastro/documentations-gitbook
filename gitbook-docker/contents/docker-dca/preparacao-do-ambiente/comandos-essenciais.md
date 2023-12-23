---
description: Iremos agora aprender alguns comandos essenciais do Docker
---

# Comandos Essenciais

```bash
docker --help
```

O primeiro passo para entendermos os comandos do docker é visualizar sua lista de comandos, iremos falar dos seguintes comandos de gerenciamento:

* `docker container`
* `docker image`
* `docker network`
* `docker system`
* `docker volume`

Para cada comando de gerenciamento acima, temos diversos subcomandos a serem executados, muitos deles são parecidos com comandos Linux como por exemplo `ls`, `rm`, dentre outros.

Antigamente o comando utilizado para listar containers era o comando `docker ps` que ainda existe na documentação, porém é indicado que seja utilizado o novo comando `docker container ls`.

> PS é o abreviamento de Process Status enquanto LS é o abreviamento de LIST

### Executando comandos

Antes de executar os comandos do docker, vamos conectar na máquina `node01`.

```bash
vagrant ssh node01
```

Para visualizar informações do ambiente, podemos utilizar o comando `docker system info` o qual exibirá informações do Docker como versão, quantidade de containers em execução, storage drivers, entre outros.

```bash
docker system info
docker info
```

> _Os comandos listados acima são equivalentes._

Para listar `containers, imagens, redes e volumes` no docker, utilizamos o comando `docker <comando> ls`

```bash
docker container ls
docker image ls
docker network ls
docker volume ls
```

* **docker container ls** - Lista os containers
* **docker image ls** - Lista as imagens
* **docker network ls** - Lista as redes
* **docker volume ls** - Lista os volumes

Para pesquisar por uma imagem, utilizamos o comando `docker search`

```bash
docker search debian
```

Para efetuar o download da imagem utilizamos o comando `docker image pull`

```bash
docker image pull debian
```

Para executar um container, utilizamos o comando `docker container run`

```bash
docker container run -dit --name debian1 --hostname c1 debian
```

### **Descrição dos comandos:**

* **`docker container run (...) debian`** - Executa um container, sendo o último parâmetro o nome da imagem a ser utilizada
* **`-dit`** - Executa um container como processo (**`d`** = Detached), habilitando a interação com o container (**`i`** = Interactive) e disponibiliza um pseudo-TTY(**`t`** = TTY)
* **`--name`** - Define o nome do container
* **`--hostname`** - Define o hostname do container

Agora que temos nosso primeiro container em execução, podemos listar os containers (`docker container ls`) e conectar ao mesmo através do comando `docker container attach`

```bash
docker container ls
docker container attach debian1
```

{% hint style="info" %}
_Note que ao se conectar ao container a **PS1** será modificada para `root@c1:/#`_
{% endhint %}

Execute alguns comandos no container:

```bash
ip -c a
hostname
cat /etc/hosts
exit
```

Liste novamente os containers

```bash
docker container ls -a
```

{% hint style="info" %}
_Note que agora o container está parado, isto aconteceu pois o processo principal do container recebeu um `return code` diferente de `0`_
{% endhint %}

Inicie novamente o container e conecte-se ao mesmo

```bash
docker container start debian1
docker container attach debian1
```

_O comando `docker container start` inicia um container parado, o comando `docker container stop` para um container que esteja em execução_

Utilize a sequencia de teclas _`<CTRL> + <P> + <Q>`_ para se desconectar do container sem que ele seja parado.&#x20;

> Este comando é chamado de _`Read escape sequence`_.

```bash
<CTRL> + <P> + <Q>
docker container ls
```

{% hint style="success" %}
_Note que agora o container ainda está em execução._
{% endhint %}

Para verificar os logs do container utilizamos o comando `docker container logs`

```bash
docker container logs debian1
```

Pare e remova o container, após isto verifique os containers existentes

```bash
docker container stop debian1
docker container rm debian1
docker container ls -a
```

Podemos utilizar o parâmetro `-f` no comando `docker container rm` para que o container seja removido mesmo que esteja sendo executado.

Execute um novo container

```bash
docker container run -dit --name c1 --hostname server debian
docker container ls
```

Crie um arquivo de teste na pasta atual para enviar ao container `c1`

```bash
echo "Arquivo de teste" > /tmp/arquivo
docker container cp /tmp/arquivo c1:/tmp
```

O comando `docker container cp` copia um arquivo da maquina host para o container ou vice-versa.

Verifique se o arquivo existe dentro do container através do comando `exec`

```bash
docker container exec c1 ls -l /tmp
docker container exec c1 cat /tmp/arquivo
```

> _O comando `docker container exec` executa um comando no container e envia o retorno na saída padrão`(STDOUT)` da máquina, caso o container não tenha sido iniciado com a opção **`-i`** o retorno não será mostrado no `STDOUT`_

Remova o container criado anteriormente

```bash
docker container rm -f c1
```
