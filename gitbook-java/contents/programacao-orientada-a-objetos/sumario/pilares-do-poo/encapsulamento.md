---
description: Nem tudo precisa ser estar disponível para todos!
---

# Encapsulamento

Já imaginou, você instalar o MSN Messenger e ao querer enviar uma mensagem, fosse solicitado a você, verificar se o computador está conectado a internet e depois, pedir para você salvar a mensagem no histórico? ou, se ao tentar enviar um SMS pelo celular, primeiro você precisasse consultar manualmente o seu saldo ?

Acredito que não seria uma experiência tão agradável de ser executada, recorrentemente, por nós usuários.

Mesmo ainda sendo necessária algumas etapas, nos processos citados, não é um requisito uma visibilidade pública, isso quer dizer, além da própria classe que possui a responsabilidade de uma determinada ação.

Quanto ao MSN Messenger, para nós, só é relevante saber que podemos e como devemos enviar e receber a mensagem, logo, as demais funcionalidades poderão ser consideradas privadas (private). E é ai que se caracteriza a necessidade do pilar de Encapsulamento. O que esconder ?

Vejamos a refatoração abaixo, da nossa classe MSN Messenger:

os demais, considerando os princípios de POO.

{% tabs %}
{% tab title="UML" %}
<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Antes" %}
```java
/*
 * Simulação do uso da classe MSNMessenger
 */
public class ComputadorPedrinho {
	public static void main(String[] args) {
		//abrindo MSN Messenger
		MSNMessenger msn = new MSNMessenger();
		
		msn.validarConectadoInternet();
		msn.enviarMensagem();
		msn.salvarHistoricoMensagem();
		
		msn.receberMensagem();
	}
}
```
{% endtab %}

{% tab title="MSNMessenger.java" %}
```java
public class MSNMessenger {
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

{% tab title="Depois" %}
```java
/*
 * Simulação do uso da classe MSNMessenger
 */
public class ComputadorPedrinho {
	public static void main(String[] args) {
		//abrindo MSN Messenger
		MSNMessenger msn = new MSNMessenger();
		
		msn.enviarMensagem();
		
		msn.receberMensagem();
	}
}
```
{% endtab %}
{% endtabs %}
