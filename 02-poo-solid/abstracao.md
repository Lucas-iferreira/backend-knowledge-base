# Abstração

## O que é?
- Foca apenas no que é essencial para o programa.
- Esconde a parte complexa que não precisa ser vista.
- Transforma objetos do mundo real em modelos simples no código.
---

## Exemplo
Pense quando você vai sacar dinheiro no caixa eletrônico:

- A Abstração (O que você vê): Uma tela com o botão "Sacar R$ 100", onde você digita a senha e pega o dinheiro.
- O Detalhe Oculto (Como funciona por trás): Consultas em banco de dados SQL, validação de criptografia de rede, comunicação via API com o BACEN, e engrenagens mecânicas contando cédulas.

Para quem está usando o sistema, os detalhes internos não importam — apenas a operação final.

## Como aplicamos no Java?
Usamos Classes Abstratas (abstract class) e Interfaces (interface) para definir esses contratos.

### 1. Criando o Contrato (Interface)
Aqui dizemos o que precisa ser feito, sem implementar o código ainda:

```java
public interface EnviadorEmail {
    // Definimos o O QUE deve ser feito
    void enviar(String destinatario, String mensagem);
}
```

### 2. Criando a Implementação Concreta
Aqui a classe implementa os detalhes do como:

```java
public class EnviadorEmailAWS implements EnviadorEmail {
    
    @Override
    public void enviar(String destinatario, String mensagem) {
        // AQUI FICA A COMPLEXIDADE: Conectar na AWS SES, autenticar credenciais, montar JSON...
        System.out.println("Email enviado via AWS SES para: " + destinatario);
    }
}
```

### 3. Usando a Abstração no Serviço
Quem chama o serviço só precisa conhecer a interface (a abstração), e não a implementação concreta da AWS:

```java
public class UsuarioService {
    
    // Injetamos a Abstração, não a classe concreta da AWS!
    private final EnviadorEmail enviadorEmail;

    public UsuarioService(EnviadorEmail enviadorEmail) {
        this.enviadorEmail = enviadorEmail;
    }

    public void cadastrarUsuario(String email) {
        // Regra de cadastro...
        
        // Chamada simples do método abstrato:
        enviadorEmail.enviar(email, "Bem-vindo ao sistema!");
    }
}
```
Por que a Abstração é tão poderosa?
Se amanhã a sua empresa decidir trocar a AWS pelo SendGrid ou Mailchimp:
- Você não precisa alterar uma linha sequer do seu UsuarioService.
- Você só cria uma nova classe EnviadorEmailSendGrid implements EnviadorEmail.
- O resto do sistema continua funcionando perfeitamente porque o contrato (EnviadorEmail) não mudou.

---