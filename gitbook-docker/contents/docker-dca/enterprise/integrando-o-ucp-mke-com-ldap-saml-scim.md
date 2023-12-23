---
description: Para integrar o UCP ou o MKE com algum protocolo de Autenticação e Autorização
---

# Integrando o UCP/MKE com LDAP/SAML/SCIM

&#x20;Podemos acessar o dashboard em [https://master.docker-dca.example](https://master.docker-dca.example) e navegar até o menu `admin`, `Admin Settings`, `Authentication & Authorization`.

Para habilitar o LDAP basta ativar a opção `LDAP` e preencher os dados da conexão.

> LDAP (Lightweight Directory Access Protocol) é um protocolo de aplicação aberto e livre para diretório de usuários, bastante conhecido em sua implementação como Microsoft Active Directory ou OpenLDAP

Para habilitar o SAML basta ativar a opção `SAML` e preencher os dados da conexão.

> SAML (Security Assertion Markup Language) é um padrão aberto para troca de dados de autenticação e autorização entre partes. Muitas aplicações aceitam a autenticação via SAML, muita das vezes atrelado ao SSO (Single Sign On)

Para habilitar o SCIM basta ativar a opção `SCIM` e preencher os dados da conexão.

> SCIM (System for Cross-domain Identity Management) é um padrão para automatizar a troca de informações de identidade do usuário entre domínios de identidade ou sistemas de TI. De todos os protocolos comentados o SCIM é o menos utilizado, porém não menos importante.
