---
description: >-
  Agora que rodamos nosso primeiro container, precisamos entender alguns
  componentes básicos da sua arquitetura e seu funcionamento.
---

# Componentes

Ao executar o container com a imagem `hello-world` o Docker fez os seguintes passos:

1. O `Docker Client` se comunicou com o `Docker Daemon`.
2. O `Docker Daemon` fez o download da imagem `hello-world` no Docker Hub.
3. O `Docker Daemon` criou um novo container, através da imagem que rodou o executável que produz o texto que vimos no terminal.
4. O `Docker Daemon` enviou o texto diretamente para o `Docker Client` que enviou para nosso terminal.

### Docker Client

O Docker Client é o pacote `docker-ce cli`  que fornece os comandos do lado do cliente, como por exemplo o comando `docker container run`, que irá interagir com o Docker Daemon

### Docker Daemon

O Docker Daemon é o pacote `docker-ce,` ele é o servidor propriamente dito, que receberá os comandos através do Docker Client e fornecerá os recursos de virtualização a nível de sistema operacional.

### Docker Registry

O Docker Registry é o local de armazenamento de imagens Docker, **"normalmente o Docker hub"**, de onde o Docker Daemon receberá as imagens a serem executadas no processo de criação de um container.
