# Operadores Condicionais

## O que é?

As estruturas condicionais em Java controlam o fluxo do programa, executando blocos de código específicos com base no resultado de uma expressão booleana (true ou false).

---

## Tipos

Estrutura if / else if / else: É a estrutura mais comum, ideal para testar faixas de valores ou múltiplas condições complexas.
- if: Executa o bloco se a condição for verdadeira.
- else if: Testa uma nova condição caso a anterior seja falsa.
- else: Executa um código padrão se todas as condições anteriores falharem.

Estrutura switch: Ideal para testar uma única variável contra uma lista de valores fixos (valores exatos). É mais limpa que múltiplos if / else.
- case: Define o valor a ser testado.
- break: Interrompe a execução para não rodar os casos seguintes.
- default: Código executado se nenhum caso for correspondido.


Operador Ternário: Uma forma compacta de escrever um if / else simples em uma única linha, útil para atribuições rápidas.
Sintaxe: condicao ? valor_se_verdadeiro : valor_se_falso;



---

## Estrutura

```java
public class Main {

    public static void main(String[] args) {

        //if
        int numero = 10;
        if (numero > 0) {
            System.out.println("O número é positivo.");
        } else if (numero < 0) {
            System.out.println("O número é negativo.");
        } else {
            System.out.println("O número é zero.");
        }
        
        //switch
        int opcao = 2;

        switch (opcao) {
            case 1:
                System.out.println("Iniciando jogo...");
                break;
            case 2:
                System.out.println("Carregando jogo...");
                break;
            default:
                System.out.println("Opção inválida.");
        }
        
        //ternario
        int estoque = 5;
        String status = (estoque > 0) ? "Disponível" : "Esgotado";
        System.out.println(status);

    }

}
```