# Matrizes

## O que é?

Matrizes são arrays multidimensionais, ou seja, são estruturas de dados estruturadas em linhas e colunas (como uma tabela de Excel ou uma grade). Em Java, uma matriz é essencialmente um "array de arrays".

---

## Declaração e Inicialização

Você utiliza dois pares de colchetes [][] para representar as dimensões (o primeiro para linhas, o segundo para colunas).

Com tamanho fixo (vazia):
```java
void main() {
    
    // Cria uma matriz com 3 linhas e 4 colunas (vazia)
    int[][] grade = new int[3][4];

}
```

Com valores iniciais:
```java
void main() {
    // Cria uma matriz 2x3 preenchida
    int[][] matriz = {
            {1, 2, 3}, // Linha 0
            {4, 5, 6}  // Linha 1
    };
}
```

## Acesso e modificação

Para acessar ou alterar um valor, você deve indicar o índice da linha e da coluna desejada, lembrando que a contagem sempre começa no zero.

```java
void main() {
    int[][] tabela = {
            {10, 20},
            {30, 40}
    };

// Lendo o valor 30 (Linha 1, Coluna 0)
    System.out.println(tabela[1][0]);

// Alterando o valor 20 para 25 (Linha 0, Coluna 1)
    tabela[0][1] = 25;
}
```

## Como Percorrer uma Matriz

Para ler ou modificar todos os elementos de uma matriz, você precisa usar dois laços for aninhados (um dentro do outro). O primeiro laço controla as linhas e o segundo as colunas.

```java
void main() {
    int[][] numeros = {
            {1, 2},
            {3, 4}
    };

// numeros.length me dá a quantidade de linhas
    for (int linha = 0; linha < numeros.length; linha++) {
        // numeros[linha].length me dá a quantidade de colunas daquela linha
        for (int coluna = 0; coluna < numeros[linha].length; coluna++) {
            System.out.print(numeros[linha][coluna] + " ");
        }
        System.out.println(); // Quebra de linha ao fim de cada linha da matriz
    }
}
```
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