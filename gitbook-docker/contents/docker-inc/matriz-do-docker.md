---
description: >-
  Além disso, os containers oferecem maior flexibilidade para você criar,
  implantar, copiar e migrar um container de um ambiente para outro.
---

# Matriz do Docker

***

> Isso otimiza as aplicações em nuvem (privada e pública).

***

## <mark style="color:red;">Modelo Cliente-Servidor</mark>

O modelo **cliente-servidor** é uma estrutura de aplicação que distribui as tarefas e cargas de trabalho entre os fornecedores de um recurso ou serviço, designados como _servidores_, e os requerentes dos serviços, designados como _clientes_.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Fonte: Formação Docker Fundamentals DIO</p></figcaption></figure>

***

## <mark style="color:red;">Cloud</mark>

A cloud computing é o acesso sob demanda, via internet, _a recursos de computação_, _aplicativos, servidores (físicos e virtuais)_, _armazenamento de dados, ferramentas de desenvolvimento, recursos de rede_ e muito mais,  hospedados em um data center remoto gerenciado por um provedor de serviços em cloud (_**Cloud Solution Provider**_).&#x20;

> _O CSP disponibiliza esses recursos por uma assinatura mensal ou por um valor cobrado conforme o uso._

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Fonte: https://www.ibm.com/br-pt/cloud/learn/cloud-computing</p></figcaption></figure>

***

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Fonte: https://aws.amazon.com/pt/resources/analyst-reports/gartner-mq-cips-2021/</p></figcaption></figure>

***

{% embed url="https://www.youtube.com/watch?t=126s&v=zDAYZU4A3w0" %}
Data Center Google
{% endembed %}

***

{% embed url="https://www.youtube.com/watch?v=ewLJ2w3KNcU" %}
Data Center da Microsoft (no fundo do mar???)
{% endembed %}

***

## <mark style="color:red;">Virtualização</mark>

A virtualização utiliza software para criar uma camada de abstração sobre o hardware do computador, permitindo que os recursos de hardware de um único computador (_processadores, memória, armazenamento, etc_) sejam divididos em vários computadores virtuais.

{% embed url="https://www.vmware.com/br/timeline.html" %}
Vmware
{% endembed %}

***

{% embed url="https://docs.microsoft.com/pt-br/windows-server/virtualization/hyper-v/hyper-v-technology-overview" %}
Microsoft Hyper-V
{% endembed %}

***

## <mark style="color:red;">Microserviços</mark>

Microsserviços são uma abordagem arquitetônica e organizacional do desenvolvimento de software na qual o software consiste em pequenos serviços independentes que se comunicam usando APIs bem definidas.&#x20;

* Esses serviços pertencem a pequenas equipes autossuficientes.
* Hoje, gigantes do mercado como _Netflix e Spotify_, divulgam a receita do sucesso ao transformar suas aplicações **monolíticas** em mais de _**500 microsserviços.**_

> _As arquiteturas de microsserviços facilitam a escalabilidade e agilizam o desenvolvimento de aplicativos, habilitando a inovação e acelerando o tempo de introdução de novos recursos no mercado._

Quando quebramos uma aplicação monolítica em várias pequenas partes, conseguimos escalá-las de forma separada.&#x20;

Supondo que um serviço de autenticação seja chamado várias vezes durante a sessão de um usuário, com certeza o stress sobre ele é maior.

***

## <mark style="color:red;">O que é um container</mark>

Os contêineres são uma tecnologia usada para reunir um aplicativo e todos os seus arquivos necessários em um ambiente de tempo de execução.&#x20;

* Como uma unidade, o contêiner pode ser facilmente movido e executado em qualquer sistema operacional, e em qualquer contexto.

{% embed url="https://www.ibm.com/br-pt/topics/containers" %}

***

## <mark style="color:red;">O que é Docker?</mark>

Com o Docker, é possível lidar com os containers como se fossem máquinas virtuais modulares e extremamente leves.&#x20;

> _Além disso, os containers oferecem maior flexibilidade para você criar, implantar, copiar e migrar um container de um ambiente para outro._

Isso otimiza as aplicações em nuvem (privada e pública).

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>Fonte: Formação Docker Fundamentals DIO</p></figcaption></figure>

***

## <mark style="color:red;">Qual é a diferença entre virtualização e os containers?</mark>

As duas tecnologias são distintas porém complementares. Veja uma maneira fácil de distinguir ambas:

* Com a virtualização, é possível executar sistemas operacionais (_Windows ou Linux_) simultaneamente em um único sistema de hardware.
* Os containers compartilham o mesmo _**kernel**_ do sistema operacional e isolam os processos da aplicação do restante do sistema.&#x20;

> _Os containers Linux são extremamente portáteis, mas devem ser compatíveis com o sistema subjacente._

***

## <mark style="color:yellow;">Referência</mark>

{% embed url="https://www.youtube.com/watch?list=PLf-O3X2-mxDn1VpyU2q3fuI6YYeIWp5rR&v=Wm99C_f7Kxw" %}
Descomplicando o docker
{% endembed %}

***

{% embed url="https://www.youtube.com/watch?list=PL18bbNo7xuh84zAeNjNejyzd1GASt8Q9x&v=8eGOtu_SZKg" %}
Docker com Erudio
{% endembed %}

***

{% embed url="https://www.youtube.com/watch?list=PL4ESbIHXST_TJ4TvoXezA0UssP1hYbP9_&v=U-GGoWq26C4" %}
Docker DCA
{% endembed %}

***
