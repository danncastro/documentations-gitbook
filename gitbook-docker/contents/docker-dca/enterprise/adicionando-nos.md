---
description: >-
  O primeiro passo é adicionar os nós restantes ao nosso cluster, o dashboard já
  nos entrega o comando completo para execução, basta clicar em Add Nodes na
  parte inferior do dashboard
---

# Adicionando Nós

Será exibida uma tela com todas as informações necessárias para adicionar um nó ao nosso cluster, deixaremos as opções de **node type** `Linux` e **node role** `Workers` vamos copiar o comando e executa-lo em todos os outros nós.

```bash
vagrant ssh node01
docker swarm join --token SWMTKN-1-40xlaju97ux404y2z8p5tixyy5q5ag0064uo367og5i98nzuwt-7kvbotg83csbjskcxzbfsjgnp 10.20.20.100:2377
exit

vagrant ssh node02
docker swarm join --token SWMTKN-1-40xlaju97ux404y2z8p5tixyy5q5ag0064uo367og5i98nzuwt-7kvbotg83csbjskcxzbfsjgnp 10.20.20.100:2377
exit

vagrant ssh registry
docker swarm join --token SWMTKN-1-40xlaju97ux404y2z8p5tixyy5q5ag0064uo367og5i98nzuwt-7kvbotg83csbjskcxzbfsjgnp 10.20.20.100:2377
exit
```

Voltando para a página do **UCP** vamos em **Shared Resources** e em seguida em **Nodes**

Vamos aguardar o processo até que os nodes fiquem em estado de `Healthy`

Voltando ao dashboard conseguimos visualizar as métricas dos nodes managers e Workers.

## Mirantis Secure Registry

Agora que temos os nós adicionados em nosso cluster, podemos definir o nó que será nosso **DTR** (Mirantis Secure Registry era anteriormente chamado de **DTR** Docker Trusted Registry)

Para adicionarmos o registry vamos no menu **Admin**>**Admin Settings**>**Mirantis Secure Registry**

E vamos selecionar o `UCP NODE` `registry.docker-dca.example` e marcar `Use a PEM-encoded TLS CA certificate for MKE` após isto podemos copiar o comando para `Unix Shell` e executar na máquina `registry`

```bash
vagrant ssh registry

docker run -it --rm docker/dtr install --ucp-node registry.docker-dca.example --ucp-username admin --ucp-url https://master.docker-dca.example --ucp-ca  "-----BEGIN CERTIFICATE-----
MIIBfjCCASSgAwIBAgIUaRHfl3/fvCS+PhFfq/gQiEpw/8QwCgYIKoZIzj0EAwIw
HTEbMBkGA1UEAxMSVUNQIENsaWVudCBSb290IENBMB4XDTIxMTAyMzE1NDEwMFoX
DTI2MTAyMjE1NDEwMFowHTEbMBkGA1UEAxMSVUNQIENsaWVudCBSb290IENBMFkw
EwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEa4NQLljC7h3eWFdLxljHpCvXBvoEgpvk
DNdTzpnFYrpUHgdLbCCRLZCnemoWDTbAUnj1D0rN7ZnalxF8FLe5cqNCMEAwDgYD
VR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFKC1J6c292rz
j16c85hbCTt8klgFMAoGCCqGSM49BAMCA0gAMEUCIQD4VcngZ/ZHR98nXTX2u5Ef
1P0J6zKxElbGuQhHkuToDwIgSrX4eftF4SX1byWbpwMi7Q5mpNdOUW686FweqxAq
xdw=
-----END CERTIFICATE-----
"

ucp-password: dannielcastro@docker.exemple
```

A instalação será concluída quando a mensagem a seguir for exibida:

```bash
INFO[0156] Installation is complete
```

Acesse o **DTR** através do endereço https://registry.docker-dca.example/ e efetue o login com o usuário e senha de administrador

Após o login clique em `Skip for now`
