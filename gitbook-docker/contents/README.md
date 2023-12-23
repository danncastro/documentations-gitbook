---
description: >-
  Primeiramente, devemos falar sobre a Docker, Inc.., que no início era chamada
  de dotCloud.
---

# Docker, Inc

***

A [dotCloud](https://www.docker.com/press-release/dotcloud-inc-now-docker-inc/) era uma empresa de [PaaS (Platform as a Service)](https://www.redhat.com/pt-br/topics/cloud-computing/what-is-paas), sendo responsável pela hospedagem da nossa aplicação, levantando o servidor, configurando-o, liberando portas, etc..., fazendo tudo o que é necessário para subir a nossa aplicação.&#x20;

> _Outros exemplos de empresas de PaaS são o_ [_Heroku_](https://www.heroku.com/)_,_ [_Microsoft Azure_](https://azure.microsoft.com/pt-br/free/search/?ef\_id=\_k\_EAIaIQobChMIh4DwqZXkgAMVEEKRCh1VEwCUEAAYASAAEgK06vD\_BwE\_k\_\&OCID=AIDcmmzmnb0182\_SEM\_\_k\_EAIaIQobChMIh4DwqZXkgAMVEEKRCh1VEwCUEAAYASAAEgK06vD\_BwE\_k\_\&gclid=EAIaIQobChMIh4DwqZXkgAMVEEKRCh1VEwCUEAAYASAAEgK06vD\_BwE) _e_ [_Google Cloud Platform._](https://cloud.google.com/?utm\_source=google\&utm\_medium=cpc\&utm\_campaign=latam-BR-all-pt-dr-BKWS-all-all-trial-e-dr-1605194-LUAC0010101\&utm\_content=text-ad-none-any-DEV\_c-CRE\_512285710731-ADGP\_Hybrid+%7C+BKWS+-+EXA+%7C+Txt+\~+GCP\_General-KWID\_43700062788251524-kwd-301173107504\&utm\_term=KW\_google%20cloud%20platform-ST\_Google+Cloud+Platform\&gclid=CjwKCAjwivemBhBhEiwAJxNWN\_G1WhqWlpi95ApMNaa2ljnD5P-0fGli7A\_mfmogZ2zHpSeKlW8SzBoCk\_sQAvD\_BwE\&gclsrc=aw.ds)

***

Inicialmente, para prover a parte de infraestrutura, a _dotCloud_ utilizava o [Amazon Web Service](https://aws.amazon.com/pt/free/?trk=2ee11bb2-bc40-4546-9852-2c4ad8e8f646\&sc\_channel=ps\&ef\_id=CjwKCAjwivemBhBhEiwAJxNWN97GWWt2EPR3NA\_BRTWKKGmtN2X-ikRDcX1nKBDrnLAin21z9BHK2hoCI5UQAvD\_BwE:G:s\&s\_kwcid=AL!4422!3!561843094926!e!!g!!amazon%20web%20services!15278604629!130587771580) **(AWS)**, serviço que nos disponibiliza máquinas virtuais e físicas para trabalharmos. E para hospedar uma aplicação, sabemos que precisamos do sistema operacional, mas a _dotCloud_ introduziu o conceito de containers na hora de subir uma aplicação, dando origem ao [Docker](https://docs.docker.com/), tecnologia utilizada para baratear o custo de hospedar várias aplicações em uma mesma máquina.

* Ou seja, quando a dotCloud criou o Docker, sua intenção era economizar os gastos da empresa, subindo várias aplicações em containers, em um mesmo hardware do AWS, e com o passar do tempo a empresa percebeu que tinham muitos desenvolvedores interessados na tecnologia que ela havia criado, a tecnologia que permite a criação de containers, que faz o intermédio entre eles e o sistema operacional, o Docker.

{% hint style="info" %}
As tecnologias do Docker nada mais é do que uma coleção de tecnologias para facilitar o `deploy` e a execução das nossas aplicações.&#x20;
{% endhint %}

***

A sua principal tecnologia é a [Docker Engine](https://docs.docker.com/engine/), a plataforma que segura os containers, fazendo o intermédio entre o sistema operacional.

***

## <mark style="color:red;">Arquitetura do Docker</mark>

<table data-full-width="true"><thead><tr><th>Terminologias</th></tr></thead><tbody><tr><td><em>Container image</em></td></tr><tr><td><em>Dockerfile</em></td></tr><tr><td><em>Build</em></td></tr><tr><td><em>Container</em></td></tr><tr><td><em>Volumes</em></td></tr><tr><td><em>Tag</em></td></tr><tr><td><em>Multi-stage Build</em></td></tr><tr><td><em>Repository</em></td></tr><tr><td><em>Registry</em></td></tr><tr><td><em>Docker Hub</em></td></tr><tr><td><em>Docker Compose</em></td></tr><tr><td><em>Docker Swarm</em></td></tr></tbody></table>

> Outras tecnologias do Docker que facilitam a nossa vida e que veremos futuramente

***

### <mark style="color:yellow;">**Docker Compose**</mark>

Um jeito fácil de definir e orquestrar múltiplos containers;

{% embed url="https://docs.docker.com/compose/" %}

***

### <mark style="color:yellow;">**Docker Swarm**</mark>

_U_ma ferramenta para colocar múltiplos docker engines para trabalharem juntos em um cluster;&#x20;

{% embed url="https://docs.docker.com/engine/swarm/" %}

***

### <mark style="color:yellow;">Docker Hub</mark>

_U_m repositório com mais de 250 mil imagens diferentes para os nossos containers;

{% embed url="https://hub.docker.com/" %}

***

### <mark style="color:yellow;">Docker Machine</mark>

Uma ferramenta que nos permite gerenciar o Docker em um host virtual.

{% embed url="https://docs.docker.com/desktop/" %}

***

#### <mark style="color:yellow;">Open Source</mark>&#x20;

Quando a empresa dotCloud tornou-se a _**Docker, Inc**_., focada em manter o Docker, ela o abriu para o mundo open source, tudo disponibilizado no seu GitHub, inclusive com várias empresas contribuindo para o desenvolvimento dessa tecnologia.

* Apesar de haver alguns serviços pagos, em sua grande parte a tecnologia do Docker é uma tecnologia open source, utilizada por várias empresas.&#x20;

{% embed url="https://www.redhat.com/en/topics/open-source/what-is-open-source" %}
Overview Red Hat
{% endembed %}

***

## <mark style="color:red;">Utilizando o</mark> <mark style="color:red;"></mark>_<mark style="color:red;">Play With Docker</mark>_&#x20;

Também tem a opção de utilizar o [Play With Docker](https://labs.play-with-docker.com/). Nele você poderá utilizar os comandos vistos daqui em diante e testar as diversas funcionalidades que o Docker proporciona.&#x20;

* Ao acessar o site basta clicar em `+Add New Instance` e começar a utilizá-lo como estivesse usando sua máquina normalmente.

***
