---
description: >-
  Docker é uma plataforma Open Source escrita em Go (Linguagem de programação em
  alta performance desenvolvida pela Google) que ajuda a criação e a
  administração de ambientes isolados.
---

# Introdução

***

## <mark style="color:red;">O que é o Docker</mark>

Com a utilização do Docker podemos gerenciar toda a infraestrutura de uma aplicação, bem como garantir que ambientes de desenvolvimento, homologação e produção contenham os mesmos componentes e versões de aplicações, a fim de minimizar impactos no processo de desenvolvimento e entrega de software.

> _O Docker trabalha com uma virtualização a nível do sistema operacional, onde o mesmo utiliza de recursos como o kernel do sistema hospedeiro para executar seus containers._&#x20;

Diferente do modelo tradicional de Máquinas Virtuais, o Docker não necessita da instalação de um sistema operacional por completo, e sim apenas dos arquivos necessários para a aplicação ser executada.

***

## <mark style="color:red;">Por que usar Docker</mark>

Em 2013, Docker introduziu o que se tornou o padrão da indústria para containers, trouxe uma maneira simples, rápida e eficiente de executar aplicações sem a complexidade de uma máquina virtual.

> _Docker garante um ecossistema consistente, fazendo com que o desenvolvedor possa trabalhar sem se preocupar, por exemplo, com a abertura de tickets para uma equipe de infraestrutura provisionar um ambiente por completo, atrasando o trabalho de entrega de software._

{% hint style="info" %}
Existem diversas engines e runtimes de containers e até é possível utilizar containers sem Docker, mas atualmente o Docker é a _**engine/runtime**_ de container mais utilizada no mercado, o que torna o conhecimento do mesmo um **"Must have"** e dificilmente encontramos vagas na área de tecnologia que não pedem um conhecimento, mesmo que básico, de containers ou Docker.
{% endhint %}

***

## <mark style="color:red;">O que é um container</mark>

Um container consiste de um ambiente completo (uma aplicação e todas suas dependências, bibliotecas, binários, arquivos de configuração) em um único pacote.&#x20;

* Ao containerizar uma plataforma de aplicação e suas dependências as diferenças em distribuições de sistemas operacionais e camadas inferiores da infraestrutura são abstraídas.

> _Imagine que o container Docker é como se fosse um container real em um navio (servidor), todos os contêineres estão lado a lado, porém seu conteúdo (ecossistema) não tem interferência de outros containers._

{% hint style="info" %}
Podemos dizer também que um container é a unidade mínima computacional do Docker, ou seja, o menor recurso que o Docker pode fornecer.
{% endhint %}

***

## <mark style="color:red;">Versões</mark>

O Docker possui basicamente duas versões, a versão da comunidade [Community Edition](https://www.docker.com/community/) e a versão empresarial **Enterprise Edition**_**.**_

* A versão Community é de uso gratuito e também tem seu código aberto.
* A maioria dos sistemas Docker em produção utiliza a versão Docker Community Edition.&#x20;

> _O licenciamento anual da versão Enterprise custa cerca de **US$750** por nó, o que torna o processo inviável para algumas empresas._

{% hint style="danger" %}
**ATENÇÃO:** Para fins da prova [Docker Certified Associate **DCA**](https://training.mirantis.com/certification/dca-certification-exam/) a versão Community deve ser utilizada apenas em ambientes de desenvolvimento e não deve ser utilizada em produção. Para produção a única versão a ser utilizada é a Enterprise Edition.
{% endhint %}

A versão Enterprise conta com recursos como o **UCP** (Universal Control Plane) e o **DTR** (Docker Trusted Registry), bem como suporte da Docker Inc.

***

<mark style="color:yellow;">A recomendação mínima para a versão Enterprise do Docker EE é:</mark>

| Managers                                                                       | Workers                                               |
| ------------------------------------------------------------------------------ | ----------------------------------------------------- |
| 8GB de RAM                                                                     | 4GB de RAM                                            |
| 2vCPUs                                                                         |                                                       |
| 10GB de espaço em disco livre para a partição `/var` Mínimo de 6GB Recomendado | 500MB de espaço em disco livre para a partição `/var` |

***

<mark style="color:yellow;">A recomendação para ambientes de Produção do Docker EE é:</mark>

| Managers                             | Workers                                               |
| ------------------------------------ | ----------------------------------------------------- |
| 16GB de RAM                          | 4GB de RAM                                            |
| 4vCPUs                               |                                                       |
| 25 a 100GB de espaço livre em disco. | 500MB de espaço em disco livre para a partição `/var` |

***

## <mark style="color:red;">Namespaces e Cgroups</mark>

O Docker utiliza de recursos do Linux como por exemplo Namespaces e Cgroups dentre vários outros que iremos falar futuramente para isolar os containers que serão executados.

|                          Namespaces                          |                   Cgroups                   |
| :----------------------------------------------------------: | :-----------------------------------------: |
|                      **PID**: Process ID                     |   **CPU**: Divisão de CPU por containers.   |
|                     **MNT**: Mount Points                    | **CPUSet**: CPU Masks, para limitar threads |
|             **IPC**: Comunicação Inter Processos             |             **Memory**: Memória             |
| **UTS**: Unix Time-sharing System (Kernel e Identificadores) |           **Device**: Dispositivos          |
|                      **NET**: Networking                     |                                             |

***

Os Namespaces fornecem isolamento para os containers, limitando seu acesso aos recursos do sistema e a outros Namespaces.

* Isto significa, por exemplo, que um usuário root dentro de um container é diferente de um usuário root da máquina hospedeira.
* Com o isolamento, os sistemas em execução nos containers tem suas próprias árvores de processo, sistemas de arquivos, conexões de rede e muito mais.

Os containers trabalham com Cgroups _**(Control Groups)**_ que fazem isolamento dos recursos físicos da máquina.&#x20;

* Em geral os Cgroups podem ser utilizados para controlar estes recursos tais como limites e reserva de CPU, limites e reserva de memória, dispositivos, etc…

***
