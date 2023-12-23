---
description: >-
  Docker Swarm é uma ferramenta de orquestração de containers, que possibilita o
  usuário a gerenciar múltiplos containers distribuídos em diversas máquinas
  hosts.
---

# Introdução

O Swarm é um recurso do Docker que fornece funcionalidades de orquestração de contêiner, incluindo clustering nativo de hosts do Docker e agendamento de cargas de trabalho de contêineres.&#x20;

> Um grupo de hosts do Docker formam um cluster "Swarm".&#x20;

Para que o cluster Swarm esteja ativo e funcionando, eu tenho que ter pelo menos 51% dos managers funcionando.

Habilitando ferramentas para dimensionamento, rede, proteção e manutenção dos containers.

## **Nós gerenciadores e nós de trabalho**

Um swarm é composto por dois tipos de hosts de contêiner: nós "gerenciadores" e nós de "trabalho".&#x20;

> Todos os comandos CLI do Docker para controlar e monitorar um swarm devem ser executados em um de seus nós "gerenciadores".&#x20;

Os nós gerenciadores podem ser considerados os **"zeladores"** do Swarm - juntos, eles formam um grupo de consenso que mantém o reconhecimento do estado dos serviços em execução no swarm e o trabalho deles é garantir que o estado real do swarm seja sempre condizente com o estado desejado, conforme definido pelo desenvolvedor ou administrador.

Para ingressar em um swarm, um nó de trabalho deve usar um `"token de associação"` gerado pelo nó gerenciador quando o swarm foi inicializado

```bash
docker swarm init --advertise-addr ip
docker swarm join 'Valor disponibilizado pelo swarm init'
```

```bash
docker node ls --format "{{.Hostname}} {{.ManagerStatus}} docker node ls --format "{{.Node}} docker service ls --format "{{.Name}} {{.Replicas}}
docker node inspect node --pretty
```

## Conceitos

### Cluster

Cluster (do inglês cluster : 'grupo, aglomerado') consiste em computadores ligados que trabalham em conjunto, de modo que , em muitos aspectos, podem ser considerados como um único sistema. Computadores em cluster executam a mesma tarefa, controlado e programado por software Cada computador presente em cluster é conhecido como nó (node). Conjunto de computadores que trabalham em grupo de modo a ser visto como um sistema único.

Utilizamos clusters para ganhar mais poder computacional e maior confiabilidade orquestrando um número de máquinas de "baixo custo" para obter um maior poder de processamento.

### Node

Utilizamos o nome Node, ou nó, quando nos referimos a qualquer elemento computacional que faz parte de um cluster, seja ele um nó do tipo primário (master) ou um do tipo secundário (follower).

Iremos nos referir aos nossos "computadores¨ como Master/Node

Em um passado não tão distante, os termos _Master/Slave_ eram utilizados para se referir aos nós e as ações na qual eles desempenhavam. Em 2020 um forte movimento aconteceu para acabar com essa terminologia, uma vez que ela traz referências a escravidão. Não é sobre aguardar alguem ser ofendido. É sobre remover estes termos com base em coisas terríveis e desumanas.

Algumas alternativas a serem utilizadas para se referir aos nodes:

* Main / Secondary
* Conductor/ Follower
* Leader / Follower
* Host / Client
* Sender / Reciever
* Producer / Consumer
* Primary / Replica
* Primary / Secondary
* Manager / Worker

## Raft Consensus

Quando trabalhamos com sistemas distribuidos, precisamos de algum algorítmo para tratar da confiabilidade do cluster. Um dos algoritmos mais utilizados para este meio é o Raft Consensus.

O Docker Swarm implementa o algorítmo Raft Consensus para gerencia do estado global do cluster.

O nome Raft é uma palavra em inglês que quer dizer Jangada, a referênca é porque para montarmos uma jangada, necessitamos de, ao menos, três toras de madeira.

Consensus é uma palavra em ingles que quer dizer Consenso. É também um dos problemas fundamentais em sistemas distribuidos com tolerância a falhas. Involve multiplos servidores aceitando valores.

O site [The Secret Lives of Data](http://thesecretlivesofdata.com/raft/) possúi um ótimo guia sobre como ocorre o raft consensus.

Temos também uma página no github dedicado ao [raft](https://raft.github.io/)

Quando temos um sistema composto por um único nó, podemos dizer que este nó armazena um valor único, temos também o cliente que envia este valor para o servidor, neste ponto o cliente e o servidor entram em um "consensus", um valor é simples com um único nó.

Quando temos diversos servidores, como podemos chegar em um consenso ? esse é o problema de sistemas distribuidos.

Como chegamos em um concenso em multiplos nodes? Utilizando algoritmos como o Raft Consensus.

Pensando de uma maneira mais macro, o raft define os nós em três estados básicos:

* Follower
* Candidate
* Leader

Todos os nós começam em um estado de seguidor, se os seguidores não encontrarem um líder, eles se tornam candidatos.

O candidato requisita votos de outros nós e os nós respondem com o voto, o candidato vira um lider se ele tiver os votos de uma maioria dos nodes. Esse processo é chamado de Eleição do Líder

O problema básico evitado ao se utilizar o raft consensus é chamado de `split-brain` ou cérebro dividido, e isto acontece da seguinte maneira:

> Estamos falando apenas de nodes do tipo Master, ou primário.

O Raft Consensus tolera até `(N-1)/2` falhas, e precisa de um quórum de `(N/2)+1` para ser funcional.

> Tratamos apenas numeros inteiros.

Imagine que temos 2 nodes. Caso um dos nodes perca a comunicação (rede), como ele irá saber se:

* Ele está sem conectividade?
* O outro nó está sem conectividade?

Esta ação é chamada de `split-brain`, e o sistema se torna inoperante.

Vamos para o mesmo cenário com 3 nós, onde um node perde a comunicação (rede). O que ocorre neste momento é:

1. Os nodes começam a se perguntar (rede) se os outros nós estão operantes.
2. Os 2 nodes que estão operantes se comunicam e verificam que são a maioria `(N/2)+1` ou seja `(3/2)+1 = 2` e continuam se comunicando.
3. O nó que está inoperante verifica que ele não é a maioria, e para de responder.

Qual seria o problema que poderiamos encontrar caso o nó que ficou sem conectividade continue a operar? Teriamos duas versões das aplicações rodando, uma em cada lado dos servidores, com conteudos diferentes.

| Nós | Tolerância |
| --- | ---------- |
| 1   | 0          |
| 3   | 1          |
| 5   | 2          |
| 7   | 3          |
| 9   | 4          |

Não é comum termos mais de 5 nodes, uma vez que a comunicação entre os nós precisa ser feita de maneira rápida e eficiente, e ao aumentar o número de nodes fazemos com que o volume de replicação de dados aumente, tornando comum a utilização de `3 ou 5` nodes

Mais informações na [Documentação Oficial](https://docs.docker.com/engine/swarm/raft/)

## Criando o Cluster

Trabalharemos com as máquinas `master` `node-01` e `node-02`

1. Ligue as máquinas

```bash
vagrant up master node01 node02
```

2. Acesse a máquina master

```bash
vagrant ssh master
```

3. Para criar o cluster do Swarm, precisamos apenas de que o docker esteja instalado na máquina, podemos em seguida executar o comando para verificar seu endereço IP e iniciar o cluster swarm

```bash
ip -c -br a show enp0s8
docker swarm init --advertise-addr 10.20.20.100
```

{% hint style="info" %}
Será exibida uma mensagem informando que o Swarm foi iniciado e o node é um manager, bem como o token para adicionar mais nós ao Swarm.
{% endhint %}

> ```bash
> Swarm initialized: current node (sqtbdppo34ez4i4wsngbdeply) is now a manager.
>
> To add a worker to this swarm, run the following command:
>
>     docker swarm join --token SWMTKN-1-4n7u9o6cmhoizpx57umbp1g8nkh5zetuimx06k20nme64syy0t-1x0yu5ogxdlu2b0fs7fcnsc91 10.20.20.100:2377
>
> To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
> ```

4. Podemos ver novamente o token para adicionar novos nós ao cluster através do comando `docker swarm join-token <manager/worker>`

```bash
docker swarm join-token manager
docker swarm join-token worker
```

### Adicionando nós ao cluster.

1. Na máquina master, copie o token de `worker`

```bash
docker swarm join-token worker
```

2. Em um novo terminal, acesse a máquina `node01` e execute o comando copiado anteriormente

```bash
vagrant ssh node01
docker swarm join --token SWMTKN-1-4n7u9o6cmhoizpx57umbp1g8nkh5zetuimx06k20nme64syy0t-1x0yu5ogxdlu2b0fs7fcnsc91 10.20.20.100:2377
```

3. Repita o passo para a máquina `node02`

```bash
vagrant ssh node02
docker swarm join --token SWMTKN-1-4n7u9o6cmhoizpx57umbp1g8nkh5zetuimx06k20nme64syy0t-1x0yu5ogxdlu2b0fs7fcnsc91 10.20.20.100:2377
```

4. De volta a máquina master, vamos verificar se os nós foram adicionados corretamente.

```bash
docker node ls
```

> Verifique que o node que estamos atualmente conectados é informado com um `*` e podemos ver também quais nodes são `manager` ou  `leader`

```bash
ID                            HOSTNAME                    STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
sqtbdppo34ez4i4wsngbdeply *   master                      Ready     Active         Leader           20.10.7
o122d88a3dwxdkw6nyopgmqu7     node01                      Ready     Active                          20.10.7
tj57th3ri0zouv1f8ui90zv0n     node02.docker-dca.example   Ready     Active                          20.10.7
```

> os comandos `docker node` só podem ser executados em nodes do tipo `manager`

5. Podemos verificar também que uma rede com o driver `overlay` foi criada para comunicação do swarm

```bash
docker network ls
```

### Promovendo um node a Manager ou rebaixando a worker

Podemos promover um node a manager ou rebaixa-lo através do comando `docker node promote` e `docker node demote`

1. Na máquina master, vamos promover o node01 a manager.

```bash
docker node ls
docker node promote node01
docker node ls
```

> Note que seu `Manager Status` se tornou `Reachable`, ou seja, ele está disponível, caso a máquina esteja sem comunicação, ela ficará no estado de `Unreachable`

```bash
ID                            HOSTNAME                    STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
sqtbdppo34ez4i4wsngbdeply *   master                      Ready     Active         Leader           20.10.7
o122d88a3dwxdkw6nyopgmqu7     node01                      Ready     Active         Reachable        20.10.7
tj57th3ri0zouv1f8ui90zv0n     node02.docker-dca.example   Ready     Active                          20.10.7
```

{% hint style="info" %}
Caso a máquina fique offline veremos o nosso cluster entrar em estado de `split-brain` exibindo a seguinte mensagem ao executar o comando `docker node ls`
{% endhint %}

```bash
Error response from daemon: rpc error: code = Unknown desc = The swarm does not have a leader. It's possible that too few managers are online. Make sure more than half of the managers are online.
```

2. Vamos agora rebaixar nosso node a worker.

```bash
$ docker node ls
$ docker node demote node01
$ docker node ls
```

> Trabalharemos com apenas um manager pois como falamos na etapa do _**raft consensus**_, ter dois managers é pior do que ter apenas um.

## Private Registry

Quando trabalhamos com clusters como o docker swarm, precisamos configurar um private registry, porque fazer isto? temos alguns motivos:

* Docker Hub Rate Limiting
* Segurança
* Velocidade

## **Docker Hub Rate Limiting**

Quando utilizamos o serviço do DockerHub, temos um limite de requisição de 100 imagens de containers a cada seis horas por utilização anônima que podemos aumentar para 200 caso utilizemos uma conta gratuita.

#### **Segurança**

Muitas das vezes não queremos que nossas imagens sejam enviadas para um registry publico por conter algum tipo de informação/tecnologia sensível.

**Velocidade**

Quando temos um ambiente complexo, com diversos nodes fazendo o download de imagens diariamente, não queremos depender da nossa banda de internet. Um registry privado ganha velocidade neste quesito, uma vez que ele se comunica com o DockerHub

## Preparando nosso Registry

1. Primeiramente precisamos conectar via SSH e adicionar a máquina registry ao nosso cluster.&#x20;

> Caso precise, execute `docker swarm join-token worker` na máquina manager para copiar o comando de join

```bash
vagrant ssh registry
docker swarm join --token 
SWMTKN-1-0cfecsrgcdvuncxosbwpgu8yg674b4lur8643tizkeilnplhax-4wow83y7dw51vvy5d281347z1 10.20.20.100:2377
```

2. O próximo passo é adicionar o bloco de insecure registry a todas máquinas do nosso cluster.

```json
{
  "insecure-registries" : ["registry.docker-dca.example:5000"] 
}
```

{% hint style="info" %}
Podemos fazer isto executando o seguinte comando em todos os nodes
{% endhint %}

```bash
echo '{ "insecure-registries" : ["registry.docker-dca.example:5000"] }' | sudo tee /etc/docker/daemon.json ; sudo systemctl restart docker
```

> Em ambientes de produção, configuramos nosso registry como seguro e adicionamos a ele um certificado SSL, mais informações podem ser encontradas na [Documentação Oficial](https://docs.docker.com/registry/deploying/)

### Deploy do Registry

Agora que já configuramos os nodes podemos efetuar o deploy do nosso registry.

1. Execute o comando na máquina registry

```bash
docker container run -dit --name registry -p 5000:5000 registry:2
```

> Utilizamos a tag `registry:2` porque utilizamos o deploy versão `v2` do registry, que é a mais atual.

### Enviando Imagens

Para enviar imagens para nosso registry precisamos criar uma tag da imagem e efetuar o push.

> Podemos fazer isto a partir de qualquer máquina que tenha acesso a nosso registry.

```bash
docker image pull alpine
docker image tag alpine registry.docker-dca.example:5000/alpine
docker image push registry.docker-dca.example:5000/alpine
```

### Listando Imagens

O Docker Registry por padrão não possui nenhuma interface para visualizar as imagens, mas podemos através de um simples `curl` verificar os repositórios existentes.

```bash
curl http://registry.docker-dca.example:5000/v2/_catalog
```

> Teremos como resposta um json.

```json
{
  "repositories": ["alpine"]
}
```

Podemos também listar as tags acessando o endpoint `v2/<image_name>/tags/list`

```bash
curl http://registry.docker-dca.example:5000/v2/alpine/tags/list
```

```json
{
  "name":"alpine","tags":["latest"]
}
```

### Adicionando as imagens que iremos utilizar nos laboratórios

Agora que ja aprendemos como enviar images para nosso private registry, iremos fazer o download das imagens que utilizaremos nos próximos laboratórios

* `nginx`
* `mysql:5.7`
* `wordpress`
* `caiodelgadonew/docker-supermario`
* `traefik:v2.4`

Para facilitar nosso processo, iremos criar um pequeno script em bash para automatizar este processo.

```bash
vim images.sh
```

```shell
for image in 'nginx' 'mysql:5.7' 'wordpress' 'caiodelgadonew/docker-supermario' 'traefik:v2.4'
do
docker image pull $image
docker tag $image registry.docker-dca.example:5000/$image
docker push registry.docker-dca.example:5000/$image
done
```

1. Vamos executar o script

```bash
chmod +x images.sh
./images.sh
```

2. Após a finalização do script podemos visualizar nosso catalogo e verificar as imagens disponíveis

```bash
curl http://registry.docker-dca.example:5000/v2/_catalog
```

> Caso você tenha o `jq` instalado em sua máquina podemos visualizar de maneira mais agradável

```bash
curl http://registry.docker-dca.example:5000/v2/_catalog | jq
```

```json
{
  "repositories": [
    "alpine",
    "caiodelgadonew/docker-supermario",
    "mysql",
    "nginx",
    "traefik",
    "wordpress"
  ]
}
```

3. Podemos verificar as tags dos repositórios que não utilizaremos a tag latest como por exemplo o `mysql` e o `traefik`

```bash
curl http://registry.docker-dca.example:5000/v2/mysql/tags/list
curl http://registry.docker-dca.example:5000/v2/traefik/tags/list
```

### **\[QUIS]**

O Docker Swarm apresenta uma série de vantagens em relação ao Docker usado de maneira tradicional. Marque as alternativas que contém essas vantagens:&#x20;

1. O Docker Swarm necessita ao menos de uma rede local para identificar cada máquina que fará parte do cluster.
2. O Docker Swarm divide os containers entre múltiplas máquinas de um mesmo cluster de maneira automática.  Através do dispatcher o Docker Swarm define a melhor máquina para executar algum container
3. O Docker Swarm consegue resetar containers automaticamente em caso de falhas. O Docker Swarm tem capacidade de reiniciar containers a fim de manter a aplicação funcionando.
4. O Docker Swarm na verdade é apenas um orquestrador para o Docker, e não seu substituto.

Vimos que a Docker Machine, por mais que não esteja relacionada diretamente ao Docker Swarm, pode nos ajudar bastante. Qual das alternativas abaixo contém uma funcionalidade da Docker Machine?&#x20;

1. Ao utilizar a Docker Machine, podemos criar máquinas virtuais prontas para executar Docker.  A Docker Machine cria máquinas virtuais bem leves já provisionadas com o Docker.
2. A Docker Machine não é utilizada para criar containers diretamente.
3. Ela apenas utiliza o ssh para acessar a máquina virtual criada.
4. A Docker Machine cria uma máquina virtual pronta para executar o Docker, sem interferir direto em nossa máquina física.

Queremos criar nosso primeiro cluster para dividir os containers em diversas máquinas e não sobrecarregar uma única máquina. Qual dos comandos abaixo devemos utilizar para criar o cluster e darmos o primeiro passo para atingir nosso objetivo?&#x20;

```bash
docker swarm init 
```

Além disso, a boa prática também seria utilizar a flag&#x20;

```bash
--advertise-addr
```

Agora queremos adicionar nós ao swarm. Qual das alternativas abaixo é realmente uma responsabilidade dos nós workers dentro do swarm?

1. São responsáveis pela execução dos containers dentro do swarm. Como o nome diz, eles são os trabalhadores, responsáveis por rodar containers.

Vimos  uma diferença bem importante entre os comandos docker service create e docker container run. Qual é essa diferença?

1. Os dois comandos possuem propósitos diferentes, um não substitui o outro.
2. Como vimos, ainda podemos subir containers com o docker container run dentro do swarm.
3. O comando docker service create no final cria um container em escopo do swarm e o docker container run em escopo local.  Como vimos, essa é uma grande diferença entre os dois comandos.

Qual artifício do Docker Swarm permite que nós possamos acessar quaisquer serviços a partir do IP de qualquer nó dentro do swarm, apenas informando a porta?

1. Routing Mesh. Graças ao Routing Mesh conseguimos acessar diferentes serviços a partir de qualquer IP pertencente ao swarm.
2. O Dispatcher é responsável por definir em qual nó será alocado o espaço para a tarefa gerada pelo serviço.

Que nós workers são responsáveis por executar containers

1. Comandos que leem ou alteram o estado do swarm só podem ser executados em nós managers
2. O comando docker container run sobe containers em escopo local e o docker service create cria serviços em escopo do swarm
3. Tarefas são instâncias de serviços
4. Portas são compartilhadas entre nós do swarm e são acessíveis a partir de qualquer nó graças ao Routing mesh

Caso nosso único manager pare de funcionar, podemos ter problemas. O que acontecerá com o nosso swarm em caso de perda do manager?

1. O swarm não sofrerá nenhum tipo de desligamento fora o próprio manager.
2. As tarefas em execução em outros nós serão mantidas sem problemas. A ausência do manager não afetará as tarefas de outros nós.
3. Não conseguiremos mais executar comandos de leitura e/ou criar novos serviços. Com a ausência do manager, não teremos mais nós capazes de executar comandos administrativos.
4. As tarefas não terão qualquer alteração por conta da ausência do manager.

É muito importante fazermos backup de todo o nosso swarm para evitarmos desastres. Por padrão, em qual diretório fica armazenado o conteúdo do nosso swarm?

1. `/var/lib/docker/swarm` - Nesse diretório temos todas as configurações de estado do nosso swarm.
2. `/etc/docker` - Nesse diretório ficam os arquivos de configuração do Docker.

Ao utilizarmos o comando `docker node ls`, como podemos identificar quais nós são managers dentro do nosso swarm?

1. Basta olhar a coluna Manager Status e ver quais nós tem o valor `Reachable` ou `Leader`. Nós com esses status são managers dentro do nosso swarm.

Vimos que em caso de falhas do Leader do swarm, temos uma eleição entre os nós managers para definir o novo líder. Se tivéssemos um swarm com 7 nós managers, qual seria o nosso quórum necessário e número máximo de falhas para realização da eleição?

1. 4 para o quórum e 3 falhas no máximo.  Como nosso quórum é (N / 2) + 1 e o número máximo de falhas é (N - 1) / 2, temos o valor esperado.

Que nós managers são primariamente responsáveis pela orquestração do swarm

1. A importância e como realizar o backup do swarm
2. &#x20;Que podemos ter mais de um nó manager no swarm
3. A importância do Leader dentro do swarm
4. Como é feita a eleição de um novo Leader em caso de falhas
5. Os requisitos para funcionamento do RAFT

Removemos um nó manager do swarm a partir de outro manager. Quais os procedimentos que devemos executar para realizar essa remoção?

1. Devemos primeiramente rebaixar o cargo do nó com o comando `docker node demote` e depois removê-lo com o comando `docker node rm`. &#x20;
2. Caso estejamos tentando remover um manager a partir de um worker, não conseguiremos executar esse comando diretamente.

Você assumiu um projeto que usa Docker Swarm. No arquivo de configuração tem o seguinte trecho:

```bash
deploy:
      mode: replicated
      replicas: 4
      restart_policy:
        condition: on-failure
      placement:
        constraints: [node.role == manager]
```

O que podemos afirmar sobre o trecho acima?

1. Garantirá que teremos exatamente 4 réplicas desse serviço. Isso será possível graças ao trecho `replicas: 4`.
2. Em caso de falhas o serviço será reiniciado.  Isso foi definido no trecho de `restart_policy`.

Qual a finalidade do comando `docker stack deploy`?

1. Os serviços serão executados de maneira automática.
2. Utilizar um arquivo de composição e subir uma pilha de serviços simultaneamente. Podemos inclusive utilizar as instruções do docker-compose.
3. Vimos que ele também é capaz de subir serviços globais. Basta definir no arquivo de composição.
