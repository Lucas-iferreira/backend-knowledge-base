# Lambda

## O que é?
Introduzidas no Java 8 são funções anônimas — funções que não precisam de um nome, modificador de acesso ou tipo de retorno explícito.
Elas permitem tratar código como dado, possibilitando passar blocos de código como parâmetros para métodos. Essa é a base da programação funcional em Java.

---

## Sintaxe da Expressão Lambda
A estrutura básica é dividida em três partes pelo operador `->`:
 $$\text{(Parâmetros)} \longrightarrow \{\text{Corpo da Função}\}$$

```java
// Sintaxe completa
(int a, int b) -> { return a + b; }

// Sintaxe enxuta (tipos inferidos e retorno implícito para linha única)
(a, b) -> a + b
```

## Interfaces Funcionais (O "Motor" dos Lambdas)
Um lambda no Java não existe no vácuo: 
ele é sempre a implementação direta de uma Interface Funcional (uma interface que possui exatamente um método abstrato, anotada com `@FunctionalInterface`).

O pacote `java.util.function` traz as principais interfaces pré-definidas no JDK:

|    Interface     |  Assinatura do Método  |                       O que faz?                        |      Exemplo de Lambda       |
|:----------------:|:----------------------:|:-------------------------------------------------------:|:----------------------------:|
|  `Predicate<T>`  |  `boolean test(T t)`   |    Recebe $T$ e retorna `boolean` (testes/filtros).     |      `s -> s.isEmpty()`      |
|  `Consumer<T>`   |   `void accept(T t)`   | Recebe $T$ e não retorna nada (efeito colateral/print). | `s -> System.out.println(s)` |
| `Function<T, R>` |     `R apply(T t)`     |      Recebe $T$ e transforma em $R$ (mapeamento).       |      `s -> s.length()`       |
|  `Supplier<T>`   |       `T get()`        |         Não recebe nada e fornece/gera um $T$.          |  `() -> UUID.randomUUID()`   |


## Exemplos Práticos de Uso

### 1. Iteração em Coleções 
`forEach`

```java
List<String> nomes = List.of("Ana", "Bruno", "Carlos");

// Antes do Java 8:
for (String nome : nomes) {
    System.out.println(nome);
}

// Com Lambda (usando Consumer<T>):
nomes.forEach(nome -> System.out.println(nome));
```

### 2. Ordenação 
`Comparator`
```java
List<String> frutas = Arrays.asList("Maçã", "Uva", "Banana");

// Ordenando por tamanho da String:
frutas.sort((f1, f2) -> Integer.compare(f1.length(), f2.length()));
```

### Method References
`::` -> Quando uma expressão lambda apenas repassa um parâmetro para um método já existente, você pode torná-la ainda mais limpa usando `Method References`:
```java
// Com Lambda:
nomes.forEach(nome -> System.out.println(nome));

// Com Method Reference (equivalente):
nomes.forEach(System::out);
```


- Classe: O molde ou a planta baixa que define o que o objeto será (ex: a planta de um carro).
- Objeto: A instância real e física criada a partir da classe (ex: um carro azul específico na rua).
- Atributos: As características e dados que o objeto possui (ex: cor, modelo, velocidade atual).
- Métodos: As ações e comportamentos que o objeto pode executar (ex: acelerar, frear, ligar farol).

---