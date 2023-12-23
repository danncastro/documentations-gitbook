# Projeto Docker pets

{% embed url="https://github.com/mark-church/docker-pets" %}
Projeto docker-pets
{% endembed %}

### Ingresse o servidor secundário ao cluster Swarm

```bash
docker swarm join --token SWMTKN-1-2xs8wa75la9ugfaiby0bjtpj71uqi48idxlykvjyft6g45lypo-30uglrk1mhg1yplvptarhp7cv 192.168.0.28:2377
```

### **Lista os nodes para verificar se estão funcionais**

```bash
docker node ls
```

### _**Criamos a rede overlay**_

```bash
docker network create -d overlay petsOverlay
```

### Criamos nosso backend

```bash
docker service create --network petsOverlay --name db consul
```

### Criamos nosso frontend

```bash
docker service create --network petsOverlay -p 8000:5000 -e 'DB=db' --name web chrch/docker-pets:1.0
```

### Listamos nossos serviços

```bash
docker service ls
```

### Escalar a aplicação

```bash
docker service scale web=3
```
