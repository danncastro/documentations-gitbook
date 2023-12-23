---
description: >-
  Vamos explorar alguns outros cenários, com fluxo condicionais, repetições e
  excepcionais.
---

# Cases

**Case 1**: Vamos imaginar que em um processo seletivo, existe o valor base salarial de R$ 2.000,00 e o salário pretendido pelo candidato. Vamos elaborar um controle de fluxo onde:

* Se o valor salário base, for maior que valor salário pretendido, imprima : **LIGAR PARA O CANDIDATO**;
* Senão, Se o valor salário base for igual ao valor salário pretendido, imprima : **LIGAR PARA O CANDIDATO, COM CONTRA PROPOSTA**;
* Senão imprima: **AGUARDANDO RESULTADO DOS DEMAIS CANDIDATOS.**

**Case 2**: Foi solicitado, que nosso sistema garanta que, diante das inúmeras candidaturas sejam selecionados apenas no máximo, 5 candidatos para entrevista, onde o salário pretendido seja menor ou igual ao salário base.

```java
// Array com a lista de candidatos

String [] candidatos = {"FELIPE","MÁRCIA","JULIA","PAULO","AUGUSTO","MÔNICA","FABRÍCIO","MIRELA","DANIELA","JORGE"};
```

```java
// Método que simula o valor pretendido

import java.util.concurrent.ThreadLocalRandom;
static double valorPretendido() {
     return ThreadLocalRandom.current().nextDouble(1800, 2200);
}
```

**Case 3**: Agora é hora de imprimir a lista dos candidatos selecionados, para disponibilizar para o RH entrar em contato.

**Case 4**: O RH deverá realizar uma ligação, com no máximo 03 tentativas para cada candidato selecionado e caso o candidato atenda, deve-se imprimir:

* **"CONSEGUIMOS CONTATO COM \_`[CANDIDATO]`` `**_**` ``` APÓS **_** `\[TENTATIVA]\`**_\*\* TENTATIVA(S)" ;\*\*
* Do contrário imprima: **"NÃO CONSEGUIMOS CONTATO COM O \_`[CANDIDATO]`**\_**".**

{% tabs %}
{% tab title="Soluções" %}
Hora de aplicar os conceitos apresentados, implementando as soluções, para cada case apresentado.
{% endtab %}

{% tab title="Case 1" %}
```java
public class ProcessoSeletivo {
	public static void main(String[] args) {
		//salario base maior que salario pretendido
		case1(2000.0, 1900.0);
		
		//salario base igual que salario pretendido
		case1(2000.0, 2000.0);
		
		//salario base igual que salario pretendido
		case1(1900.0, 2000.0);V
	}
	static void case1(double salarioBase, double salarioPretendido) {
		
		if(salarioBase > salarioPretendido) {
			System.out.println("LIGAR PARA O CANDIDATO");
		}
		else if(salarioBase == salarioPretendido) {
			System.out.println("LIGAR PARA O CANDIDATO COM CONTRA PROPOSTA");
		}
		else {
			System.out.println("AGUARDANDO RESULTADO DOS DEMAIS CANDIDATOS");
		}
	}
}

```
{% endtab %}

{% tab title="Case 2" %}
```java
import java.util.concurrent.ThreadLocalRandom;

public class ProcessoSeletivo {
	public static void main(String[] args) {
		case2();
		
	}
	static void case2() {
		double salarioBase = 2000.0;
		String [] candidatos = {"FELIPE","MÁRCIA","JULIA","PAULO","AUGUSTO","MÔNICA","FABRÍCIO","MIRELA","DANIELA","JORGE"};
		int totalSelecionados = 0;
		int proximoCandidato = 0; 
		while(totalSelecionados <5 && proximoCandidato < candidatos.length) {
			String candidato = candidatos[proximoCandidato++];
			double valorPretendido = valorPretendido();
			System.out.println("O candidato " + candidato + " está pedindo " + valorPretendido);
			if(valorPretendido > salarioBase) {
				System.out.println("QUE PENA!! O candidato " + candidato + " NÃO foi selecionado ");
				
			}else {
				System.out.println("LEGAL!! O candidato " + candidato + " foi selecionado ");
				totalSelecionados++;
			}
			
		}
		System.out.println("Total de selecionados: " + totalSelecionados);
		System.out.println("Total de consultados: " + proximoCandidato);
	}
	static double valorPretendido() {
	     return ThreadLocalRandom.current().nextDouble(1800, 2200);
	}
	
	
}

```
{% endtab %}

{% tab title="Case 3" %}
```java
public class ProcessoSeletivo {
	public static void main(String[] args) {
		case3();
		
	}
	static void case3() {
		String [] candidatosSelecionados = {"FELIPE","MÁRCIA","JULIA","PAULO","AUGUSTO"};
		
		//forma indexada
		//quando preciso do indice para complementar a lógica
		System.out.println("Imprimindo com a ordem de seleção pelo índice");
		for(int x=0; x<candidatosSelecionados.length; x++) {
			System.out.println((x+1)+ "° Candidato é " + candidatosSelecionados [x] );
		}
		
		//forma abrevida
		//interação total sem precisar da posição ou indice
		System.out.println("Imprimindo todos sem a necessidade de exibir o índice");
		
		for(String candidato: candidatosSelecionados) {
			System.out.println(candidato);
		}
		
	}
}

```
{% endtab %}

{% tab title="Case 4" %}
```java
import java.util.Random;

public class ProcessoSeletivo {
	public static void main(String[] args) {
		String [] candidatosSelecionados = {"FELIPE","MÁRCIA","JULIA","PAULO","AUGUSTO"};
		//primeiro um for para selecionar os candidatos
		for(String candidato: candidatosSelecionados) {
			case4(candidato);
		}
		
	}
	static void case4(String candidato) {
		
		int tentativasRealizadas = 1;
		boolean continuarTentando = true;
		boolean atendeu=false;
		do {
			atendeu= atender();
			continuarTentando = !atendeu;
			if(continuarTentando)
				tentativasRealizadas++;
			else
				System.out.println("CONTATO REALIZADO COM SUCESSO");
			
		}while(continuarTentando && tentativasRealizadas<3);
		
		if(atendeu)
			System.out.println("CONSEGUIMOS CONTATO COM " + candidato +" NA " + tentativasRealizadas + " TENTATIVA");
		else
			System.out.println("NÃO CONSEGUIMOS CONTATO COM " + candidato +", NÚMERO MAXIMO TENTATIVAS " + tentativasRealizadas + " REALIZADA");
		
		
	}
	
	//método auxiliar
	static boolean atender() {
		return new Random().nextInt(3)==1;	
	}
}

```
{% endtab %}
{% endtabs %}
