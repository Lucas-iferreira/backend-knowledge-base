# Injeção de Dependencia - DI

## O que é?
A Injeção de Dependências (DI) é o padrão de projeto (Design Pattern) que coloca em prática o princípio de Inversão de Controle (IoC) especificamente no gerenciamento dos relacionamentos entre objetos.

Em vez de uma classe criar ou buscar ativamente as dependências das quais precisa para funcionar (usando new ou um Service Locator), essas dependências são "fornecidas" (injetadas) a ela por um agente externo (o IoC Container do Spring).

### 1. O Problema das Dependências Rígidas
Para entender a necessidade do DI, veja a evolução do código:

❌ Sem DI: Dependência Rígida
A classe PedidoService está "soldada" à implementação concreta SmtpEmailService. Se você precisar trocar para envio de e-mail via SendGrid, terá que alterar o código-fonte de PedidoService. Além disso, é impossível testar PedidoService sem disparar e-mails reais.

```java
public class PedidoService {
    // A própria classe decide QUEM é sua dependência
    private SmtpEmailService emailService = new SmtpEmailService();
}
```
✅ Com DI: Injeção por Contrato (Interface)
PedidoService agora depende apenas de uma abstração (EmailService). Quem decide qual implementação será usada e quem entrega a instância pronta é o container do Spring.
```java
@Service
public class PedidoService {

    // Dependência declarada como Interface e final (imutável)
    private final EmailService emailService;

    // O Spring INJETA a dependência no momento da instanciação
    public PedidoService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

### 2. As 3 Formas de Injeção de Dependências no Spring
Existem três maneiras de o Spring injetar um Bean em outro. A escolha correta impacta a qualidade e a segurança do código.

```text
[ Formas de Injeção no Spring ]
                      │
     ┌────────────────┼────────────────┐
     ▼                ▼                ▼
Construtor         Setter            Campo
(Recomendado)    (Opcional)      (Desencorajado)
```

|            Tipo de Injeção             |                                  Como Funciona                                   |                                                                                                            Pros / Contras                                                                                                            |
|:--------------------------------------:|:--------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|      Via Construtor (Recomendado)      |           Passa as dependências como parâmetros na criação do objeto.            |   `Prós`: Garante imutabilidade (`final`), impede objetos incompletos, facilita testes de unidade sem subir o Spring. `Contras`: Pode ficar legível mal se a classe tiver dependências demais (o que indica má responsabilidade).    |
|               Via Setter               |               Usa métodos `setXxx(...)` após instanciar o objeto.                |                        `Prós`: Útil para dependências estritamente opcionais ou reconfiguração em tempo de execução. `Contras`: Objeto pode ser usado antes do setter rodar, gerando `NullPointerException`.                         |
|        Via Campo (`@Autowired`)        |        Injeta direto na variável privada via reflexão Java (Reflection).         |                                    `Prós`: Código visualmente menor. `Contras`: Oculta dependências, impossibilita uso de `final`, dificulta testes puros com `new` e acopla a classe ao Spring.                                     |

### 3. Resolvendo Ambiguidade de Injeção (Multiplas Implementações)
Se você tiver uma interface com duas ou mais implementações salvas no contexto (ex: `SmtpEmailService` e `SendGridEmailService`), o Spring lançará uma exceção do tipo `NoUniqueBeanDefinitionException` ao tentar injetar `EmailService`.

Para resolver a ambiguidade, utilize uma das duas abordagens:

### A) `@Primary`
Define qual implementação deve ser a escolha "padrão" quando não houver indicação explícita.

```java
@Primary
@Component
public class SendGridEmailService implements EmailService { ... }
```
### B) `@Qualifier`
Define e especifica um "apelido" para a dependência exatamente no ponto de injeção:

```java
@Service
public class PedidoService {

    private final EmailService emailService;

    // Especifica exatamente qual Bean deseja injetar
    public PedidoService(@Qualifier("smtpEmailService") EmailService emailService) {
        this.emailService = emailService;
    }
}
```

### 💡 Dica de Produtividade para a Documentação
Uso do Lombok com Injeção por Construtor:
Para evitar escrever construtores manuais repetitivos, padronize no projeto o uso do Lombok via @`RequiredArgsConstructor`. Ele gera o construtor automaticamente para todos os campos marcados como `final`:
```java
@Service
@RequiredArgsConstructor // Gerador automático do construtor das variáveis final
public class PedidoService {
    private final EmailService emailService;
    private final PedidoRepository pedidoRepository;
}
```
---