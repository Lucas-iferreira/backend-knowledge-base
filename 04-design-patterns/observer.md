# Observer

## O que é?
Define um mecanismo de assinatura (publish/subscribe) para notificar múltiplos objetos (observadores) automaticamente sobre qualquer evento ou mudança de estado que ocorra no objeto que eles estão monitorando (sujeito).

---

### ❌ O Problema (Código Ruim)
Acoplamento forte e chamadas diretas engessadas. Toda vez que um evento acontece, o objeto principal precisa conhecer e chamar explicitamente cada dependência. Adicionar uma nova notificação exige alterar a classe principal:

```java
public class Pedido {
    // Se precisar adicionar "NotificacaoWhatsApp", terá que alterar esta classe!
    private EmailService emailService = new EmailService();
    private EstoqueService estoqueService = new EstoqueService();

    public void finalizarPedido() {
        // Lógica do pedido...

        // Chamadas engessadas diretamente no código
        emailService.enviarEmailConfirmacao();
        estoqueService.baixarEstoque();
    }
}
```

### ✅ A Solução com Observer
```java 
import java.util.ArrayList;
import java.util.List;

// 1. Interface do Observador (Subscriber)
public interface PedidoObserver {
    void onPedidoFinalizado(Pedido pedido);
}

// 2. Observadores Concretos
public class EmailObserver implements PedidoObserver {
    public void onPedidoFinalizado(Pedido pedido) {
        System.out.println("Email enviado para o pedido: " + pedido.getId());
    }
}

public class EstoqueObserver implements PedidoObserver {
    public void onPedidoFinalizado(Pedido pedido) {
        System.out.println("Estoque atualizado para o pedido: " + pedido.getId());
    }
}

// 3. O Sujeito (Publisher)
public class Pedido {
    private final String id;
    private final List<PedidoObserver> observadores = new ArrayList<>();

    public Pedido(String id) { this.id = id; }
    public String getId() { return id; }

    // Métodos para inscrever/desinscrever observadores
    public void adicionarObservador(PedidoObserver observer) {
        observadores.add(observer);
    }

    public void removerObservador(PedidoObserver observer) {
        observadores.remove(observer);
    }

    // Notifica todos os inscritos
    public void finalizarPedido() {
        System.out.println("Finalizando pedido " + id + "...");
        
        for (PedidoObserver obs : observadores) {
            obs.onPedidoFinalizado(this);
        }
    }
}
```
Uso Limpo e Desacoplado:
```java
Pedido pedido = new Pedido("1001");

// Inscreve os interessados
pedido.adicionarObservador(new EmailObserver());
pedido.adicionarObservador(new EstoqueObserver());

// Executa: todos os inscritos serão notificados automaticamente!
pedido.finalizarPedido();
```

## ⚡ Quando Usar?
- Quando uma mudança no estado de um objeto exige ações em outros objetos, e você não sabe quantos objetos precisam reagir.
- Para criar arquiteturas orientadas a eventos com baixo acoplamento.
- Quando o conjunto de objetos que deve reagir pode mudar dinamicamente em tempo de execução.

---