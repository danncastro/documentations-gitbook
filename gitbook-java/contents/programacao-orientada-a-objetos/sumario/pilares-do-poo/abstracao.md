---
description: Para você ser, é preciso você fazer.
---

# Abstração

Sabemos que qualquer sistema de mensagens instantâneas realiza as mesmas operações de Enviar e Receber Mensagem, dentre outras operações comuns ou exclusivas de cada aplicativo disponível no mercado.

Mas será que as ações realizadas, contém o mesmo comportamento ? Acreditamos que não.

{% hint style="info" %}
Já imaginou a **Microsoft** falar para o **Facebook**: _**"Ei, toma meu código do MSN!"**_.
{% endhint %}

O que vale destacar para compreender, é que todo e qualquer sistema de mensagem precisa sim, no mínimo Enviar e Receber Mensagem, logo, consideramos se firmar um "contrato" para qualquer um que queira se apresentar assim para o mercado.

Observem a nova estruturação dos códigos abaixo, com base na implementação apresentada no pilar [Herança](broken-reference).

{% tabs %}
{% tab title="ServicoPai" %}
```java
public abstract class ServicoMensagemInstantanea {
	public abstract void enviarMensagem();
	public abstract void receberMensagem();	
}
```
{% endtab %}

{% tab title="MSN" %}
```java
public class MSNMessenger extends ServicoMensagemInstantanea{
	public void enviarMensagem() {
		System.out.println("Enviando mensagem pelo MSN Messenger");
	}
	public void receberMensagem() {
		System.out.println("Recebendo mensagem pelo MSN Messenger");
	}
}
```
{% endtab %}

{% tab title="Facebook" %}
```java
public class FacebookMessenger extends ServicoMensagemInstantanea {
	public void enviarMensagem() {
		System.out.println("Enviando mensagem pelo Facebook Messenger");
	}
	public void receberMensagem() {
		System.out.println("Recebendo mensagem pelo Facebook Messenger");
	}
}
```
{% endtab %}

{% tab title="Telegram" %}
```java
public class Telegram extends ServicoMensagemInstantanea {
	public void enviarMensagem() {
		System.out.println("Enviando mensagem pelo Telegram");
	}
	public void receberMensagem() {
		System.out.println("Recebendo mensagem pelo Telegram");
	}
}

```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Em Java, o conceito de abstração é representado pela palavra reservada **abstract** e métodos que **NÃO** possuem corpo na classe abstrata (pai).
{% endhint %}

{% hint style="success" %}
É muito difícil falar de `abstração` e **NÃO** mencionar `polimorfismo`.
{% endhint %}
