# Exceptions

## O que é?
O tratamento de exceções é o mecanismo robusto utilizado para gerenciar erros e situações anômalas em tempo de execução, permitindo que a aplicação trate falhas de forma controlada sem quebrar abruptamente (crashar).
O topo de toda essa hierarquia é a classe `Throwable`, que se divide em duas ramificações principais: `Error` e `Exception`.

---

## A Hierarquia das Exceções


|           Tipo            |                                                          Descrição                                                          |                                             Exemplo                                             | Deve ser usado?                                    |
|:-------------------------:|:---------------------------------------------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------:|:---------------------------------------------------|
|          `Error`          |           Problemas graves no ambiente de execução ou na JVM. Geralmente fora do alcance do código da aplicação.            |                            `OutOfMemoryError`, `StackOverflowError`                             | Não. Não devem ser capturados.                     |
|    `Checked Exception`    | Exceções verificadas em tempo de compilação. O compilador obriga você a tratá-las (`try-catch`) ou declará-las (`throws`).  |                                  `IOException`, `SQLException`                                  | Sim. Obrigatório por lei (do compilador).          |
|   `Unchecked Exception`   |               Exceções de tempo de execução (Runtime). Geralmente refletem bugs de lógica ou mau uso de APIs.               |         `NullPointerException`, `IllegalArgumentException`, `IndexOutOfBoundsException`         | Opcional. Boa prática evitar via código defensivo. |


## Sintaxe e Estruturas de Tratamento

### 1. Bloco `try-catch-finally`
Utilizado para capturar e tratar exceções, garantindo a execução de códigos de limpeza (como fechar conexões) no bloco `finally`.

```java
try {
    // Código que pode lançar uma exceção
    int resultado = 10 / 0;
} catch (ArithmeticException e) {
    // Tratamento específico para o erro
    System.err.println("Erro matemático: " + e.getMessage());
} finally {
    // Sempre executa, ocorrendo erro ou não
    System.out.println("Bloco final executado.");
}
```
### 2. Try-with-Resources (Gerenciamento Automático de Recursos)
Disponível a partir do Java 7, fecha automaticamente qualquer recurso que implemente `AutoCloseable` (como conexões de banco de dados, `Streams` ou arquivos),
eliminando a necessidade de blocos `finally` verbosos.

```java
// O BufferedReader será fechado automaticamente ao final do bloco
try (BufferedReader br = new BufferedReader(new FileReader("arquivo.txt"))) {
    String linha = br.readLine();
    System.out.println(linha);
} catch (IOException e) {
    System.err.println("Erro ao ler o arquivo: " + e.getMessage());
}
```

## Criando Exceções Customizadas
Muitas vezes, as exceções padrão do Java não expressam o domínio do seu negócio. Você pode criar suas próprias exceções estendendo `RuntimeException` (para regras de negócio/unchecked) ou `Exception` (para checked).

```java
// Exceção customizada para regra de negócio (Unchecked)
public class SaldoInsuficienteException extends RuntimeException {
    public SaldoInsuficienteException(String mensagem) {
        super(mensagem);
    }
}

// Uso:
public void sacar(double valor) {
    if (valor > saldo) {
        throw new SaldoInsuficienteException("Tentativa de saque maior que o saldo disponível.");
    }
}
```
---