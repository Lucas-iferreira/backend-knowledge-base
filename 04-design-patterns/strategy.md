# Strategy

## O que é?
Substitui cadeias de if/else ou switch isolando variações de um mesmo algoritmo em classes separadas com uma interface comum. Permite trocar a regra de negócio em tempo de execução de forma flexível.

---

### ❌ O Problema (Código Ruim)
Violava o princípio do SOLID (Open/Closed). Qualquer nova regra exigia alterar a mesma classe, criando um método gigante e frágil:

```java
public class CalculadorDeFrete {
    public double calcular(String tipo, double peso) {
        if (tipo.equalsIgnoreCase("SEDEX")) {
            return peso * 1.5 + 10.0;
        } else if (tipo.equalsIgnoreCase("PAC")) {
            return peso * 0.8 + 5.0;
        } else if (tipo.equalsIgnoreCase("RETIRADA")) {
            return 0.0;
        }
        throw new IllegalArgumentException("Tipo inválido");
    }
}
```

### ✅ A Solução com Strategy
```java
// 1. Interface
public interface EstrategiaFrete {
    double calcular(double peso);
}

// 2. Implementações Concretas
public class FreteSedex implements EstrategiaFrete {
    public double calcular(double peso) { return peso * 1.5 + 10.0; }
}

public class FretePac implements EstrategiaFrete {
    public double calcular(double peso) { return peso * 0.8 + 5.0; }
}

// 3. Contexto (quem executa)
public class CalculadorDeFrete {
    private EstrategiaFrete estrategia;

    public CalculadorDeFrete(EstrategiaFrete estrategia) {
        this.estrategia = estrategia;
    }

    public double executar(double peso) {
        return estrategia.calcular(peso);
    }
}
```
### Uso Limpo no Sistema:
```java
public class CheckoutService {
    private final ProcessadorPagamento processador;

    // O serviço só conhece a interface padronizada!
    public CheckoutService(ProcessadorPagamento processador) {
        this.processador = processador;
    }

    public void finalizarCompra(double valor) {
        processador.pagar(valor);
    }
}

// No código principal:
PayPalSDK sdkIncompativel = new PayPalSDK();
ProcessadorPagamento adaptador = new PayPalAdapter(sdkIncompativel);

CheckoutService checkout = new CheckoutService(adaptador);
checkout.finalizarCompra(150.00); // R$ 150,00
```
## ⚡ Quando Usar?
- Quando você quer usar uma classe existente/terceiros, mas a interface dela não é compatível com o resto do seu sistema.
- Para criar uma camada de isolamento contra bibliotecas externas (facilita trocar a biblioteca no futuro sem quebrar seu código).
- Quando você precisa integrar sistemas legados a novas arquiteturas.

---