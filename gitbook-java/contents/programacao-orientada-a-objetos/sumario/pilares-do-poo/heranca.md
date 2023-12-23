---
description: Nem tudo se copia, às vezes se herda.
---

# Herança

Já imaginou, você ter sido classificado para a vaga de emprego de seus sonhos e como desafio, seria justamente você criar um diagrama de classes e em seguida os respectivos arquivos .java, que apresentasse os fundamentos de POO, com base no contexto de vários aplicativos de mensagens instantâneas?&#x20;

&#x20;

<figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
Com base na nossa classe **MsnMessenger**, você já poderia dar os primeiros passos para se dar bem no processo seletivo, simplesmente, copiar e colar a estrutura, para as novas classes **FacebookMessenger, Telegram** e **BINGO!!!**
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Agora é escrever o código das classes acima e esperar pela contratação !!!

{% hint style="danger" %}
Lamentamos dizer, mas esta não seria a melhor alternativa para a proposta citada acima.
{% endhint %}

Além de uma compreensão do desafio, é necessário que, tenhamos domínio dos pilares de POO e aplicá-los em situações iguais a esta.

**NOTE:** Todas as três classes, possuem a mesma estrutura comportamental e diante deste contexto, se encaixa perfeitamente o segundo pilar da POO, a Herança.

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>Representação UML do sistema de mensagens instantâneas</p></figcaption></figure>

{% tabs %}
{% tab title="ServicoPai" %}
```java
//a classe MSNMessenger é ou representa
public class ServicoMensagemInstantanea {
	public void enviarMensagem() {
		//primeiro confirmar se esta conectado a internet
		validarConectadoInternet();
		System.out.println("Enviando mensagem");
		//depois de enviada, salva o histórico da mensagem
		salvarHistoricoMensagem();
	}
	public void receberMensagem() {
		System.out.println("Recebendo mensagem");
	}
	
	//métodos privadas, visíveis somente na classe
	private void validarConectadoInternet() {
		System.out.println("Validando se está conectado a internet");
	}
	private void salvarHistoricoMensagem() {
		System.out.println("Salvando o histórico da mensagem");
	}
}
```
{% endtab %}

{% tab title="MSN" %}
```java
public class MSNMessenger extends ServicoMensagemInstantanea{

}
```
{% endtab %}

{% tab title="Facebook" %}
```java
public class FacebookMessenger extends ServicoMensagemInstantanea {

}
```
{% endtab %}

{% tab title="Telegram" %}
```java
public class Telegram extends ServicoMensagemInstantanea {

}
```
{% endtab %}

{% tab title="ComputadorPedrinho" %}
```java
public class ComputadorPedrinho {
	public static void main(String[] args) {
		
		MSNMessenger msn = new MSNMessenger();
		msn.enviarMensagem();
		msn.receberMensagem();
		
		FacebookMessenger fbm = new FacebookMessenger();
		fbm.enviarMensagem();
		fbm.receberMensagem();
		
		Telegram tlg = new Telegram();
		tlg.enviarMensagem();
		tlg.receberMensagem();
		
	}
}
```
{% endtab %}
{% endtabs %}

Podemos avaliar a importância de compreender os pilares de POO, para ter uma melhor implementação, reaproveitamento e reutilização de código, em qualquer contexto das nossas aplicações, mas não para por ai.

{% hint style="warning" %}
Será que todos os sistemas de mensagens, realizam as suas operações de uma mesma maneira? e agora ? este é um trabalho para os pilares **Abstração** e **Polimorfismo**.
{% endhint %}
