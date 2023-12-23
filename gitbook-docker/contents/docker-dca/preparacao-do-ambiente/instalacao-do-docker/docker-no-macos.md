---
description: >-
  Para instalar o Docker no MacOS, utilizamos o Docker for Mac, no qual podemos
  baixar o instalador.
---

# Docker no MacOS

Primeiramente, devemos nos atentar aos requisitos do uso do Docker for Mac, ou seja, devemos possuir um MacOS:

* Modelo 2010 ou mais recente
* Versão OS X El Capitan 10.11 ou mais recente;
* Com no mínimo 4GB de memória RAM;&#x20;
* Sem VirtualBox instalada na versão 4.3.30 ou anterior, pois causa incompatibilidade com o Docker.&#x20;

Caso você não atinja algum desses requisitos, há uma instalação alternativa, o Docker Toolbox.

Com isso, podemos instalar o Docker, clicando no `.dmg` baixado anteriormente e arrastando o Docker para as nossas aplicações. Após isso, já podemos pesquisar pelo Docker, confirmar que queremos utilizá-lo e damos acesso privilegiado a ele, clicando em OK e digitando a senha de administrador em seguida.

No menu superior do MacOS, à direita, o ícone do Docker aparecerá. Ele pode demorar um pouco para inicializar, mas quando a mensagem Docker is now up and running! for exibida, significa que ele já pode ser utilizado.

Funcionamento do Docker no MacOS O Docker é executado em cima de uma micro máquina virtual, chamada Alpine Linux, onde será executada a sua Docker Engine:

Mas para criar máquinas virtuais, o Docker precisa utilizar uma tecnologia chamada de HyperKit, que é um Hypervisor. O problema disto é que o HyperKit só está presente na versão OS X El Capitan 10.11 ou mais recente. Mas uma alternativa é instalar o Docker Toolbox.

Vamos agora então detalhar o processo de instalação para cada esse caso.

Instalação alternativa no MacOS Para instalar o Docker Toolbox, primeiramente devemos baixá-lo aqui.

O Docker Toolbox vai instalar tudo que é necessário para que trabalhemos com o Docker em nosso computador, pois ele irá instalar também a Oracle VirtualBox, a máquina virtual da Oracle que vai permitir executarmos o Docker sem maiores problemas.

A diferença é que, quando trabalhamos com o Docker for Mac, podemos utilizar o terminal nativo do MacOS, pois o terminal está sendo executado dentro do próprio sistema operacional. Já no Docker Toolbox, ele instalará o Docker Machine, que deverá ser utilizado no lugar do terminal nativo do MacOS.
