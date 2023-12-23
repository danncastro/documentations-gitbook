---
description: >-
  Primeiramente, devemos verificar se o Docker está rodando. Se sim, no Linux ou
  no macOs, vamos executar os comandos do Docker através do próprio terminal
  nativo do sistema operacional.
---

# Docker DCA

No caso do Windows, o próprio Docker recomenda que os seus comandos sejam executados através do Windows PowerShell.

Lembrando que se o seu Docker foi instalado pelo Docker Toolbox, você deve executar os seus comandos através do Docker Quickstart Terminal, terminal que foi instalado pelo próprio Docker Toolbox.

A diferença entre imagens e containers Na aula anterior, para executar a imagem hello-world, executamos o comando docker run hello-world. Quando executado esse comando, a primeira coisa que o Docker faz é verificar se temos a imagem hello-world no nosso computador, e caso não tenhamos, o Docker buscará e baixará essa imagem no [Docker Hub/Docker Store](https://store.docker.com/)

A imagem é como se fosse uma receita de bolo, uma série de instruções que o Docker seguirá para criar um container, que irá conter as instruções da imagem, do hello-world. Criado o container, o Docker executa-o. Então, tudo isso é feito quando executamos o docker run hello-world.

Há milhares de imagens na Docker Store disponíveis para executarmos a nossa aplicação. Por exemplo, temos a imagem do Ubuntu:

```bash
docker run ubuntu
```

Ao executar o comando, o download começará. Podemos ver que não é feito somente um download, pois a imagem é dividida em camadas, que veremos mais à frente. Terminados os downloads, nenhuma mensagem é exibida, então significa que o container não foi criado? Na verdade o container foi criado, o que acontece que é a imagem do Ubuntu não executa nada, por isso nenhuma mensagem foi exibida.

Podemos verificar isso vendo os containers que estão sendo executados no momento, executando o seguinte comando:

```bash
docker ps
```

Ao executar esse comando, vemos que não há nenhum container ativo, pois quando não há nada para o container executar, eles ficam parados. Para ver todos containers, inclusive os parados, adicionamos a flag -a ao comando acima:

```bash
docker ps -a
```

```
CONTAINER ID    IMAGE         COMMAND       CREATED         STATUS                     PORTS     NAMES
4139842e283a    ubuntu        "/bin/bash"   3 minutes ago   Exited (0) 3 minutes ago             elastic_albattani
c1a155091114    hello-world   "/hello"      4 days ago      Exited (0) 4 days ago                nifty_mcclintock
```

Com esse comando, conseguimos ver o id e o nome do container, valores que são criados pelo próprio Docker. Além disso, temos a outras informações dos containers, a imagem em que eles são baseados, o comando inicial que roda quando ele é executado, quando ele foi criado e qual o seu status.

Então, o container do Ubuntu foi executado, mas ele não fez nada pois não pedimos para o container executar algo que funcione dentro do Ubuntu. Então, quando executamos o container do Ubuntu, precisamos passar para ele um comando que rode dentro dele, por exemplo:

```
docker run ubuntu echo "Ola Mundo"
```

Com isso, o Docker irá executar um container com Ubuntu, executar o comando echo "Ola Mundo" dentro dele e nos retornar a saída:

```bash
alura@alura-estudio-03:~$ docker run ubuntu echo "Ola Mundo"
Ola Mundo
```

Só que sabemos que o Ubuntu é um sistema operacional completo, então não queremos ficar somente executando um comando por comando dentro dele, sempre criando um novo container. Então, como fazemos para criar um container e interagir com ele mais do que com um único comando?

Trabalhando dentro de um container Podemos fazer com que o terminal da nossa máquina seja integrado ao terminal de dentro do container, para ficar um terminal interativo. Podemos fazer isso adicionando a flag -it ao comando, atrelando assim o terminal que estamos utilizando ao terminal do container:

```bash
docker run -it ubuntu
```

Assim que executamos o comando, já podemos perceber que o terminal muda:

```bash
alura@alura-estudio-03:~$ docker run -it ubuntu
root@05025384675e:/# 
```

Com isso, estamos trabalhando dentro do container. E dentro dele, podemos trabalhar como se estivéssemos trabalhando dentro do terminal de um Ubuntu, executando comandos como ls, cat, etc.

Agora, se abrirmos outro terminal e executar o comando docker ps, veremos o container que estamos executando. Podemos parar a sua execução, digitando no container o comando exit ou através do atalho CTRL + D .

Executando novamente um container Paramos a execução do container, tanto que o comando docker ps não nos retorna mais nada. E se listarmos todos os containers, através do comando docker ps -a, vemos que ele está lá, parado. Mas agora, para não criar novamente um novo container, queremos executá-lo novamente.

Fazemos isso pegando id do container a ser iniciado, e passando-o ao comando docker start

```bash
docker start 05025384675e
```

Esse comando roda um container já criado, mas não atrela o nosso terminal ao terminal dele. Para atrelar os terminais, primeiramente devemos parar o container, com o comando docker stop mais o seu id:

```bash
docker stop 05025384675e
```

E rodamos novamente o container, mas passando duas flags: -a, de attach, para integrar os terminais, e -i, de interactive, para interagirmos com o terminal, para podermos escrever nele:

```bash
alura@alura-estudio-03:~$ docker start -a -i 05025384675e
root@05025384675e:/#
```

Com isso, conseguimos ver um pouco de como subir um container, pará-lo e executá-lo novamente, além de trabalhar dentro dele.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Removendo containers Só que com os testes que fizemos até agora, acabamos criando vários containers (lembrando que podemos ver todos os containers criados executando o comando docker ps -a) e nunca removemos algum deles, já que os comandos acima só mudam os seus estados. Para remover um container, executamos o comando docker rm, passando para ele o id do container a ser removido, por exemplo:

```bash
docker rm c9f83bfb82a8
```

Mas para limpar todos os containers inativos, devemos remover um por um? Não, pois há um novo comando do Docker, o prune, que serve para limparmos algo específico do Docker. Como queremos remover os containers parados, executamos o seguinte comando:

```bash
docker container prune
```

O comando é tão poderoso que ele pede para confirmarmos se é isso mesmo que queremos fazer.

Listando e removendo imagens E do mesmo jeito que temos o comando docker container para mexermos com o container, temos o comando docker images, que nos exibe as imagens que temos na nossa máquina. Para remover uma imagem, utilizarmos o comando docker rmi, passando para ele o nome da imagem a ser removida, por exemplo:

```bash
docker rmi hello-world
```

Camadas de uma imagem Na aula anterior, quando baixamos a imagem do Ubuntu, reparamos que ela possui camadas, mas como elas funcionam? Toda imagem que baixamos é composta de uma ou mais camadas, e esse sistema tem o nome de Layered File System.

Essas camadas podem ser reaproveitadas em outras imagens. Por exemplo, já temos a imagem do Ubuntu, isso inclui as suas camadas, e agora queremos baixar a imagem do CentOS. Se o CentOS compartilha alguma camada que já tem na imagem do Ubuntu, o Docker é inteligente e só baixará as camadas diferentes, e não baixará novamente as camadas que já temos no nosso computador:

No caso da imagem acima, o Docker só baixará as duas primeiras camadas da imagem do CentOS, já que as duas últimas são as mesmas da imagem do Ubuntu, que já temos na nossa máquina. Assim poupamos tempo, já que precisamos de menos tempo para baixar uma imagem.

Uma outra vantagem é que as camadas de uma imagem são somente para leitura. Mas como então conseguimos criar arquivos na aula anterior? O que acontece é que não escrevemos na imagem, já que quando criamos um container, ele cria uma nova camada acima da imagem, e nessa camada podemos ler e escrever:

Então, quando criamos um container, ele é criado em cima de uma imagem já existente e nele nós conseguimos escrever. E com uma imagem base, podemos reaproveitá-la para diversos containers:

Isso nos traz economia de espaço, já que não precisamos ter uma imagem por container.

\[QUIS] Temos os seguintes comandos do Docker e suas respectivas funcionalidades:

1. O comando docker ps nos permite ver todos os container já criados
2. O comando docker rm ID\_DO\_CONTAINER remove um container
3. O comando docker container prune permite remover todos os containers inativos de uma só vez.

Quais funcionalidades, e seus respectivos comandos, estão corretas? Funcionalidade 1 Alternativa errada! A funcionalidade 1 está errada, pois o comando docker ps mostra apenas os containers em execução. O correto então é docker ps -a. A flag -a faz com que ps mostre todos os containers. Alternativamente você pode usar: docker ps --all.

Funcionalidade 3 Alternativa correta! O comando docker container prune realmente apaga todos os containers inativos, mas dá um aviso antes.

Funcionalidade 2 Alternativa correta! O comando docker rm ID\_DO\_CONTAINER permite remover um container especifico.

\[QUIS] Temos as seguintes afirmações sobre o Layered File System:

A) Toda imagem que baixamos é composta de uma ou mais camadas.

B) Essas camadas podem ser reaproveitadas em outras imagens, acelerando assim o tempo de download.

C) As camadas de uma imagem são de escrita e leitura

Qual dessas afirmações é falsa? B é falsa B é correto, motivo pelo qual os downloads podem ser acelerados.

C é falsa Realmente é falsa pois as camadas na imagem são de leitura apenas.

A é falsa A é correto, pois imagens são compostas de uma ou mais camadas.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Agora que já conhecemos mais sobre containers, imagens e a diferença entre eles, já podemos fazer um container mais interessante, um pouco mais complexo. Então, vamos criar um container que segurará um site estático, para entendermos também como funciona a parte de redes do Docker. Para tal, vamos baixar a imagem dockersamples/static-site:

```bash
docker run dockersamples/static-site
```

Nas imagens que vimos anteriormente, as imagens oficiais, não precisamos colocar um username na hora de baixá-las. Esse username representa o usuário que toma conta da imagem, quem a criou. Como a imagem que vamos utilizar foi criada por outro(s) usuário(s), precisamos especificar o seu username para baixá-la.

Terminado o download da imagem, o container é executado, pois sabemos que os containers ficam no estado de running quando são criados. No caso dessa imagem, o container está executando um processo de um servidor web, que está disponibilizando o site estático para nós, então esse processo trava o terminal. Mas como evitamos que esse travamento aconteça?

Para tal, paramos o container que acabamos de criar e para impedir o travamento, nós executamos-o sem atrelar o nosso terminal ao terminal do container, fazendo isso através da flag -d:

```bash
docker run -d dockersamples/static-site
```

Assim, o container fica executando em segundo plano. Podemos verificar que o container realmente está rodando executando o comando docker ps:

```bash
alura@alura-estudio-03:~$ sudo docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS             PORTS            NAMES
a6f2fab332db   dockersamples/static-site  "/bin/sh -c 'cd /u..."   About a minute ago   Up About a minute  80/tcp, 443/tcp  brave_wozniak
```

Mas como fazemos para acessar o site estático?

Acessando o site Em nenhum momento dizemos onde está o site estático. Qual porta que utilizamos para acessá-lo? A 80, conforme está na saída do docker ps? Essa é a porta interna que o container está utilizando. Então, o que precisamos fazer é linkar essa porta interna do container a uma porta do nosso computador. Para fazer isso, precisamos adicionar mais uma flag, a -P, que fará com que o Docker atribua uma porta aleatória do mundo externo, que no caso é a nossa máquina, para poder se comunicar com o que está dentro do container:

```bash
docker run -d -P dockersamples/static-site
```

Agora, ao executar novamente o comando docker ps, na coluna PORTS, vemos algo como:

```bash
PORTS
0.0.0.0:9001->80/tcp, 0.0.0.0:9000->443/tcp
```

No caso do mapeamento acima, vemos que a porta 9001 da nossa máquina faz referência à porta 80 do container, e a porta 9000 da nossa máquina faz referência à porta 443 do container. Uma outra maneira de ver as portas é utilizar o comando docker port, passando para ele o id do container:

```bash
alura@alura-estudio-03:~$ sudo docker port 989e4d7d3638
443/tcp -> 0.0.0.0:9000
80/tcp -> 0.0.0.0:9001
```

Então, se quisermos acessar a porta 80, que é onde está o site estático, na nossa máquina, como o endereço 0.0.0.0 representa a nossa máquina local, podemos acessar o endereço http://localhost:9001/ no navegador.

Caso você esteja utilizando o Docker Toolbox, como ele está rodando em cima de uma máquina virtual, o endereço http://localhost:9001/ não funcionará, pois você deve acessar a porta através do IP da máquina virtual. Para descobrir o IP dessa máquina virtual, basta executar o comando docker-machine ip. Com o IP em mãos, basta acessá-lo no navegador, utilizando a porta que o Docker atribuiu, por exemplo http://192.168.0.38:9001/.

Nomeando um container Uma outra coisa interessante que é possível fazer quando estamos criando um container é que podemos dar um nome para o container, assim não ficamos dependendo os ids aleatórios que o Docker atribui, tornando mais fácil na hora de parar e remover o container, por exemplo. Para dar um nome para o container, utilizamos a flag --name:

```bash
docker run -d -P --name meu-site dockersamples/static-site
```

Assim o nome do nosso container será meu-site. Agora, para pará-lo, basta passar o seu nome para o comando docker stop:

```bash
docker stop meu-site
```

A mesma coisa seria para rodar o container novamente, ou para removê-lo, bastando apenas nós utilizarmos o seu nome.

Definindo uma porta específica Uma outra coisa interessante para vermos é que, quando estamos criando um container e queremos linkar uma porta interna sua a uma porta do nosso computador, utilizamos a flag -P, para o Docker atribuir uma porta aleatória da nossa máquina, assim podemos nos comunicar com o que está dentro do container. Mas podemos definir essa porta, utilizando a flag -p, nesse modelo: -p PORTA-MUNDO-EXTERNO:PORTA-CONTAINER, por exemplo:

```bash
docker run -d -p 12345:80 dockersamples/static-site
```

Nesse exemplo, através da porta 12345 do nosso computador podemos acessar a porta 80 do container.

Atribuindo uma variável de ambiente Além disso, podemos atribuir uma variável de ambiente no container. Por exemplo, a página do site estático pega o valor da variável de ambiente AUTHOR e o exibe junto à mensagem de Hello, então podemos modificar o valor dessa variável, através da flag -e:

```bash
docker run -d -P -e AUTHOR="Douglas Q" dockersamples/static-site
```

Quando abrirmos o site, a mensagem que será exibida é Hello Douglas Q.

Parando todos os containers de uma só vez Por último, podemos ver apenas os ids dos containers que estão rodando, executando o comando docker ps -q. E com esse comando, podemos parar todos os containers de uma só vez. Para isso, podemos utilizar a interpolação de comandos, no padrão $(comando), que executa o comando, captura sua saída e insere isso na linha de comando:

```bash
docker stop $(docker ps -q)
```

Então, o comando docker ps -q será executado e a sua saída,os ids dos containers que estão rodando, será inserida no comando docker stop, parando assim todos os containers.

Além disso, o comando docker stop demora um pouco para ser executado pois ele espera 10 segundos para parar o container. Podemos diminuir esse tempo através da flag -t, passando o tempo a ser aguardado, por exemplo:

```bash
docker stop -t 0 $(docker ps -q)
```

Com isso, exploramos um pouco mais o docker run.

\[QUIS] Luis resolveu acessar sua aplicação, chamada de MinhaAplicacao e que está dentro do container, utilizando a porta 8080 a partir de sua máquina. Porém, como é novo no ramo do Docker, ele está em dúvida sobre qual comando deve utilizar para saber quais são as portas mapeadas. Marque a opção que realiza o desejo de Luis: Luis deve usar o comando docker run MinhaAplicacao. Errado. Este comando é para executar um container com uma respectiva imagem.

Luis deve usar o comando docker port ID\_DO\_CONTAINER para que possa saber qual porta de sua máquina faz referência à porta 8080 de seu container. Certo!

Luis deve usar o comando docker rmi MinhaAplicacao. Errado. Este comando é para remover uma imagem passada como parâmetro.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

DevOps é um tópico bem amplo que envolve tanto aspectos culturais como técnicos, mas possui como principal objetivo aumentar a qualidade e eficiência da entrega de software. DevOps é uma metodologia que visa integrar os times de desenvolvimento com infraestrutura e o Docker está tendo um papel importante nessa tarefa.

Repare que com Docker os desenvolvedores não precisam se preocupar em configurar um ambiente de desenvolvimento específico de cada vez. Em vez disso, eles podem se concentrar na construção de um código de boa qualidade. Isso, obviamente, leva à aceleração nos esforços de desenvolvimento. O Docker facilita muito construir o ambiente: é rapido, simples e confiável.

No outro lado, para a equipe de operações de TI/Sysadmins, o Docker possibilita configurar ambientes que são exatamente como um servidor de produção e permite que qualquer pessoa trabalhe no mesmo projeto com exatamente as mesmas configurações, independentemente do ambiente de host local. As configurações são descritas em arquivos simples facilmente aplicáveis pelo desenvolvedor.

Com a padronização de um entregável Docker é possível que o desenvolvedor tenha um ambiente similar ao de produção na sua máquina sem todo o custo de configuração e o Sysadmin consiga lidar apenas com um tipo de entregável conseguindo, desta forma, dar atenção aos desafios de monitoramento e orquestração para que nada dê errado. Neste caso, o melhor para os dois.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

O que são os volumes? Quando escrevemos em um container, assim que ele for removido, os dados também serão. Mas podemos criar um local especial dentro dele, e especificamos que esse local será o nosso volume de dados.

Quando criamos um volume de dados, o que estamos fazendo é apontá-lo para uma pequena pasta no Docker Host. Então, quando criamos um volume, criamos uma pasta dentro do container, e o que escrevermos dentro dessa pasta na verdade estaremos escrevendo do Docker Host.

Isso faz com que não percamos os nossos dados, pois o container até pode ser removido, mas a pasta no Docker Host ficará intacta.

Trabalhando com volumes Sabendo disso, vamos ver como trabalhar com o Docker Host. No Terminal ou PowerShell (ou Docker Quickstart Terminal), criamos um container com o docker run, mas dessa vez utilizando a flag -v para criar um volume, seguido do nome do mesmo:

```bash
docker run -v "/var/www" ubuntu
```

No exemplo acima, criamos o volume /var/www, mas a que pasta no Docker Host ele faz referência? Para descobrir, podemos inspecionar o container, executando o comando docker inspect, passando o seu id para o mesmo:

```bash
docker inspect 8cf7b40ce226
```

Temos uma saída com diversas informações, mas a que nos interessa é o "Mounts":

```bash
"Mounts": [
    {
        "Type": "volume",
        "Name": "5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576",
        "Source": "/var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/_data",
        "Destination": "/var/www",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
]
```

Nele, podemos ver que o /var/www será escrito na nossa máquina no diretório /var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/\_data, endereço que foi gerado automaticamente pelo Docker. Ou seja, tudo que escrevermos na pasta /var/www do container, na verdade estaremos escrevendo na pasta /var/lib/docker/volumes/5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576/\_data da nossa máquina.

E ao remover o container, a pasta continuará na nossa máquina. Essa pasta gerada pelo Docker pode ser configurada, podemos dizer a pasta que será referenciada pela pasta /var/www do container. Por exemplo, se quisermos escrever dentro do Desktop da nossa máquina, devemos passá-lo antes do volume, separando-os com dois pontos. Além disso, vamos executar o container no modo interativo:

```bash
docker run -it -v "C:\Users\Alura\Desktop:/var/www" ubuntu
root@abd0286c0083:/#
```

Ou seja, quando escrevermos na pasta /var/www do container, estaremos escrevendo no Desktop da nossa máquina. Para provar isso, na pasta /var/www, vamos criar um arquivo e escrever nele uma mensagem:

```bash
root@abd0286c0083:/# cd /var/www/
root@abd0286c0083:/var/www# touch novo-arquivo.txt
root@abd0286c0083:/var/www# echo "Este arquivo foi criado dentro de um volume" > novo-arquivo.txtCOPIAR CÓDIGO
```

Ao acessarmos o nosso Desktop, o arquivo estará lá, também com a mensagem escrita. E ao remover o container, a sua camada de escrita é removida, mas os arquivos continuam no nosso Desktop.

Então, o uso de volumes é importante para salvarmos os nossos dados fora do container, e esses volumes sempre estarão atrelados ao Docker Host. No caso acima, atrelamos o volume com o Desktop, mas podemos atrelar com um lugar mais seguro, salvando os dados do banco de dados nele, logs, e até mesmo o código fonte, coisa que faremos no próximo vídeo.

\[QUIS] Containers não guardam nenhum dado (são read-only) e por isso precisamos de volumes. Containers não são necessariamente read-only, como já vimos nesse curso. Você pode guardar dados dentro do container desde que não apague ele após o uso.

Muitas vezes removemos os containers após o uso. Volumes são usados para os dados que não devem ser removidos. Correto, é muito comum usar o container e apagá-lo após seu uso. Dessa forma também são removidos os dados desse container e aí entram os volumes que permitem salvar dados fora do container.

O uso dos volumes simplifica a execução pois não precisamos mais de imagens. Errado, as imagens continuam sendo utilizadas e são necessárias.

Um volume fica salvo: Na Imagem Errado, pois o volume fica no Docker Host. Lembrando, imagens são read-only e não guardam informação após serem construídas.

No Docker Host Correto, o volume fica no Docker Host. Ou seja, fica salvo no computador onde a Docker Engine está rodando.

No Container Errado, pois o volume fica no Docker Host. O container possui apenas o "Link" para o volume.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Já vimos que o que escrevemos no volume (pasta /var/www do container) aparece na pasta configurada da nossa máquina local, que no vídeo anterior foi o Desktop. Mas podemos pensar o contrário, ou seja, tudo o que escrevemos no Desktop será acessível na pasta /var/www do container.

Isso nos dá a possibilidade de implementar localmente um código de uma linguagem que não está instalada na nossa máquina, e colocá-lo para compilar e rodar dentro do container. Se o container possui Node, Java, PHP, seja qual for a linguagem, não precisamos tê-los instalados na nossa máquina, nosso ambiente de desenvolvimento pode ser dentro do container.

É isso que faremos, pegaremos um código nosso, que está na nossa máquina, e colocaremos para rodar dentro do container, utilizando essa técnica com volumes.

Rodando código em um container Para isso, vamos usar um exemplo escrito Node.js, que pode ser baixado aqui. Até podemos executar esse código na nossa máquina, mas temos que instalar o Node na versão certa em que o desenvolvedor implementou o código.

Agora, como fazemos para criar um container, que irá pegar e rodar esse código Node que está na nossa máquina? Vamos utilizar os volumes. Então, vamos começar a montar o comando.

Primeiramente, como vamos rodar um código em Node.js, precisamos utilizar a sua imagem:

```bash
docker run node
```

Além disso, precisamos criar um volume, que faça referência à pasta do código no nosso Desktop:

```bash
docker run -v "C:\Users\Alura\Desktop\volume-exemplo:/var/www" node
```

Agora, para iniciar o seu servidor, executamos o comando npm start. Para executar um comando dentro do container, podemos iniciá-lo no modo interativo ou passá-lo no final do docker run:

```bash
docker run -v "C:\Users\Alura\Desktop\volume-exemplo:/var/www" node npm start
```

Por fim, esse servidor roda na porta 3000, então precisamos linkar essa porta a uma porta do nosso computador, no caso a 8080. O comando ficará assim:

```bash
docker run -p 8080:3000 -v "C:\Users\Alura\Desktop\volume-exemplo:/var/www" node npm start
```

Executado o comando, recebemos um erro. Nele podemos verificar a seguinte linha:

```bash
npm ERR! enoent ENOENT: no such file or directory, open '/package.json'
```

Isto é, o package.json não foi encontrado, mas ele está dentro da pasta do código. O que acontece é que o container não inicia já dentro da pasta /var/www, e sim em uma pasta determinada pelo próprio container. Por exemplo, se a imagem é baseada no Ubuntu, o container iniciar no root.

Então devemos especificar que o comando npm start deve ser executado dentro da pasta /var/www. Para isso, vamos passar a flag -w (Working Directory), para dizer em qual diretório o comando deve ser executado, a pasta /var/www:

```bash
docker run -p 8080:3000 -v "C:\Users\Alura\Desktop\volume-exemplo:/var/www" -w "/var/www" node npm start
```

Agora, ao acessar a porta 8080 no navegador, vemos uma página exibindo a mensagem Eu amo Docker!. E para testar que está mesmo funcionando, podemos editar o arquivo index.html localmente, salvá-lo e ao recarregar a página no navegador, a nova mensagem é exibida! Ou seja, podemos criar um ambiente de desenvolvimento todo baseado em containers, o que ainda facilita o trabalho da nossa equipe, já que se todos utilizarem o container, todos terão o mesmo ambiente de desenvolvimento.

Melhorando o comando Por fim, sabemos que podemos executar o Docker de qualquer local da nossa máquina, então podemos executar o comando que fizemos dentro da pasta do nosso projeto. Fazendo isso, podemos melhorar esse comando com o auxílio da interpolação de comandos, já que o comando pwd retorna o nosso diretório atual:

```bash
alura@alura-estudio-03:~$ cd Desktop/volume-exemplo/
alura@alura-estudio-03:~/Desktop/volume-exemplo$ pwd
/home/alura/Desktop/volume-exemplo
```

Assim, ao invés de passar o diretório físico para dentro do comando docker run, podemos utilizar a interpolação de comandos, e interpolar o comando pwd, assim a sua saída será capturada e inserida dentro do docker run:

```bash
alura@alura-estudio-03:~$ cd Desktop/volume-exemplo/
alura@alura-estudio-03:~/Desktop/volume-exemplo$ pwd
/home/alura/Desktop/volume-exemplo
alura@alura-estudio-03:~/Desktop/volume-exemplo$ docker run -p 8080:3000 -v "$(pwd):/var/www" -w "/var/www" node npm start
```

Assim, vimos como rodar um código local, que está na nossa máquina, dentro de um container, utilizando a tecnologia dos volumes, linkando a nossa pasta local com uma pasta do container, criando assim um ambiente de desenvolvimento todo baseado em containers.

\[QUIS] Qual dos comandos abaixo configura o volume do diretório /var/www do container para C:\logs do Host? docker run -v "C:\logs:/var/www" ubuntu Correto, usando a flag -v seguindo pelo CAMINHO\_HOST:CAMINHO\_CONTAINER.

\[QUIS] Flavio é um programador com muita experiência no mundo Javascript, porém agora resolveu se aventurar no mundo do Docker. Ao pensar em como iria organizar os caminhos dos volumes em sua máquina e container, ele executou o comando docker inspect. Abaixo temos um pedaço da saída do comando docker inspect ID\_DO\_CONTAINER no terminal de Flavio, sobre a saída abaixo é verdade que:

```bash
"Mounts": [
    {
        "Type": "volume",
        "Name": "5e1cbfd48d07284680552e56087c9d5196659600ccd6874bfa3831b51ddd0576",
        "Source": "/home/Flavio/Desktop/volumes/caminho/_data",
        "Destination": "/var/opt",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
]
```

"/var/opt" pertence ao container e será escrito no caminho "/home/Flavio/Desktop/volumes/caminho/\_data" em nossa máquina. Correto! "/var/opt" pertence ao container enquanto "/home/Flavio/Desktop/volumes/caminho/\_data" pertence à máquina e irá armazenar "/var/opt".

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Já trabalhamos com a imagem do ubuntu, hello-world, dockersamples/static-site e por fim do node, mas até agora não criamos a nossa própria imagem, para podermos distribuir para as outras pessoas. Então é isso que faremos nesta aula.

No começo do treinamento, foi comentado que a imagem é como se fosse uma receita de bolo. Então, para criarmos a nossa própria imagem, temos que criar a nossa receita de bolo, o Dockerfile, ensinando o Docker a criar uma imagem a partir da nossa aplicação, para que ela seja utilizada em outros locais.

Montando o Dockerfile Então, no nosso projeto, devemos criar o arquivo Dockerfile, que nada mais é do que um arquivo de texto. Ele pode ter qualquer nome, porém nesse caso ele também deve possuir a extensão .dockerfile, por exemplo node.dockerfile, mas vamos manter o nome padrão mesmo.

Geralmente, montamos as nossas imagens a partir de uma imagem já existente. Nós podemos criar uma imagem do zero, mas a prática de utilizar uma imagem como base e adicionar nela o que quisermos é mais comum. Para dizer a imagem-base que queremos, utilizamos a palavra FROM mais o nome da imagem.

Como o nosso projeto precisa do Node.js, vamos utilizar a sua imagem:

```bash
FROM node
```

Além disso, podemos indicar a versão da imagem que queremos, ou utilizar o latest, que faz referência à versão mais recente da imagem. Se não passarmos versão nenhuma, o Docker irá assumir que queremos o latest, mas vamos deixar isso explícito:

```bash
FROM node:latest
```

Outra instrução que é comum colocarmos é quem cuida, quem criou a imagem, através do MAINTAINER:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
```

Agora, especificamos o que queremos na imagem. No caso, queremos colocar o nosso código dentro da imagem, então utilizarmos o COPY. Como queremos copiar tudo o que está dentro da pasta, vamos utilizar o . para copiar tudo que está na pasta do arquivo Dockerfile, e vamos copiar para /var/www, do exemplo da aula anterior:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
COPY . /var/www
```

No projeto, já temos as suas dependências dentro da pasta node\_modules, mas não queremos copiar essa pasta para dentro do container, pois elas podem estar desatualizadas, quebradas, então queremos que a própria imagem instale as dependências para nós, rodando o comando npm install. Para executar um comando, utilizamos o RUN:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
COPY . /var/www
RUN npm install
```

Agora, deletamos a pasta node\_modules, para ela não ser copiada para o container. Além disso, toda imagem possui um comando que é executado quando a mesma inicia, e o comando que utilizamos na aula anterior foi o npm start. Para isso, utilizamos o ENTRYPOINT, que executará o comando que quisermos assim que o container for carregado:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
COPY . /var/www
RUN npm install
ENTRYPOINT npm start
```

Também podemos passar o comando como se fosse em um array, por exemplo \["npm", "start"], ambos funcionam.

Falta colocarmos a porta em que a aplicação executará, a porta em que ela ficará exposta. Para isso, utilizamos o EXPOSE:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
COPY . /var/www
RUN npm install
ENTRYPOINT ["npm", "start"]
EXPOSE 3000
```

Por fim, falta dizermos onde os comandos rodarão, pois eles devem ser executados dentro da pasta /var/www. Então, através do WORKDIR, assim que copiarmos o projeto, dizemos em qual diretório iremos trabalhar;

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE 3000
```

Com isso, finalizamos o Dockerfile, baseado no comando que fizemos na aula anterior:

```bash
alura@alura-estudio-03:~/Desktop/volume-exemplo$ docker run -p 8080:3000 -v "$(pwd):/var/www" -w "/var/www" node npm start
```

Resta agora criar a imagem.

Criando a imagem Para criar a imagem, precisamos fazer o seu build através do comando docker build, comando utilizado para buildar uma imagem a partir de um Dockerfile. Para configurar esse comando, passamos o nome do Dockerfile através da flag -f:

```bash
alura@alura-estudio-03:~/Desktop/volume-exemplo$ docker build -f Dockerfile
```

Como o nome do nosso Dockerfile é o padrão, poderíamos omitir esse parâmetro, mas se o nome for diferente, por exemplo node.dockerfile, é preciso especificar, mas vamos deixar especificado para detalharmos melhor o comando.

Além disso, passamos a tag da imagem, o seu nome, através da flag -t. Já vimos que para imagens não-oficiais, colocamos o nome no padrão NOME\_DO\_USUARIO/NOME\_DA\_IMAGEM, então é isso que faremos, por exemplo:

```bash
alura@alura-estudio-03:~/Desktop/volume-exemplo$ docker build -f Dockerfile -t douglasq/node
```

E agora dizemos onde está o Dockerfile. Como já estamos rodando o comando dentro da pasta volume-exemplo, vamos utilizar o ponto (.);

```bash
alura@alura-estudio-03:~/Desktop/volume-exemplo$ docker build -f Dockerfile -t douglasq/node .
```

Ao executar o comando, podemos perceber que cada instrução executada do nosso Dockerfile possui um id. Isso por que para cada passo o Docker cria um container intermediário, para se aproveitar do seu sistema de camadas. Ou seja, cada instrução gera uma nova camada, que fará parte da imagem final, que nada mais é do que a imagem-base com vários containers intermediários em cima, sendo que cada um desses containers representa um comando do Dockerfile.

Assim, se um dia a imagem precisar ser alterada, somente o container referente à instrução modificada será alterado, com as outras partes intermediárias da imagem já prontas.

Criando um container a partir da nossa imagem Agora que já temos a imagem criada, podemos criar um container a partir dela:

```bash
docker run -d -p 8080:3000 douglasq/node
```

Ao acessar o endereço da porta no navegador, vemos a página da nossa aplicação. No Dockerfile, também podemos criar variáveis de ambiente, utilizando o ENV. Por exemplo, para criar a variável PORT, para dizer em que porta a nossa aplicação irá rodar, fazemos:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
ENV PORT=3000
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE 3000
```

E no próprio Dockerfile, podemos utilizar essa variável:

```bash
FROM node:latest
MAINTAINER Douglas Quintanilha
ENV PORT=3000
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE $PORT
```

E como modificamos o Dockerfile, precisamos construir a nossa imagem novamente e podemos perceber que dessa vez o comando é bem mais rápido, já que quase todas as camadas estão cacheadas pelo Docker.

Agora que criamos a imagem, vamos disponibilizá-la para outras pessoas. E é isso que veremos no próximo vídeo.

\[QUIS] No primeiro dia de trabalho de Fernando, ele encontrou um Dockerfile chamado ns.dockerfile:

```bash
FROM node:latest
MAINTAINER Ubuntu
COPY . /var/www
WORKDIR /var/www
RUN npm install
ENTRYPOINT npm start
EXPOSE 3000
```

Marque as alternativas verdadeiras sobre o Dockerfile encontrado por Fernando: Correto. A imagem se baseia na imagem node. Inclusive o container se baseará sempre na última versão da imagem. Correto. Todo o conteúdo que será copiado para a imagem ficará dentro da pasta /var/www.

Em MAINTAINER indicamos a versão do sistema operacional que utilizaremos. Completamente errado! Nela indicamos nome do responsável em manter o container. Nesse sentido, o nome Ubuntu foi utilizado mais para confundir do que para explicar, ao menos que o nome do responsável seja Ubuntu.

EXPOSE indica quanto tempo o container ficara no ar. Errado. Ela indica a porta de acesso do container.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Já criamos a imagem, mas por enquanto ela só está na nossa máquina local. Para disponibilizar a imagem para outras pessoas, precisamos enviá-la para o Docker Hub.

O primeiro passo é criar a nossa conta. Com ela criada, no terminal nós executamos o comando docker login e digitamos o nosso login e senha que acabamos de criar.

Após isso, basta executar o comando docker push, passando para ele a imagem que queremos subir, por exemplo:

```bash
docker push douglasq/node
```

Esse comando pode demorar um pouco, mas terminada a sua execução, podemos ver que várias mensagens Mounted from library/node, ou seja, o Docker já sabe que essas camadas podem ser reaproveitadas da imagem do node, então não tem o porquê dessas camadas subirem também, então só as camadas diferentes são enviadas para o Docker Hub.

Mais uma vantagem em se trabalhar com camadas, o Layered File System, pois até na hora de fazer o upload, só é feito das camadas diferentes, as outras são referenciadas da imagem-base que estamos utilizando, no caso a do node.

Por fim, ao acessar a nossa conta do Docker Hub, podemos ver que a imagem está lá. Para baixá-la, podemos utilizar o comando docker pull:

```bash
docker pull douglasq/node
```

Esse comando somente baixa a imagem, sem criar nenhum container acima dela.

Então, esse é um jeito de simples de compartilharmos uma imagem com outras pessoas, através do Docker Hub. A imagem é disponibilizada em um repositório público, mas também podemos disponibilizar em repositórios privados, que no momento da criação do curso, cada usuário pode criar um repositório privado gratuitamente.

\[QUIS] Guilherme Nicolau recebeu as seguintes instruções para criação de um docker container:

Deve instalar o mysql da última imagem disponível Os dados iniciais devem ser copiados para a pasta /etc/sinc O diretório de trabalho deve ser /etc/sinc/plen A porta de comunicação deve ser 1711 O comando de entrada chmod 755 /etc/sinc Marque a opção que possui a configuração de um Dockerfile condizente com a especificação passada para Guilherme:

FROM mysql:latest MAINTAINER Guilherme Nicolau COPY . /etc/sinc WORKDIR /etc/sinc/plen ENTRYPOINT chmod 755 /etc/sinc EXPOSE 1711

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Neste capítulo, veremos como funciona a rede, e como fazemos para interligar diversos containers no Docker. Normalmente uma aplicação é composta por diversas partes, sejam elas o load balancer/proxy, a aplicação em si, um banco de dados, etc. Quando estamos trabalhando com containers, é bem comum separarmos cada uma dessas partes em um container específico, para cada container ficar com somente uma única responsabilidade.

Mas se temos uma parte da nossa aplicação em cada container, como podemos fazer para essas partes falarem entre elas? Pois para a nossa aplicação funcionar como um todo, os containers precisam trocar dados entre eles.

Redes com Docker A boa notícia é que no Docker, por padrão, já existe uma default network. Isso significa que, quando criamos os nossos containers, por padrão eles funcionam na mesma rede:

Docker Host ----> Default Network v \[172.168.0.1] \[172.168.0.2] \[172.168.0.3]

Para verificar isso, vamos subir um container com Ubuntu:

```bash
docker run -it ubuntu
```

m outro terminal, vamos verificar o id desse container através do comando docker ps, e com ele em mãos, vamos passá-lo para o comando docker inspect. Na saída desse comando, em NetworkSettings, vemos que o container está na rede padrão bridge, rede em que ficam todos os containers que criamos.

Voltando ao terminal do container, se executarmos o comando hostname -i vemos o IP atribuído a ele pela rede local do Docker:

```bash
root@973feeeeb1df:/# hostname -i
172.17.0.2
```

Então, dentro dessa rede local, os containers podem se comunicar através desses IPs. Para comprovar isso, vamos deixar esse container rodando e criar um novo:

```bash
docker run -it ubuntu
```

E vamos verificar o seu IP:

```bash
root@dd316a9f585f:/# hostname -i
172.17.0.3
```

Agora, no primeiro container, vamos instalar o pacote iputils-ping para podermos executar o comando ping para verificar a comunicação entre os containers:

```bash
root@973feeeeb1df:/# apt-get update && apt-get install iputils-ping
```

Após o término da instalação, executamos o comando ping, passando para ele o IP do segundo container. Para interromper o comando, utilizamos o atalho CTRL + C:

```bash
root@973feeeeb1df:/# ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.180 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.133 ms
64 bytes from 172.17.0.3: icmp_seq=3 ttl=64 time=0.148 ms
^C
--- 172.17.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.133/0.153/0.180/0.024 ms
```

Assim, podemos ver que os containers estão conseguindo se comunicar entre eles.

Comunicação entre containers utilizando os seus nomes Então, o Docker criar uma rede virtual, em que todos os containers fazem parte dela, com os IPs automaticamente atribuídos. Mas quando os IPs são atribuídos, cada hora em que subirmos um container, ele irá receber um IP novo, que será determinado pelo Docker. Logo, se não sabemos qual o IP que será atribuído, isso não é muito útil quando queremos fazer a comunicação entre os containers. Por exemplo, podemos querer colocar dentro do aplicativo o endereço exato do banco de dados, e para saber exatamente o endereço do banco de dados, devemos configurar um nome para aquele container.

Mas nomear um container nós já sabemos, basta adicionar o --name, passando o nome que queremos na hora da criação do container, certo? Apesar de conseguirmos dar um nome a um container, a rede do Docker não permite com que atribuamos um hostname a um container, diferentemente de quando criamos a nossa própria rede.

Na rede padrão do Docker, só podemos realizar a comunicação utilizando IPs, mas se criarmos a nossa própria rede, podemos "batizar" os nossos containers, e realizar a comunicação entre eles utilizando os seus nomes:

Docker Host ----> My Network v \[nginx] \[APP] \[Database]

Isso não pode ser feito na rede padrão do Docker, somente quando criamos a nossa própria rede.

Criando a nossa própria rede do Docker Então, vamos criar a nossa própria rede, através do comando docker network create, mas não é só isso, para esse comando também precisamos dizer qual driver vamos utilizar. Para o padrão que vimos, de ter uma nuvem e os containers compartilhando a rede, devemos utilizar o driver de bridge.

Especificamos o driver através do --driver e após isso nós dizemos o nome da rede. Um exemplo do comando é o seguinte:

```bash
docker network create --driver bridge minha-rede
```

Agora, quando criamos um container, ao invés de deixarmos ele ser associado à rede padrão do Docker, atrelamos à rede que acabamos de criar, através da flag --network. Vamos aproveitar e nomear o container:

```bash
docker run -it --name meu-container-de-ubuntu --network minha-rede ubuntu
```

Agora, se executarmos o comando docker inspect meu-container-de-ubuntu, podemos ver em NetworkSettings o container está na rede minha-rede. E para testar a comunicação entre os containers na nossa rede, vamos abrir outro terminal e criar um segundo container:

```bash
docker run -it --name segundo-ubuntu --network minha-rede ubuntu
```

Agora, no segundo-ubuntu, instalamos o ping e testamos a comunicação com o meu-container-de-ubuntu:

```bash
root@00f93075d079:/# ping meu-container-de-ubuntu
PING meu-container-de-ubuntu (172.18.0.2) 56(84) bytes of data.
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=1 ttl=64 time=0.210 ms
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=2 ttl=64 time=0.148 ms
64 bytes from meu-container-de-ubuntu.minha-rede (172.18.0.2): icmp_seq=3 ttl=64 time=0.138 ms
^C
--- meu-container-de-ubuntu ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.138/0.165/0.210/0.033 ms
```

Conseguimos realizar a comunicação entre os containers utilizando somente os seus nomes. É como se o Docker Host, o ambiente que está rodando os containers, criasse uma rede local chamada minha-rede, e o nome do container será utilizado como se fosse um hostname.

Mas lembrando que só conseguimos fazer isso em redes próprias, redes que criamos, isso não é possível na rede padrão dos containers.

\[QUIS] Marque as alternativas verdadeiras a respeito de uma Rede Docker: Por padrão, os containers ficam na mesma rede com o nome bridge. Correto. Porém, a comunicação deve ser feita através de IP

Na rede padrão do Docker, só podemos realizar a comunicação utilizando IPs. Exatamente. Se criarmos nossa própria rede podemos usar seu nome no lugar do IP.

Com docker inspect ID\_DO\_CONTAINER podemos verificar a qual rede ele pertence. Exatamente, o comando inspect mostra vários detalhes sobre o container, entre eles o network. Nesse item, aparece o nome da nossa rede, IP do container, o gateway e entre outras informações.

A rede padrão deve ser habilitado explicitamente para funcionar. Errado, pois já está habilitado por padrão.

Emanuelle decidiu criar uma nova rede para que pudesse realizar a comunicação entre containers através dela. Então, ela executou o seguinte comando:

```bash
docker network create --driver bridge local
```

Marque as alternativas verdadeiras a respeito do comando executado por Emanuelle.

A instrução create não é opcional. Correto, sem ela não será possível criar a rede.

Cria uma rede local chamada bridge. Falsa. bridge é o driver utilizado, a rede criada se chama de local.

Utiliza o driver local. Falsa. O driver utilizado é o bridge, a rede criada se chama de local.

O parâmetro --driver indica qual driver será utilizado durante a criação da rede local. Correto.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Para praticar o que vimos sobre redes no Docker, vamos criar uma pequena aplicação que se conectará ao banco de dados, utilizando tudo o que vimos no vídeo anterior.

O que vamos fazer é utilizar a aplicação alura-books, que irá pegar os dados de um banco de dados de livros e exibi-los em uma página web. É uma aplicação feita em Node.js e o banco de dados é o MongoDB.

Pegando dados de um banco em um outro container Então, primeiramente vamos baixar essas duas imagens, a imagem douglasq/alura-books na versão cap05 e a imagem mongo:

```bash
docker pull douglasq/alura-books:cap05
docker pull mongo
```

Na imagem douglasq/alura-books, não há muito mistério. Ela possui o arquivo server.js, que carrega algumas dependências e módulos que são instalados no momento em que rodamos a imagem. Esse arquivo carrega também as configurações do banco, que diz onde o banco de dados estará em execução, no caso o seu host será meu-mongo, e o database, com nome de alura-books. Então, quando formos rodar o container de MongoDB, seu nome deverá ser meu-mongo. Além disso, o arquivo realiza a conexão com o banco, configura a porta que será utilizada (3000) e levanta o servidor .

No Dockerfile da imagem, também não há mistério, é basicamente o que vimos no vídeo anterior. Por fim, temos as rotas, que são duas: a rota /, que carrega os livros e os exibe na página, e a rota /seed, que salva os livros no banco de dados.

Caso queira, você pode baixar [aqui](https://s3.amazonaws.com/caelum-online-public/646-docker/05/projetos/alura-docker-cap05.zip) o código da versão cap05 da imagem alura-books.

Visto isso, já podemos subir a imagem:

```bash
docker run -d -p 8080:3000 douglasq/alura-books:cap05
```

Ao acessar a página http://localhost:8080/, nenhum livro nos é exibido, pois além de não termos levantado o banco de dados, nós não salvamos nenhum dado nele. Então, vamos excluir esse container e subir o container do MongoDB, lembrando que o seu nome deve ser meu-mongo, e vamos colocá-lo na rede que criamos no vídeo anterior:

```bash
docker run -d --name meu-mongo --network minha-rede mongo
```

Com o banco de dados rodando, podemos subir a aplicação do mesmo jeito que fizemos anteriormente, mas não podemos nos esquecer que ele deve estar na mesma rede do banco de dados, logo vamos configurar isso também:

```bash
docker run --network minha-rede -d -p 8080:3000 douglasq/alura-books:cap05
```

Agora, acessamos a página http://localhost:8080/seed/ para salvar os livros no banco de dados. Após isso, acessamos a página http://localhost:8080/ e vemos os dados livros são extraídos do banco e são exibidos na página. Para provar isso, podemos parar a execução do meu-mongo e atualizar a página, veremos que nenhum livro mais será exibido.

Então, esse foi um exemplo para praticar a comunicação entre containers, sempre lembrando que devemos colocá-los na mesma rede. Na próxima aula, veremos um jeito de orquestrar melhor diversos containers e automatizar esse processo de levantá-los e configurá-los, ao invés de fazer tudo na mão.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

No último video usei o comando docker pull douglasq/alura-books:cap05 sem ter explicado antes. Fique tranquilo pois esse comando faz nada mais do que baixar, sem rodar, a imagem desejada do Docker Hub :)

Por exemplo, para baixar a imagem ubuntu do Docker Hub você pode usar:

```bash
docker pull ubuntu
```

Isso é diferente do comando run, que baixa a imagem (se não existe local) e depois cria e roda o container. O pull apenas baixa!

Para baixar uma imagem de um usuário especifico vimos a sintaxe:

```bash
docker pull NOME_USUARIO/NOME_IMAGEM
```

Além disso, uma imagem pode ter um tag que serve para pegar uma determinada versão dessa imagem. Alias, você já viu a tag :latest e no video eu usei a tag :cap05, por exemplo:

```bash
docker pull douglasq/alura-books:cap05
```

Isso baixará a versão (ou tag) cap05 da imagem alura-books do usuário douglasq.

\[QUIS] Eric, para testar sua amiga Paula, executou o seguinte comando:

```bash
docker run -d --name meu-mongo --network minha-rede mongo
```

Em seguida, pediu para que Paula detalhasse o comando executado.

Nesse contexto, marque as afirmativas verdadeiras a respeito do comando executado: Sobe um container na rede minha-rede. Correto.

Executa o container na rede mongo. A rede é minha-rede.

Executa o container em modo detached. Correto.

É um comando inválido, pois o parâmetro -d não é suportado. Falso, o comando -d é suportado sim, ele realiza uma execução detached do container. Dessa maneira, podemos continuar a usar o terminal.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Nesta aula, estudaremos uma tecnologia chamada Docker Compose, que nos auxiliará a lidar com múltiplos containers simultaneamente.

Na aula anterior, para subir a aplicação alura-books, foi necessário subirmos dois containers, executando os seguintes comandos:

```bash
docker run -d --name meu-mongo --network minha-rede mongo
docker run --network minha-rede -d -p 8080:3000 douglasq/alura-books:cap05
```

Isso tudo depois de termos construído pelo menos a imagem douglasq/alura-books

O problema Esses dois comandos criam dois containers, mas subindo eles desse jeito manual, é muito comum esquecermos de passar alguma flag, ou subir o container na ordem errada, sem a devida rede, ou seja, é um trabalho muito manual e facilmente suscetível a erros, isso com somente dois containers.

Esse modo de subir os containers na mão é bom se quisermos criar um ambiente rapidamente, ou são poucos containers, mas quando a aplicação começa a crescer, temos que digitar muitos comandos.

Funcionamento das aplicações em geral Na vida real, sabemos que a aplicação é maior que somente dois containers, geralmente temos dois, três ou mais containers para segurar o tráfego da aplicação, distribuindo a carga. Além disso, temos que colocar todos esses containers para se comunicar com o banco de dados em um outro container, mas quanto maior a aplicação, devemos ter mais de um container para o banco também.

E claro, se temos três aplicações rodando, não podemos ter três endereços diferentes, então nesses casos utilizamos um Load Balancer em um outro container, para fazer a distribuição de carga quando tivermos muitos acessos. Ele recebe as requisições e distribui para uma das aplicações, e ele também é muito utilizado para servir os arquivos estáticos, como imagens, arquivos CSS e JavaScript. Assim, a nossa aplicação controla somente a lógica, as regras de negócio, com os arquivos estáticos ficando a cargo do Load Balancer:

```
                      Aplicação
```

Load Balancer \[NODEjs] \[NGINX] ------------> \[NODEjs] <---------------> \[Banco] v \[NODEjs] \[JS] \[IMG] \[CSS3]

Se formos seguir esse diagrama, teríamos que criar cinco containers na mão, e claro, cada container com configurações e flags diferentes, além de termos que nos preocupar com a ordem em que vamos subi-los.

Docker Compose Ao invés de subir todos esses containers na mão, o que vamos fazer é utilizar uma tecnologia aliada do Docker, chamada Docker Compose, feito para nos auxiliar a orquestrar melhor múltiplos containers. Ele funciona seguindo um arquivo de texto YAML (extensão .yml), e nele nós descrevemos tudo o que queremos que aconteça para subir a nossa aplicação, todo o nosso processo de build, isto é, subir o banco, os containers das aplicações, etc.

Assim, não precisamos ficar executando muitos comandos no terminal sem necessidade. E esse será o foco desta aula, montar uma aplicação na estrutura descrita anteriormente na imagem, que é uma situação comum no nosso dia-a-dia.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

O código do projeto pode ser baixado [aqui](https://s3.amazonaws.com/caelum-online-public/646-docker/06/projetos/alura-docker-cap06.zip)

Para começarmos a entender como funciona o Docker Compose, primeiramente vamos entender como funciona a aplicação que utilizaremos como base. É uma aplicação bem semelhante à utilizada na aula anterior, com o mesmo servidor, rotas e banco de dados. De novidade, é que agora precisamos criar o NGINX, que é mais um container que devemos subir.

Então, ou utilizamos a imagem nginx, ou criamos a nossa própria. Como vamos configurar o NGINX para algumas coisas específicas, como lidar com os arquivos estáticos, vamos criar a nossa própria imagem, por isso que na aplicação há o nginx.dockerfile:

```bash
FROM nginx:latest
MAINTAINER Douglas Quintanilha
COPY /public /var/www/public
COPY /docker/config/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80 443
ENTRYPOINT ["nginx"]
# Parametros extras para o entrypoint
CMD ["-g", "daemon off;"]
```

Nesse arquivo, nós utilizamos a última versão disponível da imagem do nginx como base, e copiamos o conteúdo da pasta public, que contém os arquivos estáticos, e um arquivo de configuração do NGINX para dentro do container. Além disso, abrimos as portas 80 e 443 e executa o NGINX através do comando nginx, passando os parâmetros extras -g e daemon off.

Por fim, vamos ver um pouco sobre o arquivo de configuração do NGINX, para entendermos um pouco como o load balancer está funcionando.

No arquivo nginx.conf, dentro server, está a parte que trata de servir os arquivos estáticos. Na porta 80, no localhost, em /var/www/public, ele será responsável por servir as pastas css, img e js. E todo resto, que não for esses três locais, ele irá jogar para o node\_upstream.

No node\_upstream, é onde ficam as configurações para o NGINX redirecionar as conexões que ele receber para um dos três containers da nossa aplicação. O redirecionamento acontecerá de forma circular, ou seja, a primeira conexão irá para o primeiro container, a segunda irá para o segundo container, a terceira irá para o terceiro container, na quarta, começa tudo de novo, e ela vai para o primeiro container e assim por diante.

Isso já está tudo pronto, basta baixarmos o código da imagem e da aplicação [aqui](https://s3.amazonaws.com/caelum-online-public/646-docker/06/projetos/alura-docker-cap06.zip).

Agora, no próximo vídeo, escreveremos o responsável por orquestrar a subida de cada uma dessas partes da nossa aplicação, o docker-compose.yml.

\[QUIS] Vejamos um exemplo de um dockerfile que utiliza a última imagem disponível do nginx:

```bash
FROM nginx:latest
MAINTAINER Douglas Quintanilha
COPY /public /var/www/public
COPY /docker/config/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80 443
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

Marque as alternativas verdadeiras sobre o arquivo: Utilizamos a última versão disponível da imagem do nginx como base

Copiamos o conteúdo da pasta public, que contém os arquivos estáticos, e um arquivo de configuração do NGINX para dentro do container.

É executado o comando nginx, passando os parâmetros extras -g e daemon off.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Para utilizar o Docker Compose, devemos criar o seu arquivo de configuração, o docker-compose.yml, na raiz do projeto. Em todo arquivo de Docker Compose, que é uma espécie de receita de bolo para construirmos as diferentes partes da nossa aplicação, a primeira coisa que colocamos nele é a versão do Docker Compose que estamos utilizando:

```bash
version: '3'
```

Estamos utilizando a versão 3 pois é a versão mais recente no momento da criação do treinamento. O YAML lembra um pouco o JSON, mas ao invés de utilizar as chaves para indentar o código, ele utiliza espaços.

Agora, começamos a descrever os nossos serviços, os nossos services:

```bash
version: '3'
services:
```

Um serviço é uma parte da nossa aplicação. Lembrando do nosso diagrama:

```
                      Aplicação
```

Load Balancer \[NODEjs] \[NGINX] ------------> \[NODEjs] <---------------> \[Banco] v \[NODEjs] \[JS] \[IMG] \[CSS3]

Temos NGINX, três Node, e o MongoDB como serviços. Logo, se queremos construir cinco containers, vamos construir cinco serviços, cada um deles com um nome específico.

Então, vamos começar construindo o NGINX, que terá o nome nginx:

```bash
version: '3'
services:
    nginx:
```

Em cada serviço, devemos dizer como devemos construí-lo, como devemos fazer o seu build:

```bash
version: '3'
services:
    nginx:
        build:
```

O serviço será construído através de um Dockerfile, então devemos passá-lo onde ele está. E também devemos passar um contexto, para dizermos a partir de onde o Dockerfile deve ser buscado. Como ele será buscado a partir da pasta atual, vamos utilizar o ponto:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
```

Construída a imagem, devemos dar um nome para ela, por exemplo douglasq/nginx:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
```

E quando o Docker Compose criar um container a partir dessa imagem, vamos dizer que o seu nome será nginx:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
```

Sabemos também que o NGINX trabalha com duas portas, a 80 e a 443. Como não estamos trabalhando com HTTPS, vamos utilizar somente a porta 80, e no próprio arquivo, podemos dizer para qual porta da nossa máquina queremos mapear a porta 80 do container. Vamos mapear para a porta de mesmo número da nossa máquina:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
```

No YAML, toda vez que colocamos um traço, significa que a propriedade pode receber mais de um item. Agora, para os containers conseguirem se comunicar, eles devem estar na mesma rede, então vamos configurar isso também. Primeiramente, devemos criar a rede, que não é um serviço, então vamos escrever do começo do arquivo, sem as tabulações:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"

networks:
```

O nome da rede será production-network e utilizará o driver bridge:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"

networks: 
    production-network:
        driver: bridge
```

Com a rede criada, vamos utilizá-la no serviço:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
        networks: 
            - production-network

networks: 
    production-network:
        driver: bridge
```

Isso é para construir o serviço do NGINX, agora vamos construir o serviço do MongoDB, com o nome mongodb. Como ele será construído a partir da imagem mongo, não vamos utilizar nenhum Dockerfile, logo não utilizamos a propriedade build. Além disso, não podemos nos esquecer de colocá-lo na rede que criamos:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
        networks: 
            - production-network

    mongodb:
        image: mongo
        networks: 
            - production-network

networks: 
    production-network:
        driver: bridge
```

```bash
Falta agora criarmos os três serviços em que ficará a nossa aplicação, node1, node2 e node3. Para eles, será semelhante ao NGINX, com Dockerfile alura-books.dockerfile, contexto, rede production-network e porta 3000:

version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
        networks: 
            - production-network

    mongodb:
        image: mongo
        networks: 
            - production-network

    node1:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-1
        ports:
            - "3000"
        networks: 
            - production-network

    node2:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-2
        ports:
            - "3000"
        networks: 
            - production-network

    node3:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-3
        ports:
            - "3000"
        networks: 
            - production-network

networks: 
    production-network:
        driver: bridge
```

Com isso, a construção dos nossos serviços está finalizada.

Ordem dos serviços Por último, quando subimos os containers na mão, temos uma ordem, primeiro devemos subir o mongodb, depois a nossa aplicação, ou seja, node1, node2 e node3 e após tudo isso subimos o nginx. Mas como que fazemos isso no docker-compose.yml?

Nós podemos dizer que os serviços da nossa aplicação dependem que um serviço suba antes deles, o serviço do mongodb:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
        networks: 
            - production-network

    mongodb:
        image: mongo
        networks: 
            - production-network

    node1:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-1
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

    node2:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-2
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

    node3:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-3
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

networks: 
    production-network:
        driver: bridge
```

Da mesma forma, dizemos que o serviço do nginx depende dos serviços node1, node2 e node3:

```bash
version: '3'
services:
    nginx:
        build:
            dockerfile: ./docker/nginx.dockerfile
            context: .
        image: douglasq/nginx
        container_name: nginx
        ports:
            - "80:80"
        networks: 
            - production-network
        depends_on: 
            - "node1"
            - "node2"
            - "node3"

    mongodb:
        image: mongo
        networks: 
            - production-network

    node1:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-1
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

    node2:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-2
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

    node3:
        build:
            dockerfile: ./docker/alura-books.dockerfile
            context: .
        image: douglasq/alura-books
        container_name: alura-books-3
        ports:
            - "3000"
        networks: 
            - production-network
        depends_on:
            - "mongodb"

networks: 
    production-network:
        driver: bridge
```

Assim, encerramos a configuração do docker-compose.yml. Vamos ver como subir a aplicação a partir desse arquivo no próximo vídeo.

\[QUIS] Quais das descrições podem ser responsabilidades do Docker Compose? Executar o build de vários containers.

Desligar os containers de maneira coordenada.

Manipular apenas um container. Errado, é possível gerencial vários containers, até mesmo num único arquivo de configuração.

Criar nova rede em qual os containers podem participar.

#### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ### ##\#

Se você utiliza Linux, provavelmente não conseguirá utilizar o Docker Compose, pois o mesmo não é instalado na instalação padrão. Para instalá-lo, primeiramente faça [este exercício](https://cursos.alura.com.br/course/docker-e-docker-compose/task/29559) e prossiga com o treinamento.

Com o docker-compose.yml pronto, podemos subir os serviços, mas antes devemos garantir que temos todas as imagens envolvidas neste arquivo na nossa máquina. Para isso, dentro da pasta do nosso projeto, executamos o seguinte comando:

```bash
$ cd Desktop/alura-docker-cap06/
~/Desktop/alura-docker-cap06$ sudo docker-compose build
```

Com os serviços criados, podemos subi-los através do comando docker-compose up. Esse comando irá seguir o que escrevemos no docker-compose.yml, ou seja, cria a rede, o container do MongoDB, os três containers da aplicação e o container do NGINX. Depois, são exibidos alguns logs, sendo que cada um dos containers fica com uma cor diferente, para podermos distinguir melhor.

Se tudo funcionou, quando acessarmos o NGINX, seremos redirecionados para algum dos containers da nossa aplicação. Configuramos a porta 80 para acessar o NGINX, então vamos acessar no navegador a página http://localhost:80/. Ao acessar a página, vemos a seguinte mensagem no log:

```bash
alura-books-1 | Exibindo a Home!
```

Ou seja, fomos redirecionados para o primeiro container. Ao atualizar a página, vemos no log que fomos redirecionados para o segundo container, ou seja, o load balancer está funcionando.

Vamos então alimentar o banco de dados acessando a página http://localhost:80/seed, e novamente acessar a página inicial, a http://localhost:80/, assim veremos os livros sendo exibidos.

Então, com um único comando, levantamos todos os containers, montamos o load balancer, servimos os arquivos estáticos, criamos o banco e colocamos todos eles na mesma rede, bem mais prático do que estávamos fazendo anteriormente.

Para parar a execução, utilizamos o atalho CTRL + C. E não somos obrigados a ficar vendo esses logs, podemos utilizar a já conhecida flag -d:

```bash
docker-compose up -d
```

E com o comando docker-compose ps, podemos ter uma visualização simples dos serviços que estão rodando:

```bash
           Name                        Command             State              Ports            
----------------------------------------------------------------------------------------------
alura-books-1                npm start                     Up      0.0.0.0:9022->3000/tcp      
alura-books-2                npm start                     Up      0.0.0.0:9021->3000/tcp      
alura-books-3                npm start                     Up      0.0.0.0:9023->3000/tcp      
aluradockercap06_mongodb_1   docker-entrypoint.sh mongod   Up      27017/tcp                   
nginx                        nginx -g daemon off;          Up      443/tcp, 0.0.0.0:88->80/tcp 
```

Agora que não estamos mais vendo os logs, como paramos os serviços? Para isso, utilizamos o comando docker-compose down. Esse comando para os containers e os remove.

E não é por que eles são serviços, que eles não tem um container por debaixo dos panos, então nós conseguimos interagir com os containers utilizando todos os comandos que já vimos no treinamento, por exemplo para testar a comunicação entre eles:

```bash
docker exec -it alura-books-1 ping alura-books-2
```

Mas também podemos utilizar o nome do serviço, não precisamos necessariamente utilizar o nome do container:

```bash
docker exec -it alura-books-1 ping node2
```

Assim conseguimos fazer a comunicação tanto com o nome do container quanto com o nome do serviço, pois os dois estão atrelados ao mesmo IP.

\[QUIS] Ana configurou o docker-compose.yml corretamente e subiu os containers. Depois de um tempo ela gostaria de reinicializá-los. Para tal, ela executa:

```bash
docker-compose down
docker-compose up
```

Estes comandos funcionam e não têm nada de errado, mas será que você consegue achar um atalho para isso?

Obs: Não vimos o comando na aula, mas basta executar o comando abaixo para descobrir a resposta:

```bash
docker-compose --help 
```

docker-compose restart
