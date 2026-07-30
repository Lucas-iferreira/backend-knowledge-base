# Generics

## O que é?

Permitem parametrizar tipos. Em vez de fixar um tipo específico (como String ou Integer) em uma classe,
interface ou método, você utiliza um tipo genérico como parâmetro formal (geralmente representado por `<T>`, `<E>`, `<K, V>`).

Isso permite reutilizar algoritmos e estruturas de dados garantindo segurança de tipos em tempo de compilação (Type Safety).

---

## Benefícios
- Segurança de Tipos (Type Safety): Erros de incompatibilidade de tipos são capturados durante a compilação, prevenindo exceções em tempo de execução como ClassCastException.
- Eliminação de Casts Explícitos: Não é necessário fazer o cast manual ao recuperar objetos de coleções ou classes genéricas.
- Reutilização de Código: O mesmo código funciona para diferentes tipos sem necessidade de duplicação.

## Sintaxe e Exemplos Práticos

### 1. Classe Genérica
Uma classe que opera sobre um tipo abstrato T:

```java
public class Caixa<T> {
    private T conteudo;

    public void guardar(T conteudo) {
        this.conteudo = conteudo;
    }

    public T recuperar() {
        return conteudo;
    }
}

// Uso:
Caixa<String> caixaTexto = new Caixa<>();
caixaTexto.guardar("Java 21");
String texto = caixaTexto.recuperar(); // Sem necessidade de cast!
```

### 2. Métodos Genéricos
Você pode definir métodos genéricos em classes normais ou genéricas:

```java

public class Utilitario {
    public static <T> void imprimirArray(T[] array) {
        for (T elemento : array) {
            System.out.println(elemento);
        }
    }
}
```

### 3. Delimitação de Tipos (Bounded Types)
Para restringir o tipo T a uma hierarquia específica (por exemplo, apenas tipos numéricos):

```java
// T deve ser Number ou qualquer subclasse de Number (Double, Integer, etc.)
public class Calculadora<T extends Number> {
    private T valor;

    public double aoQuadrado() {
        return valor.doubleValue() * valor.doubleValue();
    }
}
```

## Wildcards (?) e a Regra PECS
O caractere curinga ? representa um tipo desconhecido. É fundamental para trabalhar com polimorfismo em coleções genéricas.

|                         Sintaxe                          |         Conceitos          |                                         Uso Principal                                          |
|:--------------------------------------------------------:|:--------------------------:|:----------------------------------------------------------------------------------------------:|
|                        `List<?> `                        |     Unbounded Wildcard     |                      Leitura de objetos onde a tipagem exata não importa.                      |
|                 `List<? extends Number>`                 |   Upper Bounded Wildcard   | Producer Extends: A lista produz (fornece) dados para leitura. Aceita `Number` ou subclasses.  |
|                 `List<? super Integer>`                  |   Lower Bounded Wildcard   | Consumer Super: A lista consome (recebe) dados para escrita. Aceita `Integer` ou superclasses. |

Regra PECS: Producer Extends, Consumer Super. Se você só lê do objeto genérico, use extends. Se você só escreve nele, use super.

---