# Optional

## O que é?
Introduzido no `Java 8` -> é um container de valor que pode ou não conter um objeto não-nulo.

O objetivo principal do Optional é fornecer um tipo de retorno mais expressivo para métodos que podem não retornar um valor, ajudando a evitar exceções de ponteiro `nulo` (`NullPointerException`) sem a necessidade de múltiplos blocos e checagens `if` (`obj != null`).

---

## Como Criar um Optional
Existem três formas principais de instanciar um `Optional`:

```java
// 1. Instância vazia
Optional<String> vazio = Optional.empty();

// 2. Com valor não-nulo (Lança NullPointerException imediatamente se o valor for null)
Optional<String> comValor = Optional.of("Dev Java");

// 3. Aceita valor nulo (Retorna Optional.empty() se for null)
String nome = null;
Optional<String> possivelmenteNulo = Optional.ofNullable(nome);
```

## Métodos Mais Utilizados para Consumir o Valor
A melhor prática com Optional é evitar o uso do tradicional .isPresent() combinado com .get(). Em vez disso, prefira a programação funcional com os métodos da própria API:

### 1. Fornecendo um Valor Default
- `orElse(valor)`: Retorna o valor interno ou um valor padrão caso esteja vazio. (Atenção: o valor padrão é avaliado sempre, mesmo se o Optional não for vazio).
- `orElseGet(() -> fabricarValor())`: Retorna o valor interno ou avalia a `Supplier` apenas se o Optional estiver vazio (Lazy Evaluation).


```java
String resultado = Optional.ofNullable(nome)
                           .orElseGet(() -> "Nome Padrão");
```

### 2. Lançando Exceção se Estiver Vazio
- `orElseThrow()`: Lança NoSuchElementException se vazio (Java 10+).
- `orElseThrow(() -> new ExceçãoCustomizada())`: Lança uma exceção personalizada de negócio.
```java
String usuario = Optional.ofNullable(nome)
                         .orElseThrow(() -> new RecursoNaoEncontradoException("Usuário não encontrado."));
```

### 3. Mapeando e Transformando Valores
Você pode encadear transformações com `map`, `flatMap` e `filter` de forma idêntica à API de Streams:

```java
Optional<String> resultado = Optional.ofNullable(usuario)
    .filter(u -> u.isAtivo())
    .map(u -> u.getNome().toUpperCase());
```

## Boas Práticas e Anti-Patterns
Regra de Ouro: O `Optional` foi projetado para ser usado primariamente como tipo de retorno de métodos.


|                                                O que FAZER ✅                                                 |                                          O que EVITAR ❌                                           |
|:-------------------------------------------------------------------------------------------------------------:|:--------------------------------------------------------------------------------------------------:|
| Usar em retornos de métodos que podem não encontrar um resultado (ex: `findByEmail()` no Spring Data / JPA).  |      Não use em parâmetros de métodos ou construtores (dificulta a chamada e polui o código).      |
|                             Usar métodos funcionais (`map, flatMap, orElseGet`).                              |              Não use em campos/atributos de classes (`Optional` não é serializável).               |
|              Tratar cenários onde a ausência de valor é um resultado válido da regra de negócio.              | Evitar o encadeamento `.isPresent()` seguido de `.get()`. É apenas um `if (x != null)` disfarçado. |

---