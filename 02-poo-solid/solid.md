# SOLID

## O que é?
SOLID é um acrônimo criado por Michael Feathers para organizar os 5 princípios fundamentais do design de código orientado a objetos, popularizados por Robert C. Martin ("Uncle Bob").

O objetivo principal do SOLID é ajudar os desenvolvedores a criar códigos mais fáceis de manter, refatorar, testar e expandir à medida que a aplicação cresce, evitando aquele "código espaguete" onde mexer em um ponto quebra o sistema em outros cinco.

---

## Principios

### 1. S — Single Responsibility Principle (Princípio da Responsabilidade Única)
"Uma classe deve ter um, e apenas um, motivo para mudar."

Uma classe deve focar em fazer apenas uma coisa e fazê-la bem. Se ela faz autenticação, envia e-mail, salva no banco e gera PDF, ela tem responsabilidades demais.
- Violação: Uma classe Usuario que gerencia dados, faz validação e salva no banco de dados.
- Correção: Separar em Usuario (dados), UsuarioRepository (banco de dados) e UsuarioValidator (regras).

### 2. O — Open/Closed Principle (Princípio do Aberto/Fechado)
"Entidades de software devem estar abertas para extensão, mas fechadas para modificação."

Você deve conseguir adicionar novas funcionalidades ao sistema sem alterar o código que já existe e funciona. Isso é atingido usando interfaces e abstração.
- Violação: Vários blocos if/else ou switch para tratar regras que mudam com frequência (ex: calcular desconto por tipo de cliente).
- Correção: Criar uma interface CalculadorDesconto e estendê-la com novas classes para cada tipo de cliente.

### 3. L — Liskov Substitution Principle (Princípio da Substituição de Liskov)
"Classes derivadas devem ser capazes de substituir totalmente suas classes base."

Se B é uma subclasse de A, devemos conseguir usar B no lugar de A sem quebrar o comportamento do programa. A herança deve ser usada com cuidado.
- Violação do clássico exemplo: A classe Quadrado herda de Retangulo. Como o quadrado força largura e altura iguais, ao alterar a largura do retângulo, o programa comporta-se de forma inesperada.
- Correção: Se B não se comporta exatamente como A, não use herança. Use composição ou abstrações adequadas.

### 4. I — Interface Segregation Principle (Princípio da Segregação de Interfaces)
"Uma classe não deve ser forçada a depender de interfaces que ela não utiliza."

É melhor ter várias interfaces pequenas e específicas do que uma interface "gorda" e genérica com dezenas de métodos.
- Violação: Uma interface Impressora com os métodos imprimir(), escanear() e enviarFax(). Uma impressora simples seria obrigada a "implementar" escanear() e fax() mesmo sem ter esses recursos.
- Correção: Dividir em interfaces separadas: Impressora, Escaneadora e Fax.

### 5. D — Dependency Inversion Principle (Princípio da Inversão de Dependência)
"Dependa de abstrações, não de implementações concretas."

Módulos de alto nível (as regras do negócio) não devem depender de módulos de baixo nível (banco de dados, frameworks, APIs externas). Ambos devem depender de interfaces/abstrações.
- Violação: O serviço PedidoService cria diretamente uma instância de MySQLDatabase (new MySQLDatabase()).
- Correção: O PedidoService recebe uma interface RepositorioPedido no seu construtor. Assim, trocar o banco de dados (por exemplo, para MongoDB ou PostgreSQL) não impacta as regras de negócio.

## Resumo


|             Princípio             |             O que ele evita?             |             Qual o resultado?             |
|:---------------------------------:|:----------------------------------------:|:-----------------------------------------:|
|    S (Responsabilidade Única)     |       Classes gigantes e acopladas       |   Código mais legível e fácil de testar   |
|        O (Aberto/Fechado)         |    Efeito colateral ao criar recursos    |          Facilidade de expansão           |
|    L (Substituição de Liskov)     |   Comportamentos estranhos na herança    |     Polimorfismo seguro e previsível      |
|    I (Segregação de Interface)    |        Métodos inútiles ou vazios        |        Interfaces limpas e coesas         |
|    D (Inversão de Dependência)    | Dependência rígida de bibliotecas/bancos |  Código desacoplado e altamente testável  |


## Exemplos

### 1. Single Responsibility Principle (SRP)
Uma classe deve ter apenas um motivo para mudar.

❌ Antes (Violando o SRP)
A classe `RelatorioService` faz tudo: gera o relatório, salva no banco e envia e-mail.

```java
public class RelatorioService {
    public void gerarRelatorio() {
        System.out.println("Gerando dados do relatório...");
    }

    public void salvarNoBanco() {
        System.out.println("Salvando no banco de dados...");
    }

    public void enviarPorEmail() {
        System.out.println("Enviando e-mail...");
    }
}
```
✅ Depois (Aplicando o SRP)
Dividimos cada responsabilidade em uma classe especializada.

```java
public class RelatorioGenerator {
    public void gerar() {
        System.out.println("Gerando dados do relatório...");
    }
}

public class RelatorioRepository {
    public void salvar() {
        System.out.println("Salvando no banco de dados...");
    }
}

public class EmailService {
    public void enviar() {
        System.out.println("Enviando e-mail...");
    }
}
```

### 2. Open/Closed Principle (OCP)
Aberto para extensão, mas fechado para modificação.

❌ Antes (Violando o OCP)
Toda vez que surgir um novo tipo de desconto, teremos que modificar a classe `CalculadoraDesconto` adicionando novos `if/else` ou `switch`.


```java
public class CalculadoraDesconto {
    public double calcular(String tipoCliente, double valor) {
        if ("COMUM".equals(tipoCliente)) {
            return valor * 0.05;
        } else if ("VIP".equals(tipoCliente)) {
            return valor * 0.15;
        }
        return 0;
    }
}
```

✅ Depois (Aplicando o OCP)
Criamos uma interface. Para adicionar um novo desconto, basta criar uma nova classe sem alterar o código existente.
```java
public interface Desconto {
    double calcular(double valor);
}

public class DescontoComum implements Desconto {
    public double calcular(double valor) {
        return valor * 0.05;
    }
}

public class DescontoVIP implements Desconto {
    public double calcular(double valor) {
        return valor * 0.15;
    }
}

// A calculadora não precisa mais ser alterada se surgirem novos tipos de desconto
public class CalculadoraDesconto {
    public double calcular(Desconto desconto, double valor) {
        return desconto.calcular(valor);
    }
}
```

### 3. Liskov Substitution Principle (LSP)
Subclasses devem poder substituir suas classes base sem quebrar o programa.

❌ Antes (Violando o LSP)
A subclasse `ContaPoupanca` herda de `ContaBancaria`, mas lança uma exceção no método `sacar()` se ultrapassar o limite, alterando o comportamento esperado da classe pai.

```java
public class ContaBancaria {
    protected double saldo;

    public void sacar(double valor) {
        this.saldo -= valor;
    }
}

public class ContaPoupanca extends ContaBancaria {
    @Override
    public void sacar(double valor) {
        if (valor > saldo) {
            throw new RuntimeException("Poupança não pode ter saldo negativo!"); // Quebra a expectativa da classe pai
        }
        this.saldo -= valor;
    }
}
```

✅ Depois (Aplicando o LSP)
Extraímos as regras de validação para a abstração ou usamos um design onde o comportamento de saque seja coeso entre as implementações.

```java
public abstract class Conta {
    protected double saldo;

    public abstract void sacar(double valor);
}

public class ContaCorrente extends Conta {
    @Override
    public void sacar(double valor) {
        this.saldo -= valor; // Pode ter saldo negativo (cheque especial)
    }
}

public class ContaPoupanca extends Conta {
    @Override
    public void sacar(double valor) {
        if (valor <= saldo) {
            this.saldo -= valor;
        }
    }
}
```

### 4. Interface Segregation Principle (ISP)
Uma classe não deve ser forçada a depender de métodos que não utiliza.

❌ Antes (Violando o ISP)
Uma interface gigante força a `ImpressoraSimples` a implementar o método `escanear()`, que ela não possui.

```java
public interface DispositivoMultifuncional {
    void imprimir();
    void escanear();
}

public class ImpressoraSimples implements DispositivoMultifuncional {
    public void imprimir() {
        System.out.println("Imprimindo...");
    }

    public void escanear() {
        // Impressora simples não escaneia, mas é obrigada a implementar
        throw new UnsupportedOperationException("Não suportado!");
    }
}
```

✅ Depois (Aplicando o ISP)
Segregamos em interfaces pequenas e focadas.

```java
public interface Impressora {
    void imprimir();
}

public interface Escaneadora {
    void escanear();
}

// Implementa apenas o que realmente usa
public class ImpressoraSimples implements Impressora {
    public void imprimir() {
        System.out.println("Imprimindo...");
    }
}

// Equipamento avançado pode implementar ambas
public class ImpressoraAvancada implements Impressora, Escaneadora {
    public void imprimir() { System.out.println("Imprimindo..."); }
    public void escanear() { System.out.println("Escaneando..."); }
}
```

### 5. Dependency Inversion Principle (DIP)
Dependa de abstrações (interfaces), não de implementações concretas.

❌ Antes (Violando o DIP)
O `PedidoService` depende diretamente da classe concreta `MySQLRepository`. Se quisermos trocar para PostgreSQL ou MongoDB, teremos que alterar o `PedidoService`.

```java
public class MySQLRepository {
    public void salvar(String pedido) {
        System.out.println("Salvando pedido no MySQL...");
    }
}

public class PedidoService {
    private MySQLRepository repository = new MySQLRepository(); // Alto acoplamento

    public void processarPedido(String pedido) {
        repository.salvar(pedido);
    }
}
```
✅ Depois (Aplicando o DIP)
Invertemos a dependência usando uma interface. O `PedidoService` agora depende apenas de uma abstração (`PedidoRepository`), que é injetada via construtor.

```java
public interface PedidoRepository {
    void salvar(String pedido);
}

public class MySQLRepository implements PedidoRepository {
    public void salvar(String pedido) {
        System.out.println("Salvando pedido no MySQL...");
    }
}

public class MongoRepository implements PedidoRepository {
    public void salvar(String pedido) {
        System.out.println("Salvando pedido no Mongo...");
    }
}

public class PedidoService {
    private final PedidoRepository repository;

    // Injeção de dependência via construtor
    public PedidoService(PedidoRepository repository) {
        this.repository = repository;
    }

    public void processarPedido(String pedido) {
        repository.salvar(pedido);
    }
}
```
---