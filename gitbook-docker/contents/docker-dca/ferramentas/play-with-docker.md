---
description: >-
  O Play With Docker é um ambiente fornecido pela Docker na qual é possível
  utilizar um laboratório por 4 horas. É necessário uma conta no Docker Hub.
---

# Play With Docker

> O PWD (Play With Docker) costuma ficar indisponível devido a alta utilização do mesmo por diversos usuários. Utilize o mesmo apenas para fins de estudo.

{% embed url="https://www.docker.com/play-with-docker/" %}
Fonte: Docker Inc
{% endembed %}

Ao acessar o laboratório e clicar em `Start` teremos acesso a uma interface simples na qual podemos executar diversos containers dentro do período especificado.

Para adicionar uma instância basta clicar em `+ ADD NEW INSTANCE`

Na tela a direita, temos alguns campos importantes que precisamos conhecer:

1. PWD Id
2. Instance Private IP
3. Exposed Ports
4. Memory Usage
5. CPU Usage
6. SSH Command
7. Delete / File Editor
8. Live Terminal

Podemos interagir com o `PWD` através do terminal. Vamos executar um container simples do nginx através do comando

```bash
docker container run -dit --name nginx -p 80:80 nginx
```

Note que ao executar um container com uma porta publicada, será exibida a porta que foi publicada ao lado do IP, se clicarmos no numero da porta teremos acesso a aplicação

Podemos também copiar o endereço SSH e executar em nosso terminal para ter acesso ao terminal através de nossa máquina

Vamos remover nossa instância clicando em `Delete`

Podemos também usufruir de clusters pré configurados de Docker Swarm, para isto basta clicar no ícone de chave inglesa e escolher qual template gostaria de utilizar.

Vamos escolher o template `3 Managers and 2 Workers`

Verifique que todas as instâncias serão criadas e exibidas no menu a esquerda, sendo as managers as instâncias que exibem um ícone de usuário.

Para verificar o cluster, execute o comando `docker node ls` em um nó do tipo manager.
