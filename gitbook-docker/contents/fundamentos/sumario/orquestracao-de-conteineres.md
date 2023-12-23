---
description: Comandos e funções utilizados na orquestração dos contêineres
---

# Orquestração de contêineres

### **Instalar explorador de imagem**

```bash
https://github.com/wagoodman/dive
wget https://github.com/wagoodman/dive/releases/download/v0.9.2/dive_0.9.2_linux_amd64.deb
sudo apt install ./dive_0.9.2_linux_amd64.deb
```

### _**Mostra processos sendo executado no container**_

```bash
docker container top nome-container
```

### **Exporta o container mergeando as camadas**

```bash
docker export hello > export.tar
```

### _**Importa o arquivo gerado, criando uma imagem do container a partir dela**_

```bash
cat export.tar | docker import - hello-export:latest
```

### **Exporta a imagem em um arquivo**

```bash
docker save luistkd4/hello:1.0 > save.tar
```

### _**Importa o arquivo gerado**_

```bash
docker load < save.tar
```

## Limites do docker

### CPU

Podemos limitar a utilização de recursos dos nossos containers. Para limitar a utilização de cpu precisamos usar o parâmetro `--cpus,`

Se temos somente 1 CPU, se quisermos que ele use somente metada disso devemos criar o container da seguinte forma:

```bash
docker run -d --cpus=".5" ubuntu
```

Abaixo temos um container de teste de strees onde `-c` é a quantidade de cores para estressar e `-t` o tempo do teste

```bash
docker run -d --rm progrium/stress -c 8 -t 30s
```

&#x20;Podemos utilizar o `docker stats` para monitorar em tempo real

```bash
docker stats
```

### Memory

Para limitar a utilização de memória precisamos usar o parâmetro `-m` ou `--memmry`

```bash
docker run --name nome-container -dti --memory 10m busybox sleep 3600
```

```bash
docker update container -m 128M --cpu 0.2
```

```bash
install stress
```

Abaixo temos um container de teste de strees onde -c é a quantidade de cores para estressar e -t o tempo do teste

```bash
docker run -d --memory 10m busybox sleep 3600
```

Abaixo temos um container de teste de strees onde `-c` é a quantidade de cores para estressar e `-t` o tempo do teste

```bash
docker run -d --rm progrium/stress --vm-bytes 60M -t 30s
```

Containers também possuem memória swap, para limitar:

```bash
docker run -d --memory-swap 150m
```

{% hint style="warning" %}
**Importante:** Sempre limite a quantidade de memória em seu container, caso sua aplicação consuma muita memória...o Kernel do Linux irá finalizar aplicações para sobrar memória para o sistema
{% endhint %}

```bash
WARNING:Your kernel does not support swap limit capabilities. Limitation discarded.
```

1. Logue como root na máquina&#x20;
2. Edite `/etc/default/grub` adicione alinha abaixo sem a hashtag(#)

```bash
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
```

3. Execute: `update-grup`
4. Reinicie a máquina&#x20;

{% embed url="https://docs.docker.com/engine/install/linux-postinstall/" %}

1. Qual desses comandos listam os containers que estão rodando

```bash
docker ps
```

2. Quais destes parâmetros possibilita o acesso a um container:&#x20;

```bash
exec
```

3. &#x20;Qual recurso é importante ser limitado:

```
Memória
```
