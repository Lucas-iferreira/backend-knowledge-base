# Adapter

## O que é?
Funciona como um adaptador de tomada: permite que objetos com interfaces incompatíveis colaborem entre si. Ele converte a interface de uma classe existente para outra interface esperada pelo seu sistema sem precisar alterar o código original.

---

### ❌ O Problema (Código Ruim)
Você precisa integrar uma biblioteca externa ou sistema legado, mas os nomes de métodos e tipos de dados não batem com o padrão do seu sistema. Tentar alterar o sistema legado ou poluir seu código com conversões diretas cria um acoplamento confuso:

```java
// O seu sistema espera esta interface:
public interface ProcessadorPagamento {
    void pagar(double valor);
}

// Mas a biblioteca externa (API legada/terceiros) usa métodos totalmente diferentes:
public class PayPalSDK {
    public void sendPayment(double amountInCents, String currency) {
        System.out.println("Pago via PayPal: " + amountInCents + " cents");
    }
}

// Código ruim: Tentar usar a SDK diretamente espalha conversões pelo sistema
public class CheckoutService {
    private PayPalSDK payPal = new PayPalSDK();

    public void checkout(double valor) {
        // Seu sistema fala 'pagar(valor)', mas você é forçado a converter manualmente aqui
        payPal.sendPayment(valor * 100, "BRL");
    }
}
```

### ✅ A Solução com Adapter
```java
// 1. A interface que o seu sistema já entende (Target)
public interface ProcessadorPagamento {
    void pagar(double valor);
}

// 2. A classe incompatível que você precisa usar (Adaptee)
public class PayPalSDK {
    public void sendPayment(double amountInCents, String currency) {
        System.out.println("Pago via PayPal: " + (amountInCents / 100) + " " + currency);
    }
}

// 3. O Adaptador (Adapter)
public class PayPalAdapter implements ProcessadorPagamento {
    private final PayPalSDK payPal;

    public PayPalAdapter(PayPalSDK payPal) {
        this.payPal = payPal;
    }

    @Override
    public void pagar(double valor) {
        // Converte a chamada do seu sistema para a chamada esperada pela SDK
        double valorEmCentavos = valor * 100;
        payPal.sendPayment(valorEmCentavos, "BRL");
    }
}
```
## ⚡ Quando Usar?
- Quando você tem muitos if/else para decidir regras de cálculo ou fluxos.
- Quando precisa alterar a regra de negócio dinamicamente durante a execução.
- Para adicionar novas regras apenas criando novas classes, sem alterar o código que já funciona.

---