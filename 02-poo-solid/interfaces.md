# Interfaces

## O que é?
Interface é um contrato 100% comportamental. Ela serve para dizer o que uma classe deve fazer, mas não se importa em como ela vai fazer.

---

## Por que Interfaces são fundamentais?

- Desacoplamento: Seu código passa a depender de contratos, não de implementações concretas (a base do Spring Boot).
- "Herança Múltipla" de Comportamento: Em Java, uma classe só pode ter uma classe mãe (extends), mas pode implementar várias interfaces (implements).
- Padronização: Garante que diferentes partes do sistema falem a mesma língua.

## Estrutura de uma Interface

Por padrão, tudo dentro de uma interface é public.

```java
public interface ProcessadorPagamento {
    
    // Método abstrato (sem corpo/código): Quem implementar OBRIGATORIAMENTE deve ter este método
    boolean processar(double valor);

    // Método Default (introduzido no Java 8): Permite ter código padrão dentro da interface!
    default void emitirComprovante(double valor) {
        System.out.println("Comprovante emitido no valor de R$ " + valor);
    }
}
```

### Implementando a Interface
Diferente da herança onde usamos extends, com interfaces usamos a palavra implements:

```java
// 1. Implementação para Pix
public class PagamentoPix implements ProcessadorPagamento {
    @Override
    public boolean processar(double valor) {
        System.out.println("Gerando QR Code e validando chave Pix no valor de R$ " + valor);
        return true;
    }
}

// 2. Implementação para Cartão de Crédito
public class PagamentoCartaoCredito implements ProcessadorPagamento {
    @Override
    public boolean processar(double valor) {
        System.out.println("Conectando na adquirente/maquininha e debitando R$ " + valor);
        return true;
    }
}
```

## Múltiplas Interfaces (Onde a mágica acontece)
Imagine que você tem uma classe RelatorioFinanceiro. Ela precisa ser gerada em PDF e também enviada por e-mail:
```java
public interface Exportavel {
    void exportarPdf();
}

public interface Notificavel {
    void enviarEmail(String destinatario);
}

// Uma única classe assinando múltiplos contratos!
public class RelatorioFinanceiro implements Exportavel, Notificavel {

    @Override
    public void exportarPdf() {
        System.out.println("Gerando arquivo PDF do relatório...");
    }

    @Override
    public void enviarEmail(String destinatario) {
        System.out.println("Enviando e-mail para: " + destinatario);
    }
}
```
---