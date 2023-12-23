---
description: >-
  Por padrão, tanto o Docker no modo standalone quanto o Docker Swarm, partilham
  apenas de um driver local para uso de volumes.
---

# Volumes

> Isso quer dizer que o Docker Swarm não possui, até então, solução nativa para distribuir volumes entre os nós.

Então, ao definirmos o volume para cada serviço, criamos um volume local dentro de cada nó que for executar a tarefa. Logo, os volumes não são compartilhados entre os diferentes nós do cluster.

Existem soluções que não são nativas do Docker Swarm para utilizar volumes distribuídos entre nós, que podem ser consultadas na [Docker Store](https://store.docker.com/search?category=volume\&q=\&type=plugin).

Para esta etapa, precisamos que o plugin `trajano/nfs-volume` esteja instalado em todas as máquinas que fazem parte do swarm, caso não esteja instalado, verifique a etapa `volumes` na seção `NFS Volume`

Verifique se o plugin esta instalado e se o `exports` é reconhecido em todas as máquinas

```bash
docker plugin ls
showmount -e master.docker-dca.example 
```

Na máquina master, crie o volume

```bash
docker volume create -d trajano/nfs-volume-plugin \
--opt device=master.docker-dca.example:/home/vagrant/storage \
--opt  nfsopts=hard,proto=tcp,nfsvers=3,intr,nolock volume_nfs

docker volume inspect volume_nfs | jq
```

Crie um serviço do nginx com 3 replicas apontando para o volume

```bash
docker service create --name webserver \
--replicas 3 \
--publish 80:80 \
--network dca-overlay \
--mount source=volume_nfs,target=/usr/share/nginx/html/ \
registry.docker-dca.example:5000/nginx

docker service ps webserver
```

Verifique o conteúdo do nginx

```bash
curl master.docker-dca.example
curl node01.docker-dca.example
curl node02.docker-dca.example
```

Altere o conteúdo da pagina e verifique o conteúdo do nginx

```bash
echo "<marquee> VOLUME NFS DOCKER SWARM</marquee>" | tee -a /home/vagrant/storage/index.html
curl master.docker-dca.example
curl node01.docker-dca.example
curl node02.docker-dca.example
```

Remova o serviço

```bash
docker service rm webserver
```

