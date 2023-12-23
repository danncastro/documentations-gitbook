---
description: >-
  Quando falamos de Swarm, precisamos entender dois recursos importantes, os
  serviços e as tasks
---

# Services

**Services**  - É a definição de um estado desejado.&#x20;

**Tasks -** É criada a partir de um serviço que da origem aos containers a serem executados nos nodes

## Tasks

Quando utilizamos os serviços descrevemos o estado desejado, como por exemplo:

```bash
caçando
```

Com isto o swarm manager irá criar as tasks e dividi-las entre os nodes disponíveis, de forma a atender, se possível, o estado desejado.

## Tasks e Agendadores

> Uma task é uma unidade atômica agendada em um swarm.&#x20;

Quando declaramos um estado desejado de um serviço criando ou atualizando o serviço, o orquestrador identifica o estado desejado através do agendamento de tasks.

Uma task é um mecanismo unidirecional, que navega entre uma séria de estados: `assigned`, `prepared`, `running`, etc.. Se uma task falha, o orquestrador remove a task e o container e em seguida cria uma nova task para a substituir de acordo com o estado desejado especificado pelo serviço

<table><thead><tr><th width="368">Estado</th><th>Descrição</th></tr></thead><tbody><tr><td><code>NEW</code></td><td>Task foi inicializada .</td></tr><tr><td><code>PENDING</code></td><td>Recursos estão sendo alocados</td></tr><tr><td><code>ASIGNED</code></td><td>Task foi atribuída a um nó</td></tr><tr><td><code>ACCEPTED</code></td><td>Task aceita por um nó worker</td></tr><tr><td><code>PREPARING</code></td><td>Docker está preparando a task</td></tr><tr><td><code>STARTING</code></td><td>Docker esta iniciando a task</td></tr><tr><td><code>RUNNING</code></td><td>Task em execução</td></tr><tr><td><code>COMPLETE</code></td><td>Task finalizou sem error code</td></tr><tr><td><code>FAILED</code></td><td>Task finalizou com error code</td></tr><tr><td><code>SHUTDOWN</code></td><td>Docker requisitou o desligamento da task</td></tr><tr><td><code>REJECTED</code></td><td>O nó worker rejeitou a task</td></tr><tr><td><code>ORPHANED</code></td><td>O nó esteve down por muito tempo</td></tr><tr><td><code>REMOVE</code></td><td>A task não terminou mas o recurso associado foi removido ou reduzido</td></tr></tbody></table>

## Serviços Replicados e Globais

Existem dois tipos de `deployment` de serviço, o `replicated` e o `global`.

Um serviço replicated, é o que informamos a quantidade de tasks idênticas que iremos executar, por exemplo quando decidimos fazer um deploy de um webserver com três replicas, servindo o mesmo conteúdo.

O serviço global, é o serviço que vai executar em todos os nós, por isso o nome global. Não existe um número de tasks pré-especificadas. Sempre que um novo nó for adicionado ao swarm o orquestrador criará uma task e o agendador (Schedule) atribui a task ao novo nó. Este tipo de serviço é amplamente utilizado quando falamos de agents de monitoramento, scanners antivírus ou algum serviço que precisa ser executado em cada nó do swarm.

O `docker-compose.yml` também pode ser utilizado para serviços de um swarm

## Gerenciando Serviços

Para gerenciar os serviços utilizamos o comando `docker service`

```bash
docker service --help 
```

1. Vamos criar um serviço para executar o nginx

```bash
docker service create --name webserver registry.docker-dca.example:5000/nginx
```

2. Podemos listar os serviços através do subcomando `ls` e listar as tasks através do comando `ps`

```bash
docker service ls 
docker service ps webserver
```

> Através do `docker service ps` podemos verificar o estado da task, o id da task e em qual nó a task está sendo executada, bem como se houveram erros e quais portas estão publicadas.

3. Para publicar alguma porta, podemos executar um `update` no service com o parâmetro `--publish-add`

```bash
docker service update --publish-add 80 webserver
docker service ls
```

> Por padrão uma porta aleatória é adicionada ao service (30000 +)

4. Vemos também que o serviço tem uma nova task e sua task anterior entra em estado de `shutdown`

```bash
docker service ps webserver
```

> No nosso caso o serviço está rodando no `node01` podemos acessar por um navegador ou através do curl e verificar que o serviço está sendo executado.

```bash
curl http://node01.docker-dca.example:30000
```

5. Também é possivel inspecionar o serviço para identificarmos alguns detalhes interessantes

```bash
docker service inspect webserver
docker service inspect --pretty webserver
```

> A opção `--pretty` exibe a informação de uma maneira mais agradável para humanos.

```bash
Endpoint Mode:  vip
Ports:
 PublishedPort = 30000
  Protocol = tcp
  TargetPort = 80
  PublishMode = ingress 
```

Podemos ver que:

* O endpoint está sendo executado como `vip` ou seja, `virtual ip`.
* A porta de destino é a `80` , `TargetPort`
* A porta publicada é a porta `30000` , `PublishedPort`
* O modo de publicação é o `ingress` ou seja, a rede que gerencia e controla os dados relacionados ao serviço de swarm.

6. Vamos remover nosso serviço

```bash
docker service rm webserver
```

Assim como fazemos com containers, podemos alterar o comando que o container deve executar, basta adicionar o comando ao final.

```bash
docker service create --name pingtest registry.docker-dca.example:5000/alpine ping google.com
```

7. Também podemos verificar os logs do serviço.

```bash
docker service logs pingtest
```

## Escalando Serviços

Agora que temos nosso serviço sendo executado, podemos fazer jus ao benefício da elasticidade e escala-lo, ou seja, aumentar a quantidade de replicas em execução.

```bash
docker service scale pingtest=3
docker service ls
docker service ps pingtest
```

> também podemos executar o comando `$ docker service update --replicas 3 pingtest`

Agora que temos nosso serviço executando um container em cada node, podemos verificar os logs de todos os containers de maneira agregada

```bash
docker service logs -f pingtest
```

Podemos também verificar os services que estão rodando em cada container

```bash
docker node ps master.docker-dca.example
docker node ps node01.docker-dca.example
docker node ps node02.docker-dca.example
```

## Disponibilidade dos Nodes

Quando precisamos efetuar algum tipo de manutenção em algum node do cluster, precisamos garantir que a manutenção seja feita de maneira correta.

Para este tipo de situação precisamos fazer o processo de `drain` ou drenagem dos nós.

Isto é possível da seguinte maneira:

Primeiramente vamos aumentar o numero de replicas de nosso `pingtest` para 7

```bash
docker service update --replicas 7 pingtest
docker service ps pingtest
```

Podemos ver que nosso serviço está sendo executado em diversos nodes. Vamos colocar nosso `node01` em estado de drenagem através do comando:

```bash
docker node update node01.docker-dca.example --availability drain 
```

Podemos verificar agora como nosso serviço e nosso node estão trabalhando

```bash
docker service ps pingtest
docker node ls 
docker node inspect --pretty node01.docker-dca.example
```

Vemos que nosso node esta em estado de `Drain` e as tasks foram direcionadas para outros nodes.

Para voltar nosso node para um estado de disponível podemos mudar sua `availability` para `active`

```bash
docker node update node01.docker-dca.example --availability active
```

Note que as tasks não serão redistribuídas uma vez que estão sendo executadas com sucesso.

```bash
docker service ps pingtest
```

> Caso novas tasks sejam requisitadas, o nosso node agora é um candidato para recebe-las

Remova o serviço

```bash
docker service rm pingtest
docker service ls
```

## Orquestração do Swarm

Podemos também aplicar restrições em serviços utilizando o comando:

```bash
docker service update --constraint-add [outras_infos]
```

`node.role == worker` - Informando o role e fazendo a comparação com `==` não teremos problemas.

Vimos como podemos adicionar restrições a um serviço. Utilizamos o comando acima para restringir serviços a funcionarem apenas em nós managers ou workers.

Porém, também podemos impor outros tipos de restrições, como id, hostname e o próprio role.

### Vamos ver alguns exemplos!

Caso quiséssemos restringir o serviço de id `ci10k3u7q6t`i para funcionar apenas em um nó com id `t76gee19fjs8`, poderíamos utilizar o comando:

```bash
docker service update --constraint-add node.id==t76gee19fjs8 ci10k3u7q6ti
```

Se o objetivo fosse fazer o serviço rodar apenas em nossa vm4 por exemplo, uma possibilidade seria utilizar:

```bash
docker service update --constraint-add node.hostname==vm4 ci10k3u7q6ti
```

Por fim, podemos também remover restrições criadas utilizando o comando de atualização passando a flag `--constraint-rm`. Para remover as duas restrições anteriores:

```bash
docker service update --constraint-rm node.id==t76gee19fjs8 ci10k3u7q6ti
docker service update --constraint-rm node.hostname==vm4 ci10k3u7q6ti
```

Após esse momento, quaisquer novas réplicas criadas para esse serviço poderão ser alocadas sem restrição alguma!

Como readicionar um manager posterior a uma falha

Restringir nós de executarem quaisquer serviços utilizando o `docker node update --availability drain`

Restringir serviços de serem executados em determinados nós utilizando o `docker service update --constraint-add`

Vamos supor que temos um serviço com id `ci10k3u7q6ti`. Como podemos escalar esse serviço para ter 5 réplicas?

Aprendemos uma das possibilidades de alterar o número de réplicas de um serviço utilizando o comando docker service update --replicas 5 ci10k3u7q6ti, mas esse não é o único meio!

Para isso também temos o comando docker service scale. Utilizando o id, podemos atualizar com o comando:

```bash
docker service scale ci10k3u7q6ti=5
```

Nesse caso, definimos 5 réplicas para o serviço. Os dois comandos produzem o mesmo resultado, o segundo é apenas uma forma resumida do primeiro comando.

* Serviços replicados rodam em um ou mais nós do swarm
* Serviços globais rodam em todos os nós do swarm
* Nós managers por padrão trabalham como workers
* Serviços como monitoramento e segurança são bons exemplos de serviços globais

```bash
docker service update --constrainet-add node.role==worker id_service docker node update --availability drain node docker service update --replicas valor_replicas service
```

```bash
docker service create -p portHost:portContainer --mode global imagem 
docker service create --name nome_servico --replcas valor_replicas imagem sleep 1d(Quantidade de dias que ele se mantera ativo)
```

### \[QUIS]

Aprendemos diversos aspectos sobre serviços replicados. Quais das alternativas abaixo são verdadeiras sobre esse tipo de serviço?

1. Serviços replicados podem rodar em apenas um nó.  Basta definirmos para o serviço ter apenas uma réplica.
2. Podemos atualizar com o comando docker service update --replicas.
3. Serviços por padrão são criados no modo replicado. Quando não informamos o modo desejado, criamos serviços replicados por padrão.

Aprendemos diversos aspectos sobre serviços globais. Quais das alternativas abaixo são verdadeiras sobre esse tipo de serviço?

1. Bons exemplos de serviços globais são serviços de monitoramento e segurança. Serviços que são críticos à aplicação como um todo podem e devem ser executados como globais para que todos os nós possam ser devidamente monitorados e estejam seguros.
2. Serviços globais rodam em todos os nós, independente do tipo do nó.
3. Não precisamos criar um serviço global como replicado para depois atualizá-lo. Podemos utilizar a flag -`-mode` global no momento da criação do serviço.
