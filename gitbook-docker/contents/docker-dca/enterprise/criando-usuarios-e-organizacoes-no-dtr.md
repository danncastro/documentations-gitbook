---
description: Na tela inicial do DTR clique em Users>New user
---

# Criando Usuários e Organizações no DTR

Coloque o nome do usuário de `analista` com a senha `dannielcastro@docker.exemple` e clique em `Save`

clique em `Organizations`>`New organization`

Preencha o nome como `docker-dca` e clique em Save

Agora que temos nosso usuário e nossa organização, podemos adicionar o usuário a organização indo em `Organizations`>`docker-dca` e em seguida clicar em `Add user`

Preencha os dados e adicione o usuário a organização, vamos alterar seu nivel de permissionamento para `Org Owner`

## Criando Repositórios no DTR

Clique em `Repositories` e em seguida em `New Repository`

Vamos criar o repositório `nginx` como público e clicar em `Save`

Após a criação do repositório podemos clicar em `View Details` para visualizar os detalhes do repositório

Na tela a seguir teremos diversas informações do repositório, mirrors e como efetuar o pull de uma nova imagem

## Enviando imagens para o DTR (sem licença ativa)

O processo de envio de imagens para o DTR é o mesmo que utilizamos com qualquer outro registry.

Como não temos uma licença do Docker Enterprise não podemos enviar imagens para o mesmo. Uma mensagem de erro será exibida, porém iremos passar por todos os passos para o envio da imagem

Em uma das máquinas do cluster iremos adicionar o parametro `insecure-registries`, efetuar o pull de uma imagem do nginx, efetuar o login no registry, alterar a tag da imagem e enviar para o DTR

```bash
vagrant ssh master

echo '{ "insecure-registries" : ["registry.docker-dca.example"] }' | sudo tee /etc/docker/daemon.json ; sudo systemctl restart docker

docker image pull nginx
docker image tag nginx registry.docker-dca.example/docker-dca/nginx
docker login registry.docker-dca.example -u analista -p dannielcastro@docker.exemple
docker push registry.docker-dca.example/docker-dca/nginx
```

A mensagem de erro será exibida porque não temos uma licença válida do DTR, porém o cenário esperado seria a imagem disponível no DTR

```bash
Using default tag: latest
The push refers to repository [registry.docker-dca.example/docker-dca/nginx]
9959a332cf6e: Preparing 
f7e00b807643: Preparing 
f8e880dfc4ef: Preparing 
788e89a4d186: Preparing 
43f4e41372e4: Preparing 
e81bff2725db: Waiting 
error parsing HTTP 402 response body: invalid character 'D' looking for beginning of value: "DTR doesn't have a license\n"
```

## Adicionando a Licença no MKE/DTR

Para seguir com esta etapa você deve possuir uma licença do MKE

> Para solicitar uma licença de teste é preciso entrar em contato com a Mirantis através do website https://www.mirantis.com/contact/

Acesse o dashboard através do endereço https://master.docker-dca.example e conecte-se com o usuário e senha que criamos

Após o login podemos enviar uma licença, clicando em upload a license, porém vamos ver outra maneira de enviar a licença, clique em _Skip For Now_

No menu principal, clique em `admin` e em seguida em `Admin Settings`

Para enviar a licença clique em `Upload License` e selecione o arquivo de licença em seu computador.

Após isto será exibida a tela com a licença válida

Acesse o **DTR** através do endereço https://registry.docker-dca.example/ e efetue o login com o usuário e senha de administrador

Após o login podemos enviar uma licença, clicando em upload a license, porém vamos ver outra maneira de enviar a licença, clique em _Skip For Now_

Para adicionar a licença ao DTR clique em `admin` e em seguida em `System`.

Na aba `General` clique em apply a new license e selecione o arquivo

Após isto será exibida a tela com a licença válida

Agora que temos nosso sistema licenciado, podemos retornar ao envio da imagem ao DTR

## Enviando imagens para o DTR (com licença ativa)

Acesse a máquina master e execute novamente o comando para enviar a imagem

```bash
vagrant ssh master

docker login registry.docker-dca.example -u analista -p dannielcastro@docker.exemple
docker push registry.docker-dca.example/docker-dca/nginx
```

Agora nossa imagem pode ser enviada com sucesso uma vez que temos o licenciamento ativo

```bash
Using default tag: latest
The push refers to repository [registry.docker-dca.example/docker-dca/nginx]
9959a332cf6e: Pushed 
f7e00b807643: Pushed 
f8e880dfc4ef: Pushed 
788e89a4d186: Pushed 
43f4e41372e4: Pushed 
e81bff2725db: Pushed 
latest: digest: sha256:7250923ba3543110040462388756ef099331822c6172a050b12c7a38361ea46f size: 1570
```
