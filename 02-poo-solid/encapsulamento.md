# Encapsulamento

## O que é?
A ideia central é simples: ocultar os detalhes internos de como a classe funciona e expor apenas uma interface segura para o mundo externo.

---

## Para que serve?

- Proteção de dados: Impede que código externo defina valores inválidos ou inconsistentes (ex: saldo = -5000 em uma conta poupança).
- Flexibilidade: Você pode alterar a implementação interna da classe no futuro sem quebrar o código que a chama.
- Controle de leitura/escrita: Permite criar atributos que são só de leitura (read-only) ou só de escrita.

## Como implementar (A Receita Padrão)

- Torne todos os atributos private.
- Forneça métodos públicos (getters e setters) ou métodos de negócio para ler e modificar esses atributos de forma controlada.

### Exemplo Incorreto (Sem Encapsulamento)

```java
// RUIM: Atributos públicos permitem bagunça e valores inválidos
public class ContaBancaria {
    public double saldo; 
}

// Em outro lugar do código:
ContaBancaria conta = new ContaBancaria();
conta.saldo = -500.0; // Negativou o saldo sem passar por nenhuma validação!
```


### Exemplo Correto (Com Encapsulamento)

```java
public class ContaBancaria {
    
    // 1. Atributo privado e protegido
    private double saldo;

    public ContaBancaria(double saldoInicial) {
        if (saldoInicial >= 0) {
            this.saldo = saldoInicial;
        }
    }

    // 2. Método de negócio encapsulando a regra
    public void depositar(double valor) {
        if (valor > 0) {
            this.saldo += valor;
            System.out.println("Depósito realizado. Novo saldo: R$ " + this.saldo);
        } else {
            System.out.println("Valor de depósito inválido.");
        }
    }

    public void sacar(double valor) {
        if (valor > 0 && valor <= this.saldo) {
            this.saldo -= valor;
            System.out.println("Saque realizado. Saldo restante: R$ " + this.saldo);
        } else {
            System.out.println("Saque não permitido: saldo insuficiente ou valor inválido.");
        }
    }

    // 3. Getter controlado (apenas leitura do saldo)
    public double getSaldo() {
        return this.saldo;
    }
    
    // Note: Não colocamos 'setSaldo(double saldo)' para impedir que alguém 
    // force o valor do saldo arbitrariamente!
}
```

## Java Records
Se você precisa apenas de uma classe que carrega dados (como um DTO) e quer que ela seja totalmente encapsulada e imutável sem escrever todo o boilerplate de private, getters, equals e hashCode, o Java moderno resolve com Records:
```java
// Automaticamente define os campos como private final, 
// cria o construtor e os métodos de leitura (ex: conta.numero())
public record ContaDTO(String numero, double saldo) {
    
    // Construtor compacto para validação rápida no encapsulamento
    public ContaDTO {
        if (saldo < 0) {
            throw new IllegalArgumentException("Saldo inicial não pode ser negativo");
        }
    }
}

```
- 💡 Resumo do Dev: Encapsular não é só colocar private no atributo e sair criando get e set pra tudo. É proteger regras de negócio e garantir que o objeto continue sempre em um estado válido.

---