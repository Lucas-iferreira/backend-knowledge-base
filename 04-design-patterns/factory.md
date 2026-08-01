# Factory

## O que é?
Centraliza a criação de objetos em um único ponto, escondendo a lógica complexa de instanciação (new) e desacoplando o código que usa o objeto da classe concreta.

---

### ❌ O Problema (Código Ruim)
Criação direta com new espalhada por todo o código. Se a classe concreta mudar ou exigir novos parâmetros de construção, você terá que alterar dezenas de lugares no sistema:

```java
public class NotificadorService {
    public void enviar(String tipo, String mensagem) {
        // Forte acoplamento com as classes concretas
        if (tipo.equalsIgnoreCase("EMAIL")) {
            EmailNotificacao email = new EmailNotificacao("smtp.server.com", 587); // Lógica de criação exposta
            email.enviar(mensagem);
        } else if (tipo.equalsIgnoreCase("SMS")) {
            SmsNotificacao sms = new SmsNotificacao("API_KEY_123");
            sms.enviar(mensagem);
        }
    }
}
```

### ✅ A Solução com Factory

```java
// 1. Interface Comum do Produto
public interface Notificacao {
    void enviar(String mensagem);
}

// 2. Implementações Concretas
public class EmailNotificacao implements Notificacao {
    public void enviar(String mensagem) { System.out.println("Email: " + mensagem); }
}

public class SmsNotificacao implements Notificacao {
    public void enviar(String mensagem) { System.out.println("SMS: " + mensagem); }
}

// 3. A Fábrica (Factory)
public class NotificacaoFactory {
    public static Notificacao criar(String tipo) {
        if (tipo.equalsIgnoreCase("EMAIL")) {
            return new EmailNotificacao();
        } else if (tipo.equalsIgnoreCase("SMS")) {
            return new SmsNotificacao();
        }
        throw new IllegalArgumentException("Tipo desconhecido");
    }
}

// 4. Uso Limpo no Client
public class NotificadorService {
    public void enviar(String tipo, String mensagem) {
        Notificacao notificacao = NotificacaoFactory.criar(tipo); // Desacoplado!
        notificacao.enviar(mensagem);
    }
}
```
## ⚡ Quando Usar?
- Quando você não sabe antecipadamente o tipo exato do objeto que precisará criar.
- Quando a criação do objeto envolve lógica complexa (ex: ler configurações, injetar dependências, montar relacionamentos).
- Para evitar duplicar chamadas de new complexas por toda a aplicação.
---