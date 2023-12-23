---
description: >-
  Toda a parte enterprise da certificação Docker DCA está relacionado a sua
  plataforma chamada MKE ou Mirantis Kubernetes Engine
---

# Enterprise

## Mirantis Kubernetes Engine

O **MKE** é uma plataforma que anteriormente era chamada de **Docker Enterprise** ou **UCP**. Ela fornece uma maneira de gerenciamento de clusters e deploy de aplicações através do uso de _Docker Swarm_ e _Kubernetes_.

O **MKE** é composto de um conjunto de diversas ferramentas como:

* **Universal Control Plane - UCP** - Interface de Gerenciamento Web
* **Calico** - Solução open source de redes e segurança de redes
* **Istio** - Ingress Gateway para o cluster
* **Mirantis Secure Registry** - Registry com features de segurança (anteriormente conhecido como **dtr**)

### Requisitos Mínimos

* 8GB of RAM para nodes Manager
* 4GB of RAM para nodes Workers
* 2 vCPUs para nodes manager
* 25GB de disco livre.

### Requisitos para Produção

* 16GB of RAM para nodes manager
* 4 vCPUs para nodes manager
* 25\~100GB de disco livre. Recomendado o uso de SSD.

> **ATENÇÃO**: Caso não tenha os requisitos mínimos para executar o **MKE** em sua máquina, não se preocupe, a prova é teórica e você precisa entender apenas o funcionamento e os conceitos.
