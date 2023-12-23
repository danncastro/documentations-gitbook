---
description: >-
  Essas assinaturas habilitam a verificação pelo cliente ou pelo runtime (docker
  no nosso caso) a verificação da integridade e o responsável por publicar tags
  específicas.
---

# Docker Content Trust

O registro de uma imagem segue o seguinte padrão.

```bash
[REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]

registry.docker-dca.example[:5000]/docker-dca/nginx[:latest]

REGISTRY_HOST: registry.docker-dca.example
REGISTRY_PORT: default (5000)
REPOSITORY: docker-dca/nginx (projeto/imagem)
TAG: default (latest)
```

Um determinado repositório pode ter diversas tags, como por exemplo `latest`, `1.2.3`, `alpine`, etc... Um usuário pode fazer a publicação de uma imagem assinada com tag `latest`. Mais tarde essa imagem pode ser substituída imagem não assinada. O segundo envio substitui a imagem fazendo com que a `latest` não seja mais assinada. porém a não irá afetar a imagem que foi assinada anteriormente. Por este e outros motivos o ideal é efetuar o tag da imagem como `latest` e uma versão estática, com isso garantindo que a imagem tenha por exemplo `latest` e `1.0.0`. E quando substituirmos a latest, teremos ainda a versão `1.0.0` assinada, e a latest "não-assinada".

Para ativar o Docker Content Trust basta exportar a variável de ambiente `DOCKER_CONTENT_TRUST=1`

> O Docker Content Trust é desativado por padrão no Docker Client.

```bash
export DOCKER_CONTENT_TRUST=1
docker image pull danncastro/docker-supermario

```

Como a imagem não é assinada, veremos que o Download irá falhar:

```bash
Using default tag: latest
Error: remote trust data does not exist for docker.io/danncastro/docker-supermario: notary.docker.io does not have trust data for docker.io/danncastro/docker-supermario
```

Caso desativemos o Docker content Trust a imagem poderá ser baixada com sucesso.

```bash
export DOCKER_CONTENT_TRUST=0
docker image pull danncastro/docker-supermario
```

### Assinando imagens com o Docker Content Trust

> Os passos a seguir serão executados no docker hub e em sua máquina local, uma vez que nosso registry não tem os certificados adicionados e é um "Insecure Registry".

Para assinarmos uma imagem precisamos gerar as chaves através do comando `docker trust`. Vamos acessar a nossa máquina efetuar o login no dockerhub e criar nossa chave.

> As chaves podem ser geradas localmente ou delegadas por uma autoridade certificadora, para fim de estudos iremos gerar localmente nossas chaves.

```bash
docker login -u <username>
docker trust key generate dannielcastro
```

Será solicitada uma senha para a chave, utilizaremos `dannielcastro@docker.exemple`

```bash
Generating key for dannielcastro...
Enter passphrase for new danncastro key with ID f0e7172: dannielcastro@docker.exemple
Repeat passphrase for new danncastro key with ID f0e7172: dannielcastro@docker.exemple
Successfully generated and loaded private key. Corresponding public key available: dannielcastro.pub
```

Caso já possua uma chave, podemos carrega-la através do comando `docker trust key load <key.pub> --name <key_name>`

Após obtermos a chave, precisamos adicionar a delegação da chave no servidor, fazemos isso através do comando

```bash
docker trust signer add --key <key_name> <user_name> <repo_name> 
```

Será solicitada a senha para a chave e para o repositório, para fins de estudo utilizaremos `dannielcastro@docker.exemple`

```bash
docker trust signer add --key danncastro.pub danncastro/nginx

Adding signer "dannielcastro" to danncastro/nginx...
Initializing signed repository for danncastro/nginx...
Enter passphrase for root key with ID 61e8fca: dannielcastro@docker.exemple
Enter passphrase for new repository key with ID ed6f165: dannielcastro@docker.exemple
Repeat passphrase for new repository key with ID ed6f165: dannielcastro@docker.exemple
Successfully initialized "dannielcastro/nginx"
Successfully added signer: dannielcastro to danncastro/nginx
```

Para fins de estudo utilizaremos a imagem do nginx, vamos efetuar o download e adicionar uma nova tag a imagem

```bash
docker image pull nginx
docker image tag nginx danncastro/nginx
```

Finalmente podemos assinar e enviar a imagem para o registry:

> Será solicitada a senha para a chave

```bash
docker trust sign danncastro/nginx:latest

Signing and pushing trust data for local image danncastro/nginx:latest, may overwrite remote trust data
The push refers to repository [docker.io/danncastro/nginx]
2bed47a66c07: Mounted from library/nginx 
82caad489ad7: Mounted from library/nginx 
d3e1dca44e82: Mounted from library/nginx 
c9fcd9c6ced8: Mounted from library/nginx 
0664b7821b60: Mounted from library/nginx 
9321ff862abb: Mounted from library/nginx 
latest: digest: sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47 size: 1570
Signing and pushing trust metadata
Enter passphrase for danncastro key with ID 6b878e6: danncastro@youtube
Successfully signed docker.io/danncastro/nginx:latest
```

Podemos validar nossa imagem através do comando:

```bash
DOCKER_CONTENT_TRUST=1 docker image pull danncastro/nginx
```

Uma mensagem será exibida informando que a imagem esta atualizada

```bash
Using default tag: latest
Pull (1 of 1): danncastro/nginx:latest@sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47
docker.io/danncastro/nginx@sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47: Pulling from danncastro/nginx
Digest: sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47
Status: Image is up to date for danncastro/nginx@sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47
Tagging danncastro/nginx@sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47 as danncastro/nginx:latest
docker.io/danncastro/nginx:latest
```

Após o download podemos inspecionar a imagem para verificar sua integridade.

```bash
docker trust inspect danncastro/nginx
docker trust inspect --pretty danncastro/nginx
```

```bash
Signatures for danncastro/nginx

SIGNED TAG   DIGEST                                                             SIGNERS
latest       4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47   danncastro

List of signers and their keys for danncastro/nginx

SIGNER           KEYS
danncastro   6b878e6e710f

Administrative keys for danncastro/nginx

  Repository Key:       ed6f165932c8bd0e3c6fb0fa326157229788fede24d49e16ac798b5560cdf5e4
  Root Key:     49e0e996f6fb02c4c2841dd8d29ffbd4f77a7c64722a47f3f622c6b5d17d78e5
```

Caso efetuemos o download da imagem sem o Docker Content Trust ativo, a mensagem exibida não verificará o digest

```bash
DOCKER_CONTENT_TRUST=0 docker image pulldanncastro/nginx

Using default tag: latest
latest: Pulling from danncastro/nginx
Digest: sha256:4424e31f2c366108433ecca7890ad527b243361577180dfd9a5bb36e828abf47
Status: Image is up to date for danncastro/nginx:latest
docker.io/danncastro/nginx:latest
```

### UCP/MKE Role Based Access Control

RBAC (Role Based Access Control) ou controle de acesso baseado em papéis, é a maneira utilizada pelo UCP/MKE para controlar o acesso a seus recursos.

Para criar ou configurar uma role, vamos acessar o dashboard em https://master.docker-dca.example e navegar até o menu `Access Control` > `Roles`. Para criar uma nova role basta selecionar `Swarm` e clicar em `Create`

> Também é possível criar roles para o Kubernetes através da aba `Kubernetes`

De um nome a role como por exemplo `containers-full-access` e em operations vamos selecionar as operações que desejamos liberar o acesso.

Selecione em `Container Operations` a opção `All container operations` e clique em create

Para atrelar uma role a um usuário, vamos em `Grants`, `Swarm`, `Create Grant`

Selecione o usuário `analista`, o Resource Set `Swarm` e a Role `containers-full-access` e clique em create

Agora nosso usuário analista terá o acesso concedido.

### UCP/MKE Client Bundle

O `Client Bundle` é utilizado para o usuário obter acesso direto ao cluster do UCP/MKE, para criar e efetuar o download do bundle, vamos acessar o dashboard em https://master.docker-dca.example e navegar até o menu `Access Control` > `Users`. Selecionar o usuário `analista`e na tela que se abrir clicar no icone da engrenagem no canto superior direito.

Selecione o menu `Client Bundle` e em seguida clique em `New Client Bundle` > `Generate Client Bundle`

Clique em `Confirm` e será feito o download do client bundle para sua máquina.

O client bundle consiste de todos os arquivos necessários para acessar o UCP/MKE.

Vamos copiar o bundle para nossa pasta atual e remover o mesmo do pacote zip.

```bash
$ mv ~/Downloads/ucp-bundle-analista.zip .
$ unzip ucp-bundle-analista.zip
```

Para utilizar o pacote podemos utilizar o arquivo `env.sh`

```bash
$ source env.sh

Cluster "ucp_master.docker-dca.example:6443_analista" set.
User "ucp_master.docker-dca.example:6443_analista" set.
Context "ucp_master.docker-dca.example:6443_analista" created.
```

Podemos executar o comando `docker version` para verificar se nosso ambiente foi configurado com sucesso.

```bash
$ docker version

Server: Mirantis Cloud Native Platform
 Engine:
  Version:          20.10.9
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.8
  Git commit:       79ea9d3
  Built:            Mon Oct  4 16:06:34 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.11
  GitCommit:        5b46e404f6b9f661a205e28d59c982d3634148f8
 runc:
  Version:          1.0.2
  GitCommit:        v1.0.2-0-g52b36a2
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
 Mirantis Kubernetes Engine:
  Version:          3.4.6
  ApiVersion:       1.40
  Arch:             amd64
  BuildTime:        Wed Oct  6 19:07:18 UTC 2021 
  GitCommit:        1b0bd58
  GoVersion:        go1.16.4
  MinApiVersion:    1.20
  Os:               linux
 Kubernetes:
  Version:          1.20+
  buildDate:        2021-09-18T04:05:21Z
  compiler:         gc
  gitCommit:        c0557aa1a9571c353503500cb7130a2f491d073f
  gitTreeState:     clean
  gitVersion:       v1.20.11-mirantis-1
  goVersion:        go1.15.15
  major:            1
  minor:            20+
  platform:         linux/amd64
 Calico:
  Version:          v3.19.1
  cni:              v3.19.1
  kube-controllers: v3.19.1
  node:             v3.19.1
```

Executar os seguintes comandos:

```bash
$ docker node ls 
$ docker container ls
```

Note que não conseguimos ver nenhuma informação dos nodes, porém podemos visualizar todos os dados dos containers em execução, isso é possível pela role que configuramos. Como expermimento, adicione a role `Full Control` ao usuário `analista` e verifique novamente o comando `docker node ls`

```bash
$ docker node ls     
ID                            HOSTNAME                      STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
8axa7grquom38yqmp8uoykt8r *   master.docker-dca.example     Ready     Active         Leader           20.10.9
xm5sn34f86o0cvtfvsozdruvi     node01.docker-dca.example     Ready     Active                          20.10.9
pwdjpq0iq9p9bs00t81dda9sw     node02.docker-dca.example     Ready     Active                          20.10.9
3pvpxesooexiibjy8dbnv6rw0     registry.docker-dca.example   Ready     Active                          20.10.9
```

Agora com o devido acesso, o usuário `analista` pode efetuar qualquer ação em nosso cluster.
