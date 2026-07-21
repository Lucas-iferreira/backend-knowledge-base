# Arrays

## O que é?

Arrays (ou vetores) são estruturas de dados que armazenam uma coleção de elementos do mesmo tipo sob um único nome, com um tamanho fixo definido na criação.

---

## Declaração e Inicialização

Existem duas formas principais de criar um array em Java:
- Com tamanho fixo (vazio): Reserva o espaço na memória.
- Com valores iniciais: Define o tamanho pelo número de elementos fornecidos.

---

## Estrutura

```java
public class Main {

    public static void main(String[] args) {

        // Criando um array vazio para 5 números inteiros
        int[] numeros = new int[5];

        // Criando um array já preenchido
        int[] idades = {18, 25, 30, 40};
    }

}
```