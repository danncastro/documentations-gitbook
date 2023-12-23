---
description: >-
  Um container funcionará junto do nosso sistema operacional base, e conterá a
  nossa aplicação, ou seja, a aplicação será executada dentro dele.
---

# Contêineres

***

Criamos um container para cada aplicação, e esses containers vão dividir as funcionalidades do sistema operacional:

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Fonte: Google</p></figcaption></figure>

***

Não temos mais um sistema operacional para cada aplicação, já que agora as aplicações estão dividindo o mesmo sistema operacional, que está em cima do nosso hardware.&#x20;

> Os próprios containers terão a lógica que se encarregará dessa divisão.

Assim, com somente um sistema operacional, reduzimos os custos de manutenção e de infraestrutura como um todo.

***

## <mark style="color:red;">Vantagens de um container</mark>&#x20;

Por não possuir um sistema operacional, o container é muito mais leve e não possui o custo de manter múltiplos sistemas operacionais, já que só teremos um sistema operacional, que será dividido entre os containers.

> Além disso, por ser mais leve, o container é muito rápido de subir, subindo em questão de segundos.&#x20;

Logo, o container é uma solução para suprir o problema de múltiplas máquinas virtuais em um hardware físico, já que com o container, nós dividimos o sistema operacional entre as nossas aplicações.

***

## <mark style="color:red;">Necessidade do uso de containers</mark>&#x20;

Mas por que precisamos dos containers, não podemos simplesmente instalar as aplicações no nosso próprio sistema operacional?&#x20;

> Até por que já fazemos isso, já que no nosso sistema operacional temos um editor de texto, terminal, navegador, etc.

{% hint style="info" %}
No caso das nossas aplicações, essa abordagem pode ter alguns problemas.&#x20;
{% endhint %}

Por exemplo, se dois aplicativos precisarem utilizar a mesma porta de rede? Precisaremos de algo para isolar uma aplicação da outra. E se uma aplicação consumir toda a CPU, a ponto de prejudicar o funcionamento das outras aplicações? Isso acontece se não limitarmos a aplicação.&#x20;

* Outro problema que pode ocorrer é cada aplicação precisar de uma versão específica de uma linguagem, por exemplo, uma aplicação precisa do **Java 7** e outra do **Java 8**.&#x20;

> Além disso, uma aplicação pode acabar congelando todo o sistema.&#x20;

Por isso é bom ter essa separação das aplicações, isolar uma da outra, e _**isso pode ser feito com os containers.**_

* Com os containers, conseguimos _**limitar o consumo de CPU**_ das aplicações, melhorando o controle sobre o uso de cada recurso do nosso sistema _**(CPU, rede, etc)**_.&#x20;
* Também temos uma facilidade maior em trabalhar com versões específicas de _**linguagens/bibliotecas**_, além de ter uma agilidade maior na hora de criar e subir containers, já que eles são mais leves que as máquinas virtuais.

***

<mark style="color:yellow;">\[Caso de uso]</mark>&#x20;

Eduardo está encarregado de convencer seu chefe a utilizar containers na infraestrutura de sua empresa. Para isso criou alguns slides para apresentar suas ideias. Um slide específico está intitulado _**"Benefícios dos Containers"**_, com os tópicos abaixo.&#x20;

Contudo, um deles não é um benefício do uso de containers. Ajude Eduardo apontando qual alternativa está _**errada**_.

***

{% hint style="success" %}
Melhor controle do uso dos recursos do sistema operacional.&#x20;
{% endhint %}

* Esse tópico deve ser mantido no slide, pois ele é um benefício dos containers. É possível restringir os recursos de SO para cada container, permitindo melhor controle.

***

{% hint style="success" %}
Agilidade na hora de criar e remover aplicações
{% endhint %}

* Esse tópico deve ser mantido no slide, pois ele é um benefício dos containers. O fato de não precisar de bootar o SO e as aplicações tornam containers muito rápidos.

***

{% hint style="warning" %}
O fato de ter um sistema operacional instalado em cada container permite o isolamento de conflitos de rede e versões.
{% endhint %}

* Eduardo precisa remover esse tópico, porque além de não ser um benefício, está errado afirmar que cada container possui um SO instalado.

***

{% hint style="success" %}
Maior facilidade na hora de trabalhar com diferentes versões de bibliotecas e linguagens
{% endhint %}

* Esse tópico deve ser mantido no slide, pois ele é um benefício dos containers.

***
