---
description: >-
  O Harbor é um registry open source que faz o armazenamento de artefatos e
  possibilita RBAC (Role based access control, ou acesso baseado em perfis) com
  interface gráfica.
---

# Harbor

{% embed url="https://goharbor.io/" %}

É um dos melhores registries existentes para se utilizar em sua própria infraestrutura.

### Instalação

Acesse a máquina `registry`

```bash
vagrant ssh registry
```

Remova o container `registry` e garanta que o docker-compose esta instalado

```bash
docker container rm -f registry
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Execute o download e a instalação do Harbor

```bash
HARBORVERSION=$(curl -s https://github.com/goharbor/harbor/releases/latest/download 2>&1 | grep -Po [0-9]+\.[0-9]+\.[0-9]+)
curl -s https://api.github.com/repos/goharbor/harbor/releases/latest | grep browser_download_url | grep online | cut -d '"' -f 4 | wget -qi -
tar xvf harbor-online-installer-v$HARBORVERSION.tgz
cd harbor
wget https://gist.githubusercontent.com/danncastro/2c41e8d7fc95ddb9e53ab28ae0c6197f/raw/4d3d5fd77db1aac7afacf91fcfa39bf37383afb7/harbor.yml 
sudo ./install.sh
```

Uma vez instalado o Harbor, acesse o endereço [http://registry.docker-dca.example/](http://registry.docker-dca.example/)

Efetue o login com usuário `admin` e senha `Harbor12345`

### Dashboard

Na pagina principal do Harbor temos as informações dos repositórios e projetos.

Vamos criar um novo projeto clicando em `+ New Project`

Vamos chamar o projeto de docker-dca e clicar em `OK`

Agora que temos nosso repositório, podemos clicar no nome `docker-dca` e em repositório para visualizar o `push command`, será exibido os comandos para fazer o push da imagem

### Users

Antes de enviar qualquer imagem para o Harbor, precisamos criar um novo usuário, afinal não queremos que o usuário admin seja utilizado para este fim.

Clique em `Users` e em seguida em em `+ NEW USER`

Criaremos o usuário `dannielcastro` com a senha `danncastro@YT2021`

> Desta vez precisamos que a senha tenha no mínimo 8 caracteres sendo 1 maiúsculo, 1 minúsculo, 1 número e 1 caractere especial

### Enviando imagens

Precisamos primeiramente alterar os `insecure-registries` em todos os nodes. Faça isso através do comando:

```bash
echo '{ "insecure-registries" : ["registry.docker-dca.example"] }' | sudo tee /etc/docker/daemon.json ; sudo systemctl restart docker
```

Agora que temos nosso usuário criado, vamos acessar a máquina `master` e efetuar o docker login

```bash
vagrant ssh master
docker login http://registry.docker-dca.example -u dannielcastro -p dannielcastro@YT2021
```

> Lembrem-se de não utilizar a flag -p em máquinas compartilhadas/ambientes de produção

Vamos editar a tag da imagem dannielcastro`/docker-supermario` e envia-la para o harbor

```bash
docker image pull dannielcastro/docker-supermario
docker image tag dannielcastro/docker-supermario registry.docker-dca.example/docker-dca/docker-supermario:latest
docker image push registry.docker-dca.example/docker-dca/docker-supermario:latest
```

Uma mensagem de erro será exibida:

```bash
unauthorized: unauthorized to access repository: docker-dca/docker-supermario, action: push: unauthorized to access repository: docker-dca/docker-supermario, action: push
```

Isso acontece porque precisamos dar permissão para o usuário dannielcastro no projeto `docker-dca` com perfil de `Mantainer` ou `Project Admin`

Vamos clicar em `Projects` , `docker-dca`, `members` e em `+ USER`.

Vamos voltar para o terminal e efetuar o push novamente

```bash
docker image push registry.docker-dca.example/docker-dca/docker-supermario:latest
```

Vamos efetuar o push também da imagem do traefik

```bash
docker image pull traefik:v2.4
docker image tag traefik:v2.4 registry.docker-dca.example/docker-dca/traefik:v2.4
docker image push registry.docker-dca.example/docker-dca/traefik:v2.4
```

Voltando ao harbor e clicando em `Repositories` no projeto `docker-dca` conseguiremos ver a nossas imagem disponíveis.
