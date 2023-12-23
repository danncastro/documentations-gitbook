---
description: >-
  Uma imagem Docker é um pacote executável que inclui tudo o que é necessário
  para executar um aplicativo, incluindo o código, bibliotecas, variáveis de
  ambientes e arquivos de configuração.
---

# Imagens

***

## <mark style="color:red;">Docker Image</mark>

As imagens do Docker possuem camadas intermediárias que aumentam a capacidade de reutilização, diminuem o uso do disco e aceleram a construção do docker, permitindo que cada etapa seja armazenada em cache.&#x20;

> _Essas camadas intermediárias não são mostradas por padrão._

* A principal diferença entre um container e uma imagem é a camada gravável superior.&#x20;
* Todas as gravações no container que adicionam novos dados ou modificam dados existentes são armazenados nessa camada gravável.&#x20;

> _Quando o container é excluído, a camada gravável também é excluída. A imagem subjacente permanece inalterada._

***

Um ponto interessante é que em um cenário onde temos 1 container com a imagem `ubuntu:18.04` por exemplo, ocuparia `200MB` ( _estamos considerando este tamanho para a imagem citada_) somados a quantidade de dados específicos deste container ( _vamos considerar `50MB` para este exemplo_) totalizando `250MB`.&#x20;

* o mesmo caso com 10 containers serão utilizados os `200MB` da imagem somados aos 50MB de cada container em execução, pois suas camadas `Read-Only` é compartilhada, totalizando assim `750MB` no total.

> O mesmo cenário em máquinas virtuais seria exponencialmente maior, uma vez que precisamos instalar todo o sistema operacional e parametrizar cada máquina individualmente.

***

### <mark style="color:yellow;">Gerenciar Imagens no Docker</mark>

1. Liste as imagens e verifique o histórico de comandos utilizados para sua construção

```bash
docker image ls
docker image history debian
```

> _O comando `docker image history` mostra as camadas que compõem uma imagem_

***

2. Para inspecionar uma imagem utilizamos o comando `docker image inspect`

```bash
docker image inspect debian
```

> _O comando `docker image inspect` exibe informações detalhadas de uma imagem_

***

3. Vamos criar uma nova imagem a partir de um container existente, criaremos um container e vamos instalar alguns pacotes.

```bash
docker container run -dit --name servidor-debian debian
docker container exec servidor-debian apt-get update
docker container exec servidor-debian apt-get install nginx -y
```

***

4. Para criar uma nova imagem a partir das alterações feitas em um container podemos utilizar o parâmetro `commit`

```bash
docker container commit servidor-debian webserver-nginx
docker image ls
```

> _O comando `docker container commit <container> <imagem>` cria uma imagem a partir de alterações realizadas em um container, este procedimento não é o recomendado para este fim, mais a frente veremos outras soluções_

***

5. Para salvar a imagem podemos utilizar o parâmetro **`save`**

```bash
docker image save webserver-nginx -o imagem-webserver-nginx.tar
du -sh imagem-webserver-nginx.tar
```

***

6. Remova o container e a imagem servidor web

```bash
docker container rm -f servidor-debian
docker image rm webserver-nginx
docker image ls
```

***

7. Para carregar uma imagem salva a partir de um arquivo, podemos utilizar o parâmetro **`load`**

```bash
docker image load -i imagem-webserver-nginx.tar
docker image ls
```

***

8. Para testar o funcionamento da imagem, podemos criar um container utilizando a mesma

```bash
docker container run -dit --name webserver webserver-nginx
docker container ls
```

***

9. Remova todos os containers

```bash
docker container rm -f $(docker container ls -aq)
docker container ls
```

> _No comando acima, estamos passando através de um subshell o comando `docker container ls -aq` que lista todos os containers por **id**, sendo assim, será feita a remoção de todos os containers_

***

## <mark style="color:red;">Dockerfile</mark>

O Dockerfile é um arquivo de instruções de como deve ser gerada a imagem Docker, através deste arquivo podemos criar novas imagens para serem utilizadas.

O Docker pode criar imagens automaticamente, lendo as instruções de um `Dockerfile`, que é um documento de texto que contém as instruções para a criação de uma imagem docker através do comando `docker build.`

***

### <mark style="color:yellow;">Sintaxe</mark>

| Parâmetro  | Valor                                           |
| ---------- | ----------------------------------------------- |
| **FROM**   | Distribuição: Versão                            |
| **COPY**   | Arquivo\_Local:Caminho\_Absoluto\_no\_Container |
| **RUN**    | Comando                                         |
| **EXPOSE** | Porta do serviço                                |
| **CMD**    | Comando executado ao iniciar o Container        |

> O arquivo de Dockerfile não é case-sensitive, no entanto por convenção utilizamos os parâmetros em **maiúsculo** para que sua leitura seja mais agradável e de fácil compreensão.&#x20;

{% hint style="info" %}
O nome do arquivo deve se chamar **Dockerfile** apenas com a letra inicial **D** em maiúsculo.
{% endhint %}

O Docker executará as instruções do Dockerfile em ordem (Top-down) e deverá sempre iniciar com a instrução `FROM`, as linhas que começam com `#` são tratadas como comentário a menos que a linha seja uma diretiva de analisador válida, o caractere `#` em qualquer outro lugar em uma linha é tratado como um argumento.

***

### <mark style="color:yellow;">**Definições**</mark>

* **`FROM`** - Inicializa um novo estágio de compilação e define a imagem de base para instruções subsequentes;

***

* **`COPY`** - Copia arquivos ou diretórios de origem local adicionando-os a imagem do container;

***

* **`ADD`** - Similar ao parâmetro **`COPY`** porém possibilita que a origem seja uma _**URL**_ bem como a alteração de permissão ao adicionar os arquivos a imagem do container;

***

* **`RUN`** - Executa os comandos em uma nova camada na parte superior a imagem atual, é uma boa prática combinar diversos comandos em um único **`RUN`** utilizando de **`;`** e **`&&`** para a combinação, assim criando apenas uma camada;

***

* **`EXPOSE`** - Informa ao docker a porta na qual o container estará escutando enquanto estiver sendo executado, é possível especificar portas _**TC**_P e _**UDP**_, caso não seja declarado o tipo de porta, o padrão _**(TCP) é assumido**_.

***

* **`CMD`** - Só pode existir uma única instrução deste tipo em um arquivo, o propósito desta instrução é prover os padrões para a execução do container, podendo ser um executável ou até mesmo opções para o executável definido na instrução **`ENTRYPOINT`**

***

* **`ENTRYPOINT`** - Possibilita configurar o container para rodar como um executável, o comando `docker run <image>` inicializará o container em seu **`ENTRYPOINT`** somado ao **`CMD`** se existente.

***

### <mark style="color:yellow;">Criando Dockerfiles</mark>

1. Vamos criar um diretório para armazenar os Dockerfiles e criar nosso primeiro Dockerfile

```bash
mkdir -p ~/dockerfiles/echo-container
cd ~/dockerfiles/echo-container
vim Dockerfile
```

```dockerfile
FROM         alpine
ENTRYPOINT   ["echo"]
CMD          ["--help"]
```

***

2. Para criar a imagem a partir do Dockerfile, utilizamos o comando `docker image build`

```bash
docker image build -t echo-container:v1 .
docker image ls
```

> _A opção `-t` significa `TAG`, após o nome da `imagem:tag` informamos qual o diretório em que o Dockerfile se encontra, utilizamos o ponto `( . )` para dizer que o Dockerfile está no diretório atual `(PWD)`_

***

3. Execute o container com a imagem criada

```bash
docker container run --rm -it echo-container:v1
```

> _A opção **`--rm`** informa que o container será apagado após cumprir seu papel_

***

4. Execute o container com a imagem criada alterando seu `CMD`

```bash
docker container run --rm -it echo-container:v1 echo "Container DevOps"
```

> _Ao passar um parâmetro após o nome da imagem estamos alterando o CMD do container, anteriormente_ `echo --help` _para_ `echo "Container DevOps"`

***

### <mark style="color:yellow;">Dockerfile Servidor WEB</mark>

1. Vamos criar um diretório para armazenar o Dockerfile para nosso servidor WEB

```bash
mkdir  ~/dockerfiles/webserver
cd ~/dockerfiles/webserver
vim Dockerfile
```

```dockerfile
FROM    debian
RUN     apt-get update; \
        apt-get install wget git apache2 -yq
EXPOSE  80          
CMD     ["apachectl", "-D", "FOREGROUND"]
```

***

2. Crie a imagem

```bash
docker image build -t webserver:v1 .
docker image ls
```

***

### <mark style="color:yellow;">Enviando a imagem para o Dockerhub</mark>

Para enviar uma imagem para o DockerHub é necessário que sejam feitos 3 passos:

1. docker login
2. docker image tag
3. docker push

***

1. Vamos efetuar o login com nossa conta no dockerhub

```bash
docker login -u <usuario_dockerhub>
```

***

2. Agora precisamos criar a tag da imagem, que deve seguir o padrão **`usuário/imagem:versão`**

```bash
docker image tag echo-container <usuario_dockerhub>/echo-container:latest
docker image tag webserver <usuario_dockerhub>/webserver
```

{% hint style="info" %}
_Caso não seja informada uma versão, o docker entende que a versão trata-se da **latest**_
{% endhint %}

***

3. Agora podemos enviar as imagens para o DockerHub

```bash
docker image push <usuario_dockerhub>/echo-container
docker image push <usuario_dockerhub>/webserver
```

***

4. Ao finalizar, lembre-se de efetuar o logout em sua conta do DockerHub

```bash
docker logout
```

***

> _As imagens enviadas podem ser visualizadas na sua página do DockerHub e todos os usuários podem efetuar o download da mesma com o comando `docker image pull usuário/imagem:versão` desde que o repositório seja público._

***

### <mark style="color:yellow;">Melhores práticas com o Dockerfile</mark>

Quando criamos uma imagem, através do comando `docker image build` , a imagem definida pelo `Dockerfile` deve gerar containers que são tão efêmeros quanto possível, isso quer dizer que o container deve poder ser parado e/ou destruído a qualquer momento, e reconstruído ou substituído com o mínimo de configuração ou atualização.

> _Uma boa metodologia para conseguir chegar neste ponto é a do **Twelve-factor App**, ou Aplicação de Doze fatores, e em sua seção de_ [_processos_](https://12factor.net/pt\_br/processes) _podemos verificar algumas das motivações para subir containers maneira **stateless (não armazenam estado)**._

***

### <mark style="color:yellow;">Entendendo o contexto de Build</mark>

Quando executamos o comando `docker image build` , o diretório no qual apontamos (muitas das vezes como `.` para referir o diretório atual) é chamado de `build context`.&#x20;

{% hint style="info" %}
Por padrão o Docker espera que o _**`Dockerfile`**_ esteja localizado nesta pasta, mas também podemos especificar uma nova localização através da flag `-f`.&#x20;
{% endhint %}

* Independentemente de onde o Dockerfile esteja, todo o conteúdo dos diretórios recursivamente e arquivos é enviado para o _`Docker daemon`_ como _`build context`_.

> _Por isto não devemos, por exemplo, criar um `Dockerfile` diretamente em nossa home `~/Dockerfile` , uma vez que todo o conteúdo, inclusive o cache de navegadores web e todas aplicações `~/.cache` será enviado para o Docker daemon, muitas das vezes falhando a build ou até mesmo fazendo com que ela demore muito tempo._

***

1. Primeiramente vamos criar um diretório para guardar nosso `Dockerfile` e criar um arquivo com um conteúdo estático

```bash
mkdir -p ~/dockerfiles/exemplo1
cd ~/dockerfiles/exemplo1
echo "Dockerfile Melhores Práticas" > conteudo.txt
```

***

2. Agora podemos criar nosso Dockerfile:

```bash
vim Dockerfile
```

```dockerfile
FROM  busybox
COPY  conteudo.txt /
RUN   cat /conteudo.txt
```

***

3. Agora vamos criar a imagem.

```bash
docker image build -t exemplo:v1 . 
```

***

4. Agora vamos criar novos diretórios e mover o arquivo `conteudo.txt` para um diretório diferente do Dockerfile para construir uma segunda imagem.

```bash
mkdir -p image context
mv Dockerfile image
mv conteudo.txt context
docker image build --no-cache -t exemplo:v2 -f image/Dockerfile context
```

> _As duas imagens tem o mesmo tamanho e o mesmo conteúdo, porém note que as imagens tem o `ID` diferente porque criamos a imagem sem utilizar o cache `--no-cache`, ou seja, criamos uma imagem totalmente nova._

***

```bash
$ docker image ls 
```

> REPOSITORY        TAG              IMAGE ID                          CREATED                             SIZE
>
> exemplo                 v2                589078e3e007                2 seconds ago                    1.24MB
>
> exemplo                 v1                de0bdd45cb9a                3 minutes ago                     1.24MB

{% hint style="warning" %}
Caso sejam incluídos arquivos que não são necessários para a construção da imagem o `build context` se tornará maior e consequentemente uma imagem maior.&#x20;
{% endhint %}

Isso pode aumentar o tempo de construção, envio e download da imagem e do `container runtime`.&#x20;

> Para ver o tamanho do build context basta verificar a mensagem exibida quando executar o build do seu `Dockerfile`.

***

5. Vamos copiar todo o conteúdo do diretório `/var/log` para o context e construir a imagem.

```bash
sudo cp -r /var/log/ ~/dockerfiles/exemplo1/context/
sudo chown -R vagrant:vagrant ~/dockerfiles/exemplo1/context/log
docker image build --no-cache -t exemplo:v3 -f image/Dockerfile context
```

Veja que o context que anteriormente era de apenas `2.6KB` desta vez foi de `26,62MB`, o que resulta em um tempo de build maior porém sua imagem continua do mesmo tamanho das outras já que o arquivo foi enviado para o context e não foi utilizado.

> &#x20;Sending build context to Docker daemon  2.607kB
>
> &#x20;Sending build context to Docker daemon  26.62MB

{% hint style="info" %}
Tratando de poucos `MB` o tempo de construção pode não ser muito expressivo, porém imagine em uma grande aplicação com diversos arquivos.&#x20;
{% endhint %}

> Utilizando o comando `time` fiz a medição no caso de `2.6KB` que gerou a build em `0m0.910s` contra `0m1.368s` do arquivo de `26,62MB` esse tempo pode ser superior caso a imagem execute diversos comandos em diversas camadas.

***

### <mark style="color:yellow;">Excluindo arquivos do build</mark>

Para excluir arquivos que não são relevantes a build, podemos criar um arquivo `.dockerignore` contendo os padrões de exclusão similares aos do `.gitignore` possibilitando que ignoremos arquivos no build sem ter que modificar nosso repositório.

> Para a referência do Docker Ignore veja a [Documentação Oficial](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

***

1. Vamos criar agora um arquivo `.dockerignore` para que o diretório `log` não seja enviado para a build.

```bash
vim context/.dockerignore
# Comentario: Ignorando arquivos do diretorio log
log
```

```bash
docker image build --no-cache -t exemplo:v4 -f image/Dockerfile context
```

> Veja que o diretório `log` foi ignorado, uma vez que o `build context` ficou em `2.6kB` (agora um pouco maior que a primeira por possuir o arquivo `.dockerignore`) ao invés dos `26MB` anteriores.

***

## <mark style="color:red;">Dicas</mark>

Vamos criar um diretório para o exemplo a seguir para guardar nosso Dockerfile e fazer o download de uma aplicação exemplo em java que conta o numero de caracteres de um texto.

```bash
mkdir -p ~/dockerfiles/dicas
cd ~/dockerfiles/dicas
git clone https://github.com/caiodelgadonew/java-wc-app.git app
```

***

### <mark style="color:yellow;">Dica #1: A ordem importa para o cache</mark>

A ordem dos passos de build é importante, se o cache de um primeiro passo é invalidado pela modificação de arquivos ou linhas do Dockerfile, os arquivos subsequentes do build quebrarão.

{% hint style="info" %}
Sempre faça a ordenação dos passos do que sofrerá menos mudanças para o que sofrerá mais mudança.
{% endhint %}

```bash
vim Dockerfile
```

```dockerfile
FROM        debian:9
RUN         apt-get update
RUN         apt-get install -y openjdk-8-jdk wget ssh vim
COPY        app /app
ENTRYPOINT  ["java", "-jar", "/app/target/app.jar"]
```

```bash
docker image build -t dicas:v1 .
```

***

### <mark style="color:yellow;">Dica #2: COPY mais específico para limitar a quebra de cache</mark>

{% hint style="info" %}
Só copie o necessário.&#x20;
{% endhint %}

* Se possível evite o `COPY`.&#x20;

Quando copiamos arquivos para nossa imagem, tenha certeza que você está sendo bem específico sob o que quer copiar, qualquer mudança no arquivo copiado quebrará o cache.

***

1. Copiaremos então apenas a aplicação para a imagem, desta maneira as mudanças nos arquivos não afetarão o cache.

```bash
vim Dockerfile
```

```dockerfile
FROM        debian:9
RUN         apt-get update
RUN         apt-get install -y openjdk-8-jdk wget ssh vim
COPY        app/target/app.jar /app/app.jar
COPY        app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v2 .
```

***

### <mark style="color:yellow;">Dica #3: Identifique as instruções que podem ser agrupadas</mark>

Cada instrução `RUN` cria uma unidade de cache e uma nova camada de imagem, agrupar todos os comandos `RUN` em uma única instrução pode melhorar o desempenho e diminuir a quantidade de camadas uma vez que eles se tornarão uma unidade única cacheavel.

```bash
vim Dockerfile
```

```dockerfile
FROM       debian:9
RUN        apt-get update \
        && apt-get install -y \
            openjdk-8-jdk wget \
            ssh vim   
COPY        app/target/app.jar /app/app.jar
COPY        app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v3 .
```

***

### <mark style="color:yellow;">Dica #4: Remova as dependências desnecessárias</mark>

Remover as dependencias desnecessárias e não instalar pacotes de debug é uma boa prática, como por exemplo trocar o `jdk` _(Java Development Kit)_ pelo `jre` (_Java Runtime Environment_) que é um pacote relativamente menor e contém apenas o necessário para execução.&#x20;

> Você pode instalar as ferramentas de debug posteriormente caso necessite.&#x20;

O instalador de pacotes `apt` possui uma flag `--no-install-recommends` que garante que dependencias que não são necessárias não sejam instaladas. Caso precise, adicione elas explicitamente.

```bash
vim Dockerfile
```

```dockerfile
FROM        debian:9
RUN         apt-get update \
         && apt-get install -y --no-install-recommends \
            openjdk-8-jre 
COPY        app/target/app.jar /app/app.jar
COPY        app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v4 .
```

> Com a **Dica #4** podemos notar uma diminuição considerável no tamanho de nossa imagem.

***

### <mark style="color:yellow;">Dica #5: Remover o cache do gerenciador de pacotes</mark>

O gerenciador de pacotes mantem seu próprio cache, o `apt` por exemplo guarda seu cache no diretório `/var/lib/apt/lists` e `/var/cache/apt/`.&#x20;

{% hint style="info" %}
Uma das maneiras de lidar com este problema é remover o cache na mesma instrução que o pacote foi instalado.&#x20;
{% endhint %}

* Remover este cache em outra instrução não irá diminuir o tamanho da imagem.

```bash
vim Dockerfile
```

```dockerfile
FROM        debian:9
RUN         apt-get update \
         && apt-get install -y --no-install-recommends \
            openjdk-8-jre \
         && rm -rf /var/lib/apt/lists \
         && rm -rf /var/cache/apt
COPY        app/target/app.jar /app/app.jar
COPY        app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v5 .
```

> Agora com a **Dica #5** nossa imagem ficou relativamente menor.

***

### <mark style="color:yellow;">Dica #6: Utilize imagens oficiais quando possível</mark>

Imagens oficiais podem ajudar muito e reduzir bastante o tempo preparando a imagem, isto porque os passos de instalação já vem prontos e normalmente com as melhores praticas aplicadas, isto também fará você ganhar tempo caso tenha múltiplos projetos, eles compartilham as mesmas camadas e utilizam a mesma imagem base.

```bash
vim Dockerfile
```

```dockerfile
FROM       openjdk
COPY       app/target/app.jar /app/app.jar
COPY       app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v6 .
```

***

### <mark style="color:yellow;">Dica #7: Utilize Tags mais específicas</mark>

{% hint style="danger" %}
Nunca utilize a tag `latest`
{% endhint %}

Ela pode receber alguma atualização e em um momento de update sua aplicação pode quebrar, dependendo de quanto tempo passou do seu último build.&#x20;

> Ao invés disso, utilize tags mais específicas.

```bash
vim Dockerfile
```

```dockerfile
FROM       openjdk:8
COPY       app/target/app.jar /app/app.jar
COPY       app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v7 .
```

***

### <mark style="color:yellow;">Dica #8: Procure por flavors mínimos</mark>

Existem diversos flavors Linux que fazem com que nossa imagem se torne cada vez menor, um bom exemplo são as imagens `slim` e `alpine` as quais são as menores encontradas.&#x20;

> A imagem `slim` é baseada no Debian, enquanto a `alpine` é baseada em uma distribuição Linux muito menor chamada Alpine.&#x20;

* A diferença básica entre elas é que o debian utiliza a biblioteca `GNU libc` enquanto o alpine utiliza `musl lbc`, que apesar de muito menor, pode ter problemas de compatibilidade.

```bash
docker image pull openjdk:8
docker image pull openjdk:8-jre
docker image pull openjdk:8-jre-slim
docker image pull openjdk:8-jre-alpine
docker image ls | egrep "REPOSITORY|openjdk"
```

> &#x20;REPOSITORY                                                  TAG         	                                  SIZE
>
> &#x20;openjdk                                                           8           	                                          510MB
>
> &#x20;openjdk                                                           8-jre       	                                  265MB
>
> &#x20;openjdk                                                           8-jre-slim  	                                  184MB
>
> &#x20;openjdk                                                           8-jre-alpine	                                  84.9MB

***

```bash
vim Dockerfile
```

```dockerfile
FROM       openjdk:8-jre-alpine
COPY       app/target/app.jar /app/app.jar
COPY       app/samples /samples
ENTRYPOINT  ["java", "-jar", "/app/app.jar"]
```

```bash
docker image build -t dicas:v8 .
```

> Agora temos uma diminuição enorme em nossa imagem pois estamos utilizando uma imagem base bem menor.

***

### <mark style="color:yellow;">Dica #9: Multi-stage build</mark>

Multi-stage build é um recurso muito poderoso que apareceu a partir do _**docker 17.05**_. Multi-stage builds são uteis para quem quer otimizar `Dockerfiles` enquanto mantém eles fáceis de ler e manter.

Antes do Multi-stage build o maior desafio das imagens é de fato manter as imagens pequenas, vimos nos exemplos anteriores que conseguimos, ao utilizar algumas das melhores práticas, diminuir bastante o tamanho da imagem.&#x20;

> Utilizando imagens slim ou alpine resolvem boa parte dos nossos problemas mas quando precisamos resolver algo mais complexo podemos utilizar elas somadas ao multi-stage build.

* O `multi-stage build` faz com que possamos utilizar diversas instruções `FROM` em um Dockerfile, e cada instrução pode utilizar uma imagem diferente, fazemos isto por exemplo para subir uma imagem, dentro desta imagem instalar os pacotes e coletar apenas os arquivos necessários diretamente para a imagem subsequente.&#x20;
* Com isso temos uma imagem muito mais enxuta e otimizada.

***

1. Por exemplo vamos criar esta imagem em `GO` pelo processo normal:

```bash
git clone https://github.com/alexellis/href-counter.git ~/dockerfiles/multistage
cd ~/dockerfiles/multistage
rm Docker*
vim Dockerfile
```

```dockerfile
FROM     golang:1.7.3
WORKDIR  go/src/github.com/alexellis/href-counter/
RUN      go get -d -v golang.org/x/net/html  
COPY     app.go .
RUN      CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .
CMD      ["./app"]
```

```bash
docker  image build -t multistage:v1 . 
```

> Esta imagem ficou com o tamanho de `700MB`, porém o que precisamos nela é apenas o diretório `/go/src/github.com/alexellis/href-counter/app`, podemos então utilizar o multi-stage build para recolher estes arquivos, chamando a primeira imagem de builder através do parâmetro `AS <nome>` e depois invocar a imagem em um segundo estágio através do parâmetro `--from=<nome>`.

***

```bash
vim Dockerfile
```

```dockerfile
FROM     golang:1.7.3 AS builder
WORKDIR  /go/src/github.com/alexellis/href-counter/
RUN      go get -d -v golang.org/x/net/html
COPY     app.go    .
RUN      CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM     alpine:latest
RUN      apk --no-cache add ca-certificates
WORKDIR  /root/
COPY     --from=builder /go/src/github.com/alexellis/href-counter/app .
CMD      ["./app"]
```

```bash
docker  image build -t multistage:v2 . 
```

***

2. Agora tivemos uma "pequena" redução de `700MB` para `12MB`

```bash
docker image ls | egrep "REPOSITORY|multistage"
```

```bash
REPOSITORY        TAG            IMAGE ID       CREATED          SIZE
multistage        v1             dae3f0761024   28 minutes ago   700MB
multistage        v2             a1108a2b5102   33 minutes ago   11.7MB
```

***

3. Caso deseje testar a imagem basta executar o comando

```bash
docker container run --rm -it -e url=https://youtube.com/caiodelgadonew multistage:v1
docker container run --rm -it -e url=https://youtube.com/caiodelgadonew multistage:v2
```

> Outra coisa interessante é que ao invés de utilizar uma imagem completa podemos puxar um arquivo de uma imagem já criada anteriormente através da flag `--from=<image>:<tag>`, vamos fazer a cópia de um arquivo da imagem da [dica 7](imagens.md#dica-7-utilize-tags-mais-especificas) para a imagem do multi-stage, para isto criaremos um diretório chamado `multistage2`.

```bash
mkdir -p ~/dockerfiles/multistage2
cd ~/dockerfiles/multistage2
vim Dockerfile
```

```dockerfile
FROM     alpine:latest
WORKDIR  /root/
COPY     --from=dicas:v7 /samples/1.txt .
CMD      ["cat", "1.txt"]
```

```bash
docker image build -t multistage:v3 .
```

***

3. Podemos agora executar nosso container para verificar se o `arquivo 1.txt` é de fato o arquivo extraído da imagem `dica:v7`

```bash
docker container run --rm -it multistage:v3
```

> _É sempre bom tentar diminuir as imagens de docker e seguir as melhores práticas, isso faz com que nosso tempo de deploy ou scale da aplicação seja menor, bem como a necessidade de um armazenamento maior e diversos outros fatores._

***

### <mark style="color:yellow;">Removendo todas as imagens</mark>

```bash
docker image prune -a
```

***

## <mark style="color:red;">Dockerhub</mark>

Estas imagens ficam armazenadas em repositórios locais e/ou remotos, um exemplo de repositório remoto é o [DockerHub](https://hub.docker.com/) que é um repositório publico de imagens docker onde podemos escolher e utilizar as imagens para subir nossos containers.

***

### <mark style="color:yellow;">Serviços Fornecidos pelo</mark> <mark style="color:yellow;"></mark><mark style="color:yellow;">**Dockerhub**</mark><mark style="color:yellow;">:</mark>

* Hospedagem de Imagens Docker;
* Autenticação de usuário
* Automatização do processo de construção de imagens através de triggers (_**webhooks**_)
* Integração com o _**Github**_ e _**Bitbucket**_

***

### <mark style="color:yellow;">Criando uma conta no Dockerhub</mark>

1. Acesse o endereço [https://hub.docker.com](https://hub.docker.com) e clique em **Sign Up** para criar uma conta, preencha com os seus dados e clique em **Continue**&#x20;
2. Confirme o cadastro em seu e-mail e logue no **Docker Hub** através do link **Sign In**
3. Logue no terminal com o usuário criado através do comando `docker login` e digite sua senha

```bash
docker login -u <usuario_dockerhub>
```

***

4. Verifique se um arquivo de autorização foi criado

```bash
cat ~/.docker/config.json
```

***

5. Encerre o login na conta do Dockerhub no terminal

```bash
docker logout
```

***

### <mark style="color:yellow;">**Criando uma imagem de um container webserver**</mark>

```bash
mkdir -p debian-apache/site
```

```bash
wget http://site1368633667.hospedagemsites.ws/site1.zip
unip site1.zip
tar -czf site.tar ./
mv site.tar ../
```

```bash
vi Dockerfile
```

```docker
FROM    debian [imagem-usada]

RUN     apt-get update && apt-get install -y apache2 && apt-get clean

ENV APACHE_LOCK_DIR="var/lock"
ENV APACHE_PID_FILE="var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

ADD site.tar /var/www/html

LABEL description = "Apache webserver 1.0"

VOLUME /var/www/html

EXPOSE 80

ENTRYPONT ["/usr/sbin/apachectl"]

CMD ["-D", "FOREGROUND"]
```

```bash
docker image build -t imagem-do-container:tag caminho-da-imagem
```

```bash
docker run -dti -p 80:80 --name nome-do-container imagem-do-container
```

***

### _<mark style="color:yellow;">**Criando uma imagem multstage**</mark>_

```docker
FROM        golang as exec

COPY        app.go /go/src/app/

ENV        GO11MODULE=auto

WORKDIR     /go/src/app/

RUN         go build -o app.go .

FROM        alpine

WORKDIR     /appexec

COPY        ..from=exec /go/src/app /appexec

RUN chmod   -R 755 /appexec

ENTRYPOINT  ./app.go
```

```bash
docker image build -t nome-da-imagem:tag caminho-da-imagem
```

***

#### <mark style="color:yellow;">\[QUIS]</mark>

1. Para adicionar novas camadas a minha imagem, o que preciso modificar:

```bash
Dockerfile
```

2. Sobre camadas, arquivos que serão constantemente modificados ao criar uma imagem, em qual posição do nosso Dockerfile o acionamos?&#x20;

```
Na última linha possível
```

3. Qual parâmetro realiza um merge de todas as camadas da nossa imagem?

```bash
 export
```

***
