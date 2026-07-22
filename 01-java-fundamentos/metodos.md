# Métodos

## O que é?

Em Java, um método é um bloco de código que executa uma tarefa específica. Eles são equivalentes às funções ou procedimentos de outras linguagens e servem para organizar, reutilizar e modularizar o código.Um método só roda quando é chamado (invocado).

---

## Tipos

Os métodos variam dependendo se recebem dados (parâmetros) ou se devolvem dados (retorno).
1. Sem Retorno e Sem Parâmetros.
Apenas executa uma ação isolada. Usa a palavra-chave `void` (vazio):
```java
public void exibirSaudacao() {
    System.out.println("Olá! Bem-vindo ao sistema.");
}

```

2. Sem Retorno e Com Parâmetros. 
Recebe dados para usar na sua lógica, mas não devolve nada para quem o chamou:
```java
public void imprimirSoma(int a, int b) {
    int resultado = a + b;
    System.out.println("O resultado é: " + resultado);
}

```

3. Com Retorno e Sem Parâmetros.
Não precisa de dados externos, mas devolve um valor (que pode ser guardado em uma variável):
```java
public double obterPi() {
    return 3.14159; // O valor retornado deve ser do tipo double
}

```
4. Com Retorno e Com Parâmetros.
O tipo mais completo. Recebe dados, processa e devolve o resultado.

```java
public int somar(int a, int b) {
    return a + b;
}
```
