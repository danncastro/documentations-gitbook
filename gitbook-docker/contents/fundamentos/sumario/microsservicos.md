---
description: Já ouviu falar de Microsserviços?
---

# Microsserviços

***

Uma forma de desenvolver uma aplicação é colocar todas as funcionalidades em um único **"lugar"**.

* Ou seja, a aplicação roda em uma única instância (ou servidor) que possui todas as funcionalidades.&#x20;

> _Isso talvez seja a forma mais simples de criar uma aplicação (também a mais natural), mas quando a base de código cresce, alguns problemas podem aparecer._

Por exemplo, qualquer atualização ou [bug fix](https://www.dicionariotecnico.com/traducao.php?termo=bug+fix) necessita parar todo o sistema, buildar o sistema todo e subir novamente. Isso pode ficar demorado e lento.&#x20;

* Em geral, quanto maior a base de código mais difícil será manter ela mesmo com uma boa cobertura de testes e as desvantagens não param por ai.&#x20;
* Outro problema é se alguma funcionalidade possuir um gargalo no desempenho o sistema todo será afetado.&#x20;

> _Não é raro de ver sistemas onde relatórios só devem ser gerados à noite para não afetar o desempenho de outras funcionalidades._&#x20;

* Outro problema comum é com os ciclos de testes e build demorados (_falta de agilidade no desenvolvimento_), problemas no monitoramento da aplicação ou falta de escalabilidade. Enfim, o sistema se torna um legado pesado, onde nenhum desenvolvedor gostaria de colocar a mão no fogo.

***

## <mark style="color:red;">Arquitetura de Microsserviços</mark>&#x20;

Então a ideia é fugir desse tipo de arquitetura _**monolítica**_ monstruosa e dividir ela em pequenos pedaços.&#x20;

* Cada pedaço possui uma funcionalidade bem definida e roda como se fosse um _"mini sistema" isolado._&#x20;
* Ou seja, em vez de termos uma única aplicação enorme, teremos várias instâncias menores que dividem e coordenam o trabalho.&#x20;

> _Essas instâncias são chamadas de Microsserviços._

Agora fica mais fácil monitorar cada serviço específico, atualizá-lo ou escalá-lo pois a base de código é muito menor, e assim o deploy e o teste serão mais rápidos. Podemos agora achar soluções específicas para esse serviço sem precisar alterar os demais.

> _Outra vantagem é que um desenvolvedor novo não precisa conhecer o sistema todo para alterar uma funcionalidade, basta ele focar na funcionalidade desse **microsserviço**._

{% hint style="warning" %}
Importante também é que um microsserviço seja acessível remotamente, normalmente usando o protocolo HTTP trocando mensagens `JSON` ou `XML`, mas nada impede que outro protocolo seja usado.&#x20;
{% endhint %}

* Um microsserviço pode usar outros serviços para coordenar o trabalho.

> Repare que isso é uma outra abordagem arquitetural bem diferente do monolítico e por isso também é chamado de arquitetura de microsserviços.

Por fim, uma arquitetura de Microsserviços tem um grau de complexidade muito alta se comparada com uma arquitetura monolítica.&#x20;

> Aliás, há aqueles profissionais que indicam partir para uma [arquitetura monolítica primeiro e mudar para uma baseada em microsserviços](http://sdtimes.com/martin-fowler-monolithic-apps-first-microservices-later/) depois, quando estritamente necessário.

***

## <mark style="color:red;">Docker e Microsserviços</mark>&#x20;

Trabalhar com uma arquitetura de microsserviços gera a necessidade de publicar o serviço de maneira rápida, leve, isolada e vimos que o Docker possui exatamente essas características!&#x20;

> _Com Docker e Docker Compose podemos criar um ambiente ideal para a publicação destes serviços._

{% hint style="info" %}
O Docker é uma ótima opção para rodar os microsserviços pelo fato de isolar os containers.&#x20;
{% endhint %}

Essa utilização de containers para serviços individuais faz com que seja muito simples gerenciar e atualizar esses serviços, de maneira automatizada e rápida, possibilitando ter vários serviços rodando usando o Docker.&#x20;

* O Docker Compose é a ferramenta ideal para coordenar a criação dos containers, no entanto para melhorar a escalabilidade e desempenho pode ser necessário criar muito mais containers para um serviço específico.&#x20;

> _Em outras palavras, agora gostaríamos de criar muitos containers aproveitando várias máquinas **(virtuais ou físicas)!**_&#x20;

* Ou seja, pode ser que um microsserviço fique rodando em 20 containers usando três máquinas físicas diferentes.&#x20;

> _Como podemos facilmente subir e parar esses containers?_&#x20;

Para isso e por isso existe uma outra ferramenta que se chama _**Docker Swarm.**_ Docker Swarm facilita a criação e administração de um cluster de containers.

***

<mark style="color:yellow;">\[QUIS]</mark>

1. Onde o Docker é executado no Windows?&#x20;

```bash
Em uma máquina virtual
```

2. Qual comando é utilizado para instalar o Docker no Ubuntu?&#x20;

```bash
apt-get install docker.io
```

3. Qual pré-requisito precisamos contemplar para instalar o Docker em um Windows?&#x20;

```
Suporte a virtualização
```

***
