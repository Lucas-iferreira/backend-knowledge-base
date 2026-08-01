# Facade

## O que é?
Fornece uma interface simplificada e única para um conjunto complexo de classes, subsistemas ou bibliotecas. Ele cria uma "fachada" que esconde toda a complexidade de bastidores por trás de uma única chamada simples.

---

### ❌ O Problema (Código Ruim)
O cliente precisa interagir com múltiplas classes e subsistemas complexos para realizar uma única ação do dia a dia, gerando acoplamento alto e duplicação da sequência de inicialização:

```java
// O cliente tem que orquestrar manualmente 4 serviços para processar um pedido:
public class OrderController {
    public void fecharPedido() {
        EstoqueService estoque = new EstoqueService();
        PagamentoService pagamento = new PagamentoService();
        FiscalService fiscal = new FiscalService();
        EmailService email = new EmailService();

        // Passos complexos e acoplados espalhados na aplicação
        if (estoque.temEstoque("ItemA")) {
            if (pagamento.processarCartao("1234", 100.0)) {
                fiscal.emitirNotaFiscal("ItemA");
                email.enviarConfirmacao("cliente@email.com");
            }
        }
    }
}
```

### ✅ A Solução com Facade
```java
// 1. A Fachada (Facade) que orquestra os subsistemas
public class PedidoFacade {
    private final EstoqueService estoque = new EstoqueService();
    private final PagamentoService pagamento = new PagamentoService();
    private final FiscalService fiscal = new FiscalService();
    private final EmailService email = new EmailService();

    // Método único e simples exposto para o mundo externo
    public boolean processarPedido(String item, double valor, String cartao, String emailCliente) {
        if (!estoque.temEstoque(item)) return false;
        if (!pagamento.processarCartao(cartao, valor)) return false;

        fiscal.emitirNotaFiscal(item);
        email.enviarConfirmacao(emailCliente);
        return true;
    }
}
```
### Uso Limpo no Sistema:
```java
public class OrderController {
    private final PedidoFacade pedidoFacade = new PedidoFacade();

    public void fecharPedido() {
        // Toda a complexidade é reduzida a UMA linha de código!
        boolean sucesso = pedidoFacade.processarPedido("ItemA", 100.0, "1234", "cliente@email.com");
    }
}
```
## ⚡ Quando Usar?
- Quando você precisa fornecer uma interface simples para um subsistema muito complexo.
- Para desacoplar seu código de regras internas pesadas ou bibliotecas de terceiros.
- Para organizar a arquitetura em camadas (ex: o Facade serve como o ponto de entrada de um módulo/serviço).

---