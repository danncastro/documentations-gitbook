# Docker Swarm

O Docker Swarm apresenta uma série de vantagens em relação ao Docker usado de maneira tradicional. Marque as alternativas que contém essas vantagens: O Docker Swarm não precisa de uma rede para funcionar pois já vem com um driver customizado. Alternativa errada! O Docker Swarm necessita ao menos de uma rede local para identificar cada máquina que fará parte do cluster.

\[QUIS] O Docker Swarm divide os containers entre múltiplas máquinas de um mesmo cluster de maneira automática. Alternativa correta! Através do dispatcher o Docker Swarm define a melhor máquina para executar algum container

O Docker Swarm consegue resetar containers automaticamente em caso de falhas. Alternativa correta! O Docker Swarm tem capacidade de reiniciar containers a fim de manter a aplicação funcionando.

O Docker Swarm é uma nova versão do Docker, por isso é mais rápido, bem projetado e deve ser usado em produção. Alternativa errada! O Docker Swarm na verdade é apenas um orquestrador para o Docker, e não seu substituto.



Vimos na última aula que a Docker Machine, por mais que não esteja relacionada diretamente ao Docker Swarm, pode nos ajudar bastante. Qual das alternativas abaixo contém uma funcionalidade da Docker Machine? Ao utilizar a Docker Machine, podemos criar máquinas virtuais prontas para executar Docker. Alternativa correta! A Docker Machine cria máquinas virtuais bem leves já provisionadas com o Docker.

Com a Docker Machine, criamos containers de maneira mais rápida e fácil. Alternativa errada! A Docker Machine não é utilizada para criar containers diretamente.

A Docker Machine provê aos nossos containers maior segurança por utilizar o ssh. Alternativa errada! Ela apenas utiliza o ssh para acessar a máquina virtual criada.

A Docker Machine instala o Docker Swarm em nossa máquina física de maneira mais fácil. Alternativa errada! A Docker Machine cria uma máquina virtual pronta para executar o Docker, sem interferir direto em nossa máquina física.

\[QUIS] Queremos criar nosso primeiro cluster para dividir os containers em diversas máquinas e não sobrecarregar uma única máquina. Qual dos comandos abaixo devemos utilizar para criar o cluster e darmos o primeiro passo para atingir nosso objetivo? docker swarm init Alternativa correta! Além disso, a boa prática também seria utilizar a flag --advertise-addr.



Durante o curso, utilizaremos a Docker Machine apenas para criarmos nosso ambiente com diversas máquinas isoladas e iniciarmos nosso cluster.

Porém, a Docker Machine também é muito utilizada com provedores de serviço em nuvem, como a AWS! Podemos definir nossas credenciais, e, utilizando o driver amazonec2, temos a possibilidade de criar diversas máquinas nos servidores da Amazon!

Caso tenha interesse, mais informações podem ser obtidas na [documentação](https://docs.docker.com/machine/examples/aws/) oficial.



O Docker Swarm é um orquestrador

O Docker Swarm é capaz de alocar e reiniciar containers de maneira automática

Como criar máquinas já provisionadas para utilizar o Docker com a Docker Machine utilizando comando docker-machine create

Um cluster é um conjunto de máquinas dividindo poder computacional

Como criar um cluster utilizando o Docker Swarm utilizando o comando docker swarm init



Agora queremos adicionar nós ao swarm. Qual das alternativas abaixo é realmente uma responsabilidade dos nós workers dentro do swarm?

São responsáveis pela execução dos containers dentro do swarm. Alternativa correta! Como o nome diz, eles são os trabalhadores, responsáveis por rodar containers.



Para executarmos comandos de leitura, como por exemplo docker node ls, e/ou alteração no estado do swarm, temos uma condição. Qual das alternativas abaixo contém essa condição?

Esses comandos podem ser executados tanto em nós managers quanto nós workers. Alternativa errada! Comandos de leitura e outros que modificam o estado do swarm não podem ser executados por workers.

Podem ser executados apenas em nós managers. Alternativa correta! Tais comandos ficam restritos apenas aos nós managers dentro do swarm.



Vimos na última aula uma diferença bem importante entre os comandos docker service create e docker container run. Qual é essa diferença?

O comando docker container run foi defasado. Hoje o recomendado pela empresa Docker é que se utilize apenas o comando docker service create. Alternativa errada! Os dois comandos possuem propósitos diferentes, um não substitui o outro.

O comando docker container run só pode ser usado fora do swarm, enquanto o docker service create pode ser usado tanto dentro quanto fora. Alternativa errada! Como vimos no vídeo, ainda podemos subir containers com o docker container run dentro do swarm.

O comando docker service create no final cria um container em escopo do swarm e o docker container run em escopo local. Alternativa correta! Como vimos, essa é uma grande diferença entre os dois comandos.



Qual artifício do Docker Swarm permite que nós possamos acessar quaisquer serviços a partir do IP de qualquer nó dentro do swarm, apenas informando a porta?

Routing Mesh. Alternativa correta! Graças ao Routing Mesh conseguimos acessar diferentes serviços a partir de qualquer IP pertencente ao swarm.

Dispatcher. Alternativa errada! O Dispatcher é responsável por definir em qual nó será alocado o espaço para a tarefa gerada pelo serviço.



Que nós workers são responsáveis por executar containers

Comandos de leitura e visualização de nós, como o docker node ls

Comandos que leem ou alteram o estado do swarm só podem ser executados em nós managers

O comando docker container run sobe containers em escopo local e o docker service create cria serviços em escopo do swarm

Tarefas são instâncias de serviços

Portas são compartilhadas entre nós do swarm e são acessíveis a partir de qualquer nó graças ao routing mesh



Caso nosso único manager pare de funcionar, podemos ter problemas. O que acontecerá com o nosso swarm em caso de perda do manager? O swarm será destruído automaticamente. Alternativa errada! O swarm não sofrerá nenhum tipo de desligamento fora o próprio manager.

As tarefas em execução em outros nós serão mantidas sem problemas. Alternativa correta! A ausência do manager não afetará as tarefas de outros nós.

Não conseguiremos mais executar comandos de leitura e/ou criar novos serviços. Alternativa correta! Com a ausência do manager, não teremos mais nós capazes de executar comandos administrativos.

Todos as tarefas em execução, em quaisquer nós, irão parar de funcionar. Alternativa errada! As tarefas não terão qualquer alteração por conta da ausência do manager.



É muito importante fazermos backup de todo o nosso swarm para evitarmos desastres. Por padrão, em qual diretório fica armazenado o conteúdo do nosso swarm?

/var/lib/docker/swarm Alternativa correta! Nesse diretório temos todas as configurações de estado do nosso swarm.

/etc/docker Alternativa errada! Nesse diretório ficam os arquivos de configuração do Docker.



docker node ls --format "\{{.Hostname\}} \{{.ManagerStatus\}} docker node ls --format "\{{.Node\}} docker service ls --format "\{{.Name\}} \{{.Replicas\}}

docker service update --constrainet-add node.role==worker id\_service docker node update --availability drain node docker service update --replicas valor\_replicas service

docker service create -p portHost:portContainer --mode global imagem docker service create --name nome\_servico --replcas valor\_replicas imagem sleep 1d(Quantidade de dias que ele se mantera ativo)

docker node inspect node --pretty



Ao utilizarmos o comando docker node ls, como podemos identificar quais nós são managers dentro do nosso swarm?

Basta olhar a coluna Manager Status e ver quais nós tem o valor Reachable ou Leader. Alternativa correta! Nós com esses status são managers dentro do nosso swarm.



Vimos que em caso de falhas do Leader do swarm, temos uma eleição entre os nós managers para definir o novo líder. Se tivéssemos um swarm com 7 nós managers, qual seria o nosso quórum necessário e número máximo de falhas para realização da eleição?

Obs: Caso não lembre das regras, reveja o último vídeo a partir dos 4:00.

4 para o quórum e 3 falhas no máximo. Alternativa correta! Como nosso quórum é (N / 2) + 1 e o número máximo de falhas é (N - 1) / 2, temos o valor esperado.



Que nós managers são primariamente responsáveis pela orquestração do swarm

A importância e como realizar o backup do swarm

Que podemos ter mais de um nó manager no swarm

A importância do Leader dentro do swarm

Como é feita a eleição de um novo Leader em caso de falhas

Os requisitos para funcionamento do RAFT



No último vídeo, removemos um nó manager do swarm a partir de outro manager. Quais os procedimentos que devemos executar para realizar essa remoção?

Devemos primeiramente rebaixar o cargo do nó com o comando docker node demote e depois removê-lo com o comando docker node rm. Alternativa correta! É necessário transformar o nó manager em worker e depois remover.

Basta remover utilizando o comando docker node rm. Alternativa errada! Como estamos tentando remover um manager, não conseguiremos executar esse comando diretamente.



Quando o nosso objetivo é restringir o comportamento de nós de um swarm, podemos utilizar o comando:

```bash
docker node update --availability drain
```

Qual das alternativas abaixo contém o comportamento esperado do nó sobre o qual foi aplicado esse comando? O nó ficará indisponível para executar tarefas. Alternativa correta! Com a disponibilidade em drain, não conseguiremos executar mais tarefas/containers nesse nó.



Podemos também aplicar restrições em serviços utilizando o comando:

```bash
docker service update --constraint-add [outras_infos]
```

node.role == worker Alternativa correta! Informando o role e fazendo a comparação com ==, não teremos problemas.



Para saber mais: Outras restrições

No último vídeo vimos como podemos adicionar restrições a um serviço utilizando o comando:

```bash
docker service update --constraint-add
```

Utilizamos o comando acima para restringir serviços a funcionarem apenas em nós managers ou workers.

Porém, também podemos impor outros tipos de restrições, como id, hostname e o próprio role. Vamos ver alguns exemplos!

Caso quiséssemos restringir o serviço de id ci10k3u7q6ti para funcionar apenas em um nó com id t76gee19fjs8, poderíamos utilizar o comando:

```bash
docker service update --constraint-add node.id==t76gee19fjs8 ci10k3u7q6ti
```

Se o objetivo fosse fazer o serviço rodar apenas em nossa vm4 por exemplo, uma possibilidade seria utilizar:

```bash
docker service update --constraint-add node.hostname==vm4 ci10k3u7q6ti
```

Por fim, podemos também remover restrições criadas utilizando o comando de atualização passando a flag --constraint-rm. Para remover as duas restrições anteriores:

```bash
docker service update --constraint-rm node.id==t76gee19fjs8 ci10k3u7q6ti
docker service update --constraint-rm node.hostname==vm4 ci10k3u7q6ti
```

Após esse momento, quaisquer novas réplicas criadas para esse serviço poderão ser alocadas sem restrição alguma!



Como readicionar um manager posterior a uma falha

Restringir nós de executarem quaisquer serviços utilizando o docker node update --availability drain

Restringir serviços de serem executados em determinados nós utilizando o docker service update --constraint-add



No último vídeo, aprendemos diversos aspectos sobre serviços replicados. Quais das alternativas abaixo são verdadeiras sobre esse tipo de serviço?

Serviços replicados podem rodar em apenas um nó. Alternativa correta! Basta definirmos para o serviço ter apenas uma réplica.

O número de réplicas de um serviço não pode ser atualizada depois do serviço ser criado. Alternativa errada! Podemos atualizar com o comando docker service update --replicas.

Serviços replicados rodam necessariamente em todos os nós do swarm. Alternativa errada! Veremos no próximo vídeo como rodar serviços em todos os nós do swarm.

Serviços por padrão são criados no modo replicado. Alternativa correta! Quando não informamos o modo desejado, criamos serviços replicados por padrão.



Vamos supor que temos um serviço com id ci10k3u7q6ti. Como podemos escalar esse serviço para ter 5 réplicas?

Aprendemos uma das possibilidades de alterar o número de réplicas de um serviço utilizando o comando docker service update --replicas 5 ci10k3u7q6ti, mas esse não é o único meio!

Para isso também temos o comando docker service scale. Utilizando o id, podemos atualizar com o comando:

```bash
docker service scale ci10k3u7q6ti=5
```

Nesse caso, definimos 5 réplicas para o serviço. Os dois comandos produzem o mesmo resultado, o segundo é apenas uma forma resumida do primeiro comando.



No último vídeo, aprendemos diversos aspectos sobre serviços globais. Quais das alternativas abaixo são verdadeiras sobre esse tipo de serviço?

Bons exemplos de serviços globais são serviços de monitoramento e segurança. Alternativa correta! Serviços que são críticos à aplicação como um todo podem e devem ser executados como globais para que todos os nós possam ser devidamente monitorados e estejam seguros.

Serviços globais rodam em todos os nós do swarm, menos em managers. Alternativa errada! Serviços globais rodam em todos os nós, independente do tipo do nó.

Para um serviço ser global, precisamos necessariamente criá-lo replicado e depois alterar com o docker service update. Alternativa errada! Não precisamos criar um serviço global como replicado para depois atualizá-lo. Podemos utilizar a flag --mode global no momento da criação do serviço.



Serviços replicados rodam em um ou mais nós do swarm

Serviços globais rodam em todos os nós do swarm

Nós managers por padrão trabalham como workers

Serviços como monitoramento e segurança são bons exemplos de serviços globais

Como definir o modo que o serviço será criado utilizando a flag --mode no momento da criação do serviço



O driver bridge permite a comunicação entre diferentes containers em um mesmo host, então se eu tenho 10 container em uma máquina só, todos eles conseguem se comunicar por causa desse driver bridge aqui no fim das contas.

O drive host, ele serve para fazer a comunicação entre container e máquina, então a máquina que está carregando esse container consegue se comunicar com o IP desse container por causa desse driver host.

\[QUIS] Conhecemos agora a rede ingress e vimos que, além de ser a rede padrão criada pelo Docker Swarm para os nós que fazem parte do cluster, ela utiliza o driver overlay. Qual das alternativas abaixo contém uma finalidade desse driver?

O driver overlay comunica seus dados de maneira criptografada para garantir nossa segurança. Alternativa correta! Executando o comando docker node inspect podemos ver os certificados de segurança.

O driver overlay permite a comunicação entre diferentes hosts de um mesmo cluster. Alternativa correta! Além de garantir a segurança, o driver overlay também permite a comunicação entre diferentes hosts rodando Docker.

\[QUIS] A rede ingress, utilizando o driver overlay, consegue fazer diversas coisas por nós, como garantir por exemplo, a comunicação entre diferentes nós de maneira altamente segura. Porém, ela também possui certas limitações. Qual das alternativas abaixo contém uma dessas limitações? Serviços conseguem se comunicar apenas via endereço IP.

Alternativa correta! Temos que saber o endereço IP do serviço que queremos nos comunicar utilizando a rede ingress.

\[QUIS] Na última aula, aprendemos alguns fatos novos sobre User-Defined Overlay networks. Quais das alternativas abaixo contém informações verdadeiras sobre redes criadas manualmente?

User-Defined Overlay são criadas de maneira lazy para managers. Alternativa errada! Elas funcionam desse modo para managers.

User-Defined Overlay não permitem comunicação via IP. Alternativa errada! Além de permitir a comunicação via nome do serviço, também permitem via IP.

User-Defined Overlay são criadas de maneira lazy para workers. Alternativa correta! Essas redes só serão reconhecidas por workers que rodarem tarefas que utilizem a rede.

Serviços que utilizam redes customizadas conseguem descobrir outros serviços diretamente por nome. Alternativa correta! Podemos utilizar o conceito de Service Discovery com User-Defined Overlay.



Por mais que o driver overlay seja responsável por comunicar múltiplos hosts em uma mesma rede, também podemos conectar containers em escopo local criados com o comando docker container run em redes criadas com esse driver.

Para isso, basta no momento da criação da rede utilizarmos a flag --attachable:

```bash
docker network create -d overlay --attachable my_overlay
```

Com o comando acima, conseguiremos conectar tanto serviços como containers "standalone" em nossa rede my\_overlay.

A rede ingress é a padrão criada junto com nosso swarm

O driver overlay é utilizado para a comunicação entre nós em diferentes hosts

Como criar nossas próprias redes com o driver overlay utilizando o comando docker network create -d overlay

Redes overlays criadas manualmente (User-Defined Overlay) permitem a comunicação entre serviços por seus nomes (Service Discovery)

As redes criadas com o driver overlay são listadas de maneira lazy para workers



O download do arquivo utilizado pode ser feito [aqui](https://caelum-online-public.s3.amazonaws.com/1486-docker-swarm-cluster-container/07/docker-compose.yml).

\[QUIS] Você assumiu um projeto que usa Docker Swarm. No arquivo de configuração tem o seguinte trecho:

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

Garantirá que teremos exatamente 4 réplicas desse serviço. Alternativa correta! Isso será possível graças ao trecho replicas: 4.

Em caso de falhas o serviço será reiniciado. Alternativa correta! Isso foi definido no trecho de restart\_policy.



Qual a finalidade do comando docker stack deploy?

Criar uma pilha de serviços para que possam ser executados de maneira manual conforme desejo do usuário. Alternativa errada! Os serviços serão executados de maneira automática.

Utilizar um arquivo de composição e subir uma pilha de serviços simultaneamente. Alternativa correta! Podemos inclusive utilizar as instruções do docker-compose.

Subir apenas serviços replicados para o swarm. Alternativa errada! Vimos que ele também é capaz de subir serviços globais. Basta definir no arquivo de composição.



Por padrão, tanto o Docker no modo standalone quanto o Docker Swarm, partilham apenas de um driver local para uso de volumes. Isso quer dizer que o Docker Swarm não possui, até então, solução nativa para distribuir volumes entre os nós.

Então, no exemplo do vídeo anterior, ao definirmos o volume para cada serviço, criamos um volume local dentro de cada nó que for executar a tarefa. Logo, os volumes não são compartilhados entre os diferentes nós do cluster.

Existem soluções que não são nativas do Docker Swarm para utilizar volumes distribuídos entre nós, que podem ser consultadas na [Docker Store](https://store.docker.com/search?category=volume\&q=\&type=plugin).



O docker-compose.yml também pode ser utilizado para serviços de um swarm

Novas instruções a partir da versão 3, como deploy e suas instruções internas

Uma stack é uma pilha de serviços trabalhando em conjunto

Como criar e remover nossa própria stack utilizando o comando docker stack

