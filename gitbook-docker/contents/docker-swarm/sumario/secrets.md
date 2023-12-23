# Secrets

{% hint style="danger" %}
Em termos de Serviços Swarm, um _`secret`_ é um `blob data` como senha, chave privada ssh, certificado SSL ou qualquer outro dado que **NÃO** deve ser transmitido pela rede ou armazenado sem criptografia no código fonte da aplicação.
{% endhint %}

> Um blob (do inglês: Binary Large OBject, basic large object, BLOB ou Blob, que significa objeto grande binário ou objeto grande básico na tradução literal), é uma coleção de dados binários armazenados como uma única entidade.

Para gerenciar os secrets no swarm, utilizamos o comando `docker secret`

```bash
docker secret --help
docker secret ls
```

Para criar um secret precisamos passa-lo através do _STDIN_, ou através de um arquivo.

```bash
echo "caiodelgadonew123" | docker secret create senha_db - 
docker secret inspect --pretty senha_db
```

> O secret criado é armazenado no arquivo `/run/secrets/<secret_name>` no container em execução

Vamos executar um container `mysql` passando a senha como um secret

```bash
docker service create --name mysql_database \
--publish 3306:3306/tcp \
--secret senha_db \
-e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/senha_db \
registry.docker-dca.example:5000/mysql:5.7
```

Vamos instalar o client do `mariadb`, verificar em qual servidor está sendo executada a task com o container e vamos conectar passando a senha que configuramos no arquivo.

```bash
sudo apt-get install mariadb-client -y
docker service ps mysql_database
mysql -h node01.docker-dca.example -u root -pcaiodelgadonew123

> CREATE DATABASE caiodelgadonew;
> SHOW DATABASES:
> EXIT
```

Destrua o serviço

```bash
docker service rm mysql_database
```
