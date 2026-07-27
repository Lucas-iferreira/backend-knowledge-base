# Classes Abstratas

## O que é?
Modelo genérico ("rascunho") usado como base para criar outras classes. Ela serve para compartilhar atributos e comportamentos comuns entre classes filhas, mas não pode ser instanciada diretamente (você não consegue dar um new ClasseAbstrata()).

Pense nela como a ideia genérica de "Animal": você não encontra um "animal" puro andando na rua, mas sim instâncias concretas como um Cachorro ou um Gato, que herdam características gerais de Animal.

---

## Principais Regras e Conceitos

- Palavra-chave abstract: Usada na declaração da classe e dos métodos abstratos.
- Métodos Abstratos: Métodos sem corpo (sem {} e sem código). Definem uma assinatura obrigatória que todas as classes filhas concretas devem implementar.
- Métodos Concretos: Métodos normais (com código) que podem ser reutilizados diretamente pelas classes filhas.
- Sem `new`: Não é possível criar um objeto direto a partir de uma classe abstrata.

## Exemplo
Imagina que estamos criando um sistema de pagamentos:

```java 
 // Classe abstrata pai
abstract class Pagamento {
    protected double valor;

    public Pagamento(double valor) {
        this.valor = valor;
    }

    // Método concreto: reutilizado por todas as subclasses
    public void emitirComprovante() {
        System.out.println("Comprovante no valor de R$ " + valor);
    }

    // Método abstrato: cada meio de pagamento processa de um jeito diferente
    public abstract void processar();
}
```

Agora criamos as classes filhas que extendem Pagamento e são obrigadas a implementar o método `processar()`:

```java
// Classe concreta 1
class CartaoCredito extends Pagamento {
    private String numeroCartao;

    public CartaoCredito(double valor, String numeroCartao) {
        super(valor);
        this.numeroCartao = numeroCartao;
    }

    @Override
    public void processar() {
        System.out.println("Validando limite e cobrando no cartão: " + numeroCartao);
    }
}

// Classe concreta 2
class Pix extends Pagamento {
    private String chavePix;

    public Pix(double valor, String chavePix) {
        super(valor);
        this.chavePix = chavePix;
    }

    @Override
    public void processar() {
        System.out.println("Gerando QR Code Pix para a chave: " + chavePix);
    }
}
```

Como fica o uso no programa principal `(main)`:
```java
public class Main {
    public static void main(String[] args) {
        // Pagamento p = new Pagamento(100); // ❌ ERRO! Não pode instanciar classe abstrata

        Pagamento pag1 = new CartaoCredito(150.00, "1234-5678-9012");
        Pagamento pag2 = new Pix(80.00, "user@email.com");

        pag1.processar();          // Imprime a lógica do cartão
        pag1.emitirComprovante();  // Método herdado da classe pai

        pag2.processar();          // Imprime a lógica do Pix
        pag2.emitirComprovante();  // Método herdado da classe pai
    }
}
```

---