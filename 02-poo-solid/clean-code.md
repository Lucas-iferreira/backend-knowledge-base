# Clean Code

## O que é?
O Clean Code (Código Limpo) é uma filosofia de desenvolvimento popularizada por Robert C. Martin (conhecido como "Uncle Bob") no livro de mesmo nome.

A ideia central é simples: código é lido muito mais vezes do que é escrito. Escrever código que o computador entende qualquer um consegue; escrever código que outros seres humanos entendem com facilidade é o verdadeiro desafio.

---

## Os Pilares Fundamentais 

### 1. Nomes Significativos e Intuitivos
Variáveis, métodos e classes devem revelar a sua intenção. Se você precisa de um comentário para explicar o que uma variável faz, o nome dela falhou:
- ❌ Ruim: int d; // tempo decorrido em dias
- ✅ Bom: int diasDecorridos; ou int elapsedTimeInDays.


### 2. Funções Pequenas e Focadas (Single Responsibility)
Uma função deve fazer apenas uma coisa, fazê-la bem e ser a única a fazê-la.
- Tamanho curto: Se a função tem mais de 20 linhas, provavelmente dá para dividir.
- Poucos parâmetros: O ideal é de 0 a 2 argumentos. Passar 4 ou 5 parâmetros é um sinal claro de que a função está fazendo coisas demais.


### 3. Comentários Apenas Quando Necessários
O código deve ser autoexplicativo. Comentários frequentemente ficam desatualizados e mentem quando o código muda e o comentário não.
- ❌ Ruim: Usa comentário para explicar código confuso.
- ✅ Bom: Refatora o código para que ele se explique sozinho. Use comentários apenas para explicar o porquê de uma decisão de negócio incomum, não o como.


### 4. Tratamento de Erros Limpo
Erros fazem parte do software, mas não devem poluir a lógica principal.
- Prefira Exceções a retornar códigos de erro (como -1 ou null).
- Não retorne e nem passe null se puder evitar — isso reduz drasticamente as telas azuis de NullPointerException (ou equivalentes).


### 5. Regra do Escoteiro (The Boy Scout Rule)
"Deixe a área de acampamento mais limpa do que como você a encontrou."
Sempre que mexer em um arquivo, refatore um pequeno trecho que esteja confuso, mesmo que não tenha sido você quem escreveu. A qualidade da base de código melhora progressivamente com o tempo.

## Ex bom e ruim

Imagine um trecho que valida e salva uma compra:
❌ Código Poluído (Bad Code)

```java
public void p(Cliente c, List<Item> i) {
    if (c != null && c.getStatus() == 1) {
        double t = 0;
        for (Item item : i) {
            t += item.getPreco();
        }
        if (t > 100) {
            t = t * 0.9; // aplica desconto de 10%
        }
        // Salva pedido...
    }
}
```
✅ Código Limpo (Clean Code)
```java
public void processarPedido(Cliente cliente, List<Item> itens) {
    validarClienteAtivo(cliente);
    
    double valorTotal = calcularValorTotal(itens);
    double valorFinal = aplicarDescontoSeEligivel(valorTotal);

    salvarPedido(cliente, itens, valorFinal);
}

private void validarClienteAtivo(Cliente cliente) {
    if (cliente == null || !cliente.isAtivo()) {
        throw new ClienteInvalidoException("Cliente deve estar ativo para realizar pedidos.");
    }
}

private double aplicarDescontoSeEligivel(double valorTotal) {
    double LIMITE_PARA_DESCONTO = 100.0;
    double PERCENTUAL_DESCONTO = 0.10;

    if (valorTotal > LIMITE_PARA_DESCONTO) {
        return valorTotal * (1 - PERCENTUAL_DESCONTO);
    }
    return valorTotal;
}
```
---