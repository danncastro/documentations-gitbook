---
description: >-
  Docker Machine é uma ferramenta que possibilita a instalação do Docker Engine
  em hosts virtuais e a administração através do comando docker-machine
---

# Docker Machine



{% embed url="https://github.com/docker/machine" %}
Docker machine
{% endembed %}

O Docker Machine pode ser utilizado localmente através do Virtualbox/HyperV ou até mesmo nas clouds.

### Instalando o Docker Machine

Instalaremos o `docker-machine` em nossa máquina host que esta rodando Linux.

> Caso você deseje instalar o `docker-machine` em macOS ou Windows, verifique a [Documentação Oficial](https://docs.docker.com/machine/install-machine/)

Execute os comandos:

```bash
base=https://github.com/docker/machine/releases/download/v0.16.0 
curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine 
sudo mv /tmp/docker-machine /usr/local/bin/docker-machine 
chmod +x /usr/local/bin/docker-machine
```

Para verificar se a instalação foi feita com sucesso execute o comando:

```bash
docker-machine --version
```

Será exibida uma mensagem com a versão do `docker-machine`

### Provisionando docker-machines em uma máquina virtual local

Primeiramente iremos aprender como provisionar uma Docker Machine localmente utilizando o virtualbox. Vamos visualizar se existe alguma docker-machine criada no atual momento através do comando:

```bash
docker-machine ls
```

Para criar uma docker machine podemos utilizar o subcomando `create` informando qual driver queremos utilizar.

```bash
docker-machine create --driver virtualbox default
```

> Note que o ultimo argumento é o nome da `docker-machine` que será criada

O comando acima faz o download de uma distribuição linux muito leve chamada [boot2docker](https://github.com/boot2docker/boot2docker) que será responsável por executar o Docker Engine.

### Interagindo com o Docker Machine

Vamos listar novamente as máquinas criadas até o momento:

```bash
docker-machine ls
```

```bash
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12   
```

Agora que temos nossa Docker Machine criada, precisamos configurar nossas variáveis de ambiente para que seja possível conectar e interagir com o Docker Engine.

Fazemos isto através do comando `docker-machine env <machine>`

```bash
docker-machine env default
```

Será exibida uma mensagem com as variáveis de ambiente que precisamos configurar.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/danncastro/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell: 
# eval $(docker-machine env default)
```

Podemos também executar o comando `eval` para configurar automaticamente as variaveis de ambiente.

```bash
eval $(docker-machine env default)
```

> O comando `eval` funciona apenas em shell unix (com exeção do `fish`) e serve para construir um comando através de concatenação de argumentos, no nosso caso ele irá executar todos os comandos exibidos pelo comando `docker-machine env default`.

Podemos executar agora algum container normalmente como se estivessemos utilizando nosso docker local, uma vez que temos as variaveis apontando para nosso docker-machine.

```bash
docker container run -dit --name webserver -p 80:80 danncastro/webserver:apache2
docker container ls
```

A aplicação não ficará disponível através do nosso localhost, uma vez que não esta sendo executada localmente

```bash
curl localhost
curl: (7) Failed to connect to localhost port 80: Connection refused
```

Ao invés disso precisamos coletar o IP da Docker Machine para acessar a aplicação para isto utilizamos o seguinte comando `docker-machine ip <machine>`:

```bash
docker-machine ip default
```

Agora podemos acessar o serviço pelo navegador através do endereço IP correto ou através do terminal com um subshell para facilitar

```bash
curl $(docker-machine ip default)
```

Podemos criar quantas docker-machine quisermos através do comando `docker-machine create <machine>`

```bash
docker-machine create docker02
docker-machine ls
```

```bash
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
default    *        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12   
docker02   -        virtualbox   Running   tcp://192.168.99.102:2376           v19.03.12   
```

Para desconfigurar as variaveis de ambientes do docker, podemos utilizar o comando `docker-machine env -u` , e será exibido os comandos para remover as configurações de conexão, podemos também utilizar o eval neste caso

```bash
docker-machine env -u
eval $(docker-machine env -u)
```

Podemos também parar e iniciar docker-machines através dos subcomandos `stop` e `start`

```bash
docker-machine stop default
docker-machine ls
docker-machine start default
```

Para inspecionar uma Docker Machine, utilizamos o subcomando `inspect`

```bash
docker-machine inspect default 
```

> Podemos também utilizar o `| jq` para facilitar a leitura

Para remover a Docker machine, utilizamos o subcomando `rm` seguido da confirmação `y`

```bash
docker-machine rm default
docker-machine rm docker02 -y
docker-machine ls
```

### Provisionando Docker Machines em Cloud

Quando instalamos o Docker Machine, temos acesso aos Cloud Providers:

* Amazon Web Services
* DigitalOcean
* Microsoft Azure
* Google Compute Engine
* etc...

> Para uma lista completa de drivers disponíveis veja a [Documentação Oficial](https://docs.docker.com/machine/drivers/)

Vamos criar uma Docker Machine na AWS, para isto precisamos que as credenciais da AWS já estejam configuradas em nosso ambiente, podemos também passar esses dados através da cli com os parametros:

```bash
docker-machine create --driver amazonec2 --amazonec2-access-key AKI******* --amazonec2-secret-key 8T93C*******  aws-sandbox
```

> Lembre-se de alterar os dados pelos seus dados de acesso.

```bash
docker-machine create --driver amazonec2 aws-sandbox
docker-machine ls
```

Vamos executar um container na cloud e verificar seu funcionamento

```bash
eval $(docker-machine env aws-sandbox)
docker container run -dit --name nginx -p 80:80 nginx
```

Ainda não será possível acessar a aplicação, uma vez que a amazon terá um `security-group` que não esta liberando o acesso a porta `80`.

```bash
curl $(docker-machine ip aws-sandbox)
```

Para isto, precisamos alterar as regras de ingress do `security-group`, podemos alterar através da web-ui ou através da `aws-cli`

```bash
aws ec2 describe-security-groups --group-names docker-machine
aws ec2 authorize-security-group-ingress --group-name docker-machine --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 describe-security-groups --group-names docker-machine
```

Agora que temos nosso `security-group` habilitando o acesso a porta 80, podemos acessar a aplicação:

```bash
curl $(docker-machine ip aws-sandbox)
```

Vamos remover nossa Docker Machine

```bash
docker-machine rm aws-sandbox
```

> Não esqueça de verificar no painel da aws ou na cli se a máquina foi removida para evitar custos inesperados
