---
description: Vamos criar agora uma rede overlay para suportar um serviço do nginx
---

# Network

```bash
docker network create -d overlay dca-overlay
docker network inspect dca-overlay
docker service create --name webserver --publish target=80,published=80 --network dca-overlay registry.docker-dca.example:5000/nginx
docker node ps
```

Verifique na máquina `node01` que não existe a rede `dca-overlay` que criamos

```bash
vagrant ssh node01
docker network ls
```

Na máquina `master` vamos efetuar o scaling para 3 replicas do webserver

```bash
docker service scale webserver=3
docker service ps webserver
```

Na máquina `node01` verifique que agora que o serviço está em execução, a rede foi criada com o mesmo ID da máquina `master`

```bash
docker network ls
```

Acesse os endereços dos servidores pelo navegador e veja a página do nginx sendo executada

{% embed url="http://master.docker-dca.example/" %}

{% embed url="http://node01.docker-dca.example/" %}

{% embed url="http://node02.docker-dca.example/" %}

Remova o serviço

```bash
docker service rm webserver
```

Como definir o modo que o serviço será criado utilizando a flag `--mode` no momento da criação do serviço

* O driver bridge permite a comunicação entre diferentes containers em um mesmo host, então se eu tenho 10 container em uma máquina só, todos eles conseguem se comunicar por causa desse driver bridge aqui no fim das contas.
* O drive host, ele serve para fazer a comunicação entre container e máquina, então a máquina que está carregando esse container consegue se comunicar com o IP desse container por causa desse driver host.

Por mais que o driver `overlay` seja responsável por comunicar múltiplos hosts em uma mesma rede, também podemos conectar containers em escopo local criados com o comando docker container run em redes criadas com esse driver.

Para isso, basta no momento da criação da rede utilizarmos a flag `--attachable`

```bash
docker network create -d overlay --attachable my_overlay
```

Com o comando acima, conseguiremos conectar tanto serviços como containers `"standalone`" em nossa rede

* A rede Ingress é a padrão criada junto com nosso swarm
* O driver overlay é utilizado para a comunicação entre nós em diferentes hosts

Como criar nossas próprias redes com o driver overlay&#x20;

* Utilizando o comando `docker network create -d overlay`
* Redes overlays criadas manualmente (`User-Defined Overlay`) permitem a comunicação entre serviços por seus nomes (`Service Discovery`)
* As redes criadas com o driver overlay são listadas de maneira lazy para workers
* O download do arquivo utilizado pode ser feito [aqui](https://caelum-online-public.s3.amazonaws.com/1486-docker-swarm-cluster-container/07/docker-compose.yml).

### \[QUIS]

Conhecemos agora a rede Ingress e vimos que, além de ser a rede padrão criada pelo Docker Swarm para os nós que fazem parte do cluster, ela utiliza o driver overlay. Qual das alternativas abaixo contém uma finalidade desse driver?

1. O driver overlay comunica seus dados de maneira criptografada para garantir nossa segurança. Executando o comando docker node inspect podemos ver os certificados de segurança.
2. O driver overlay permite a comunicação entre diferentes hosts de um mesmo cluster. Além de garantir a segurança, o driver overlay também permite a comunicação entre diferentes hosts rodando Docker.

A rede Ingress, utilizando o driver overlay, consegue fazer diversas coisas por nós, como garantir por exemplo, a comunicação entre diferentes nós de maneira altamente segura. Porém, ela também possui certas limitações. Qual das alternativas abaixo contém uma dessas limitações? Serviços conseguem se comunicar apenas via endereço IP.

1. Temos que saber o endereço IP do serviço que queremos nos comunicar utilizando a rede Ingress.

Aprendemos alguns fatos novos sobre `User-Defined Overlay networks`. Quais das alternativas abaixo contém informações verdadeiras sobre redes criadas manualmente?

1. User-Defined Overlay além de permitir a comunicação via nome do serviço, também permitem via IP.
2. User-Defined Overlay são criadas de maneira lazy para workers. Essas redes só serão reconhecidas por workers que rodarem tarefas que utilizem a rede.
3. Serviços que utilizam redes customizadas conseguem descobrir outros serviços diretamente por nome. Podemos utilizar o conceito de Service Discovery com User-Defined Overlay.
