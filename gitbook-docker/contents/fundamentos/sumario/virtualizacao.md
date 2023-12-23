---
description: >-
  Utilizar um servidor físico separado para cada serviço do nosso sistema gera
  vários problemas, entre eles o custo de luz e rede, e o seu alto tempo de
  ociosidade.
---

# Virtualização

***

## <mark style="color:red;">A evolução dos hosts de aplicações</mark>&#x20;

Antigamente, quando queríamos montar o nosso sistema, com vários serviços (_bancos de dados, proxy, etc_) e aplicações, acabávamos tendo vários servidores físicos, cada um com um serviço ou aplicação do nosso sistema, por exemplo \[_**apache, nginx, tomcat, mysql, mongodb, microsoftiis, serv...serv...serv...]**_

> _É claro, não conseguimos instalar os serviços diretamente no hardware do servidor, ou seja, precisamos de um intermediário entre as aplicações e o hardware, que é o sistema operacional._&#x20;

* Ou seja, devemos instalar sistemas operacionais em cada servidor, e os sistemas poderiam ser diferentes: \[_**apache, nginx, tomcat, mysql, mongodb, microsoftiis, os...os...os...serv...serv...serv].**_ E se quisermos que uma aplicação se comunique com outra ou faça qualquer comunicação externa, devemos conectar os servidores à rede.&#x20;

> _Além disso, para eles funcionarem, precisamos ligá-los à eletricidade._&#x20;

Logo, havia diversos custos de eletricidade, rede e configuração dos servidores.

> _Além disso, o processo era lento, já que a cada nova aplicação, deveríamos **comprar/montar** o servidor físico, instalar o sistema operacional, configurá-lo e subir a aplicação._

Capacidade pouco aproveitada. O que foi falado anteriormente não era o único problema desse tipo de arquitetura.

Era muito comum termos servidores parrudos, com uma única aplicação sendo executada, para normalmente ficarem funcionando abaixo da sua capacidade, para quando for necessário, o servidor aguentar uma grande quantidade de acessos.&#x20;

{% hint style="warning" %}
Isso resultava em muita capacidade ociosa nos servidores, com muitos recursos desperdiçados.
{% endhint %}

***

### _<mark style="color:yellow;">**Virtualização, uma solução?**</mark>_&#x20;

Para fugir desses problemas de servidores ociosos, custo e tempo altos para subir e manter aplicações em servidores físicos, surgiu como solução a [Virtualização](https://aws.amazon.com/pt/what-is/virtualization/), surgindo assim as máquinas virtuais.

As máquinas virtuais foram possíveis de ser criadas graças a uma tecnologia chamada [Hypervisor](https://www.vmware.com/br/topics/glossary/content/hypervisor.html), que funciona em cima do sistema operacional, permitindo a virtualização dos recursos físicos do nosso sistema.&#x20;

* Assim, criamos uma máquina virtual que tem acesso a uma parte do nosso _**HD, memória RAM e CPU**_, criando um computador dentro de outro:

{% hint style="success" %}
"VM" - Virtual Machine
{% endhint %}

> _Hypervisor OS Hardware físico_

Se temos uma máquina virtual que está acessando uma parte do nosso hardware como um todo, conseguimos colocar dentro dela uma das nossas aplicações. E replicar isso, criando mais máquinas virtuais com outras aplicações:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Hypervisor OS Hardware físico</p></figcaption></figure>

***

Assim, reduzimos a quantidade de servidores e consequentemente os custos com luz e rede.&#x20;

> _Além disso, dividimos melhor o nosso hardware, aproveitando melhor os seus recursos e diminuindo a ociosidade._

***

### <mark style="color:yellow;">Problemas das máquinas virtuais</mark>

Apesar de resolver os problemas do uso de vários servidores físicos, as máquinas virtuais também possuem problemas.&#x20;

> Para podermos hospedar a nossa aplicação em uma máquina virtual, também precisamos instalar um sistema operacional nela:

* Cada aplicação necessita de um sistema operacional para poder ser executada, e esses sistemas podem ser diferentes.&#x20;

> _E apesar dos sistemas operacionais serem um software, eles possuem um custo de memória RAM, disco e processamento._&#x20;

* Ou seja, precisamos de uma capacidade mínima para manter as funcionalidades de um sistema operacional, aumentando o seu custo de manutenção a cada sistema que tivermos.

{% hint style="danger" %}
Também devemos sempre estar atentos à sua segurança, mantendo o sistema de cada máquina virtual sempre atualizado.
{% endhint %}

Muitas vezes, o tempo voltado para a manutenção das máquinas virtuais era o mesmo tempo voltado para a nossa aplicação em si.&#x20;

* Ou seja, acabávamos dividindo o valor da nossa empresa, ao invés de focar somente nas aplicações, dividíamos o trabalho com a manutenção dos sistemas operacionais.

A solução para esses problemas foi a virtualização dos recursos físicos, reduzindo assim os custos de luz e rede, já que não teremos mais vários servidores físicos, e não teremos mais ociosidade do hardware.

> Mas as máquinas virtuais também possuem problemas, que estão listados abaixo, exceto um.&#x20;

***

{% hint style="warning" %}
Como as máquinas virtuais possuem sistemas operacionais, os mesmos possuem um custo de hardware para manter suas funcionalidades. Esse é um problema das máquinas virtuais!
{% endhint %}

* Precisamos instalar um sistema operacional em cada uma delas, que por sua vez possui um custo de hardware para funcionar.&#x20;
* Se houver várias máquinas virtuais, o seu custo de manutenção acaba se tornando bem alto.

***

{% hint style="warning" %}
Como as máquinas virtuais possuem sistemas operacionais, os mesmos possuem configurações iniciais a serem feitas, e devem ser atualizados com frequência. Esse é um problema das máquinas virtuais!&#x20;
{% endhint %}

* Como devemos instalar sistemas operacionais nelas, eles devem ser configurados (_liberação de portas, instalação de bibliotecas, etc_) e estar sempre atualizados, principalmente por questões de segurança.

***

{% hint style="warning" %}
Manter vários sistemas operacionais requer muito tempo de trabalho, chegando a equivaler ao tempo requerido para manter as aplicações Esse é um problema das máquinas virtuais!&#x20;
{% endhint %}

* Manter vários sistemas operacionais gera trabalho, consumindo tempo que poderia estar sendo utilizada para **manter/criar aplicações**.

***

{% hint style="success" %}
A dificuldade em fazer um backup dos dados. Esse não é um problema das máquinas virtuais!&#x20;
{% endhint %}

* O backup dos dados de uma máquina virtual pode ser feito com certa facilidade, seja por softwares externos ou através das próprias VMs.

***
