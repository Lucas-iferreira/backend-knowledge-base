# Functional Interfaces

## O que é?
Uma Functional Interface (Interface Funcional) em Java é uma interface que possui exatamente um método abstrato.
Elas são o pilar central da programação funcional introduzida no Java 8, pois servem de base para expressões `Lambda` e `Method References`.

---


## Pontos Chave
### 1. Estrutura Básica e a Anotação @FunctionalInterface
Embora não seja obrigatória, a anotação `@FunctionalInterface` deve ser usada para indicar 
que a interface é funcional. O compilador emitirá um erro caso você tente adicionar um segundo método abstrato.

```java
@FunctionalInterface
public interface Calculadora {
    int executar(int a, int b); // Único método abstrato

    // Pode conter métodos default/static sem violar a regra:
    default void mensagem() {
        System.out.println("Executando cálculo...");
    }
}
```
Como utilizar LAMBDA:
```java
public class Teste {
    public static void main(String[] args) {
        // Implementação via Expressão Lambda
        Calculadora soma = (a, b) -> a + b;
        Calculadora multiplicacao = (a, b) -> a * b;

        System.out.println(soma.executar(5, 3));          // Saída: 8
        System.out.println(multiplicacao.executar(5, 3)); // Saída: 15
    }
}
```

### 2. As Principais Interfaces Funcionais Nativas (`java.util.function`)
Na maioria das vezes, você não precisa criar suas próprias interfaces. 
O Java já fornece um pacote com as mais comuns:


|      Interface       | Assinatura do método  |                                   O que faz?                                    |                           Exemplo de Uso                           |
|:--------------------:|:---------------------:|:-------------------------------------------------------------------------------:|:------------------------------------------------------------------:|
|    `Predicate<T>`    |  `boolean test(T t)`  |            Recebe 1 parâmetro e retorna boolean. Usado para filtros.            |                       `s -> s.length() > 5`                        |
|   `Function<T, R`    |    `R apply(T t)`     |     Recebe 1 parâmetro (T) e retorna 1 valor (R). Usado para transformação.     |                     `str -> str.toUpperCase()`                     |
|    `Consumer<T>`     |  `void accept(T t)`   |   Recebe 1 parâmetro e não retorna nada. Usado para ações/efeitos colaterais.   |                 `item -> System.out.println(item)`                 |
|    `Supplier<T>`     |       `T get()`       |    Não recebe parâmetros e retorna 1 valor. Usado para gerar/fornecer dados.    |                       `() -> Math.random()`                        |
|  `UnaryOperator<T>`  |    `T apply(T t)`     |      Tipo especial de `Function` onde a entrada e saída são do mesmo tipo.      |                            `n -> n * 2`                            |


### 3. Exemplos Práticos no Dia a Dia
A. Filter com `Predicate` e Foreach com `Consumer`
```java
import java.util.List;
import java.util.function.Predicate;

public class ExemploPratico {
    public static void main(String[] args) {
        List<String> nomes = List.of("Ana", "Bruno", "Carlos", "Amanda");

        // Predicate: verifica se começa com "A"
        Predicate<String> comecaComA = nome -> nome.startsWith("A");

        nomes.stream()
             .filter(comecaComA)
             .forEach(nome -> System.out.println(nome)); // Consumer
    }
}
```
B. Mapeamento de Dados com `Function`

```java
import java.util.List;
import java.util.function.Function;

public class ExemploFunction {
    public static void main(String[] args) {
        List<String> nomes = List.of("ana", "bruno", "carlos");

        // Function: transforma String em String em caixa alta
        Function<String, String> paraMaiusculas = String::toUpperCase;

        List<String> maiusculas = nomes.stream()
                                       .map(paraMaiusculas)
                                       .toList();

        System.out.println(maiusculas); // [ANA, BRUNO, CARLOS]
    }
}
```

### 4. Variações Comuns (Bi-Interfaces e Primitivos)
Quando você precisa de dois parâmetros de entrada, o Java disponibiliza as versões "Bi":

- `BiPredicate<T, U>: boolean test(T t, U u)`
- `BiFunction<T, R U,>: R apply(T t, U u)`
- `BiConsumer<T, U>: void accept(T t, U u)`
- `BinaryOperator<T>: T apply(T t1, T t2)` (Ambos os parâmetros e o retorno são do mesmo tipo)

Performance com Primitivos: Para evitar o custo de boxing/unboxing (ex: conversão de int para Integer),
existem versões otimizadas como `IntPredicate`, `DoubleFunction`, `LongConsumer`, etc.

---