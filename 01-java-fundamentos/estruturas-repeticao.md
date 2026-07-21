# Estruturas De Repetição

## O que é?

As estruturas de repetição (também chamadas de laços ou loops) servem para executar o mesmo bloco de código várias vezes até que uma condição seja atingida.

---

## Tipos

Laço for: Ideal para quando você já sabe exatamente quantas vezes o código deve se repetir. Ele agrupa a inicialização, a condição e o incremento/decremento na mesma linha.

Laço while: Ideal para quando você não sabe quantas vezes o código vai rodar. Ele testa a condição antes de executar o bloco. Se a condição for falsa logo de início, o código nunca roda.

Laço do-while: Garante que o código seja executado pelo menos uma vez, porque ele roda o bloco primeiro e só testa a condição no final.

---

## Estrutura

```java
public class Main {

    public static void main(String[] args) {
        
        //for
        // Contagem regressiva de 5 até 1 usando o decremento
        for (int i = 5; i > 0; i--) {
            System.out.println("Número: " + i);
        }
        
        //while
        int contador = 3;
        while (contador > 0) {
            System.out.println("Contando... " + contador);
            contador--; // Importante para não criar um loop infinito
        }
        
        //do-while
        int tentativas = 0;
        do {
            System.out.println("Tentativa de conexão...");
            tentativas++;
        } while (tentativas < 1);
    }

}
```