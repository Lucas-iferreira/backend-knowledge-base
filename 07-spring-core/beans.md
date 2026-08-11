# Beans

## O que são Spring Beans?
Em termos simples, um Spring Bean é qualquer objeto Java que é instanciado, montado, configurado e totalmente gerenciado pelo IoC Container do Spring (via `ApplicationContext`).

Diferente de um objeto comum do Java (criado manualmente com `new`), o ciclo de vida e a injeção do Bean são controlados 100% pelo framework.

### 1. Objeto Java Comum (POJO) vs. Spring Bean
```text
[ Objeto Comum (POJO) ] ──────> Criado com 'new' por você ───> Você gerencia a memória e as dependências.
[ Spring Bean ] ──────────────> Criado pelo Spring ───────────> O Spring injeta dependências e gerencia o ciclo de vida.
```

### 2. As Duas Formas de Declarar um Bean
Existem dois caminhos principais para registrar uma classe como um Bean no Spring:

A) Via Anotações de Componente (Stereotype Annotations)
Usado para classes do seu próprio projeto. O Spring escaneia o projeto (`@ComponentScan`) e cria os Beans automaticamente.

|     Anotação      |                                    Camada / Propósito                                    |
|:-----------------:|:----------------------------------------------------------------------------------------:|
|   `@Component`    |             Anotação genérica para qualquer componente mantido pelo Spring.              |
|    `@Service`     |                    Indica componentes da camada de Regras de Negócio.                    |
|   `@Repository`   | Indica componentes da camada de Acesso a Dados (adiciona tradução de exceções do banco). |
| `@RestController` |        Indica componentes da camada Web / API REST (serializa retornos em JSON).         |
| `@Configuration`  |                Classe especial que agrupa métodos declaradores de Beans.                 |


#### B) Via Métodos com `@Bean` (em classes `@Configuration`)
Usado quando você precisa configurar classes de bibliotecas de terceiros (cujo código-fonte você não pode alterar para colocar `@Component`) ou quando a criação do objeto exige uma lógica manual de montagem.
```java
@Configuration
public class JacksonConfig {

    // Registra o ObjectMapper da biblioteca Jackson como um Bean no Spring
    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());
        return mapper;
    }
}
```

### 3. Escopo dos Beans (Bean Scopes)
O escopo define como e quando o Spring cria novas instâncias de um Bean.

|          Escopo          |                                         Descrição                                          |                          Caso de Uso                           |
|:------------------------:|:------------------------------------------------------------------------------------------:|:--------------------------------------------------------------:|
|   `singleton`(Padrão)    |              Anotação genérica para qualquer componente mantido pelo Spring.               |   Quase tudo: `@Service`, `@Repository`, `@RestController`.    |
|       `prototype`        |                     Indica componentes da camada de Regras de Negócio.                     |  Objetos com estado mutável que não podem ser compartilhados.  |
|      `request`(Web)      |  Indica componentes da camada de Acesso a Dados (adiciona tradução de exceções do banco).  |             Dados específicos da requisição atual.             |
|      `session`(Web)      |         Indica componentes da camada Web / API REST (serializa retornos em JSON).          |    Carrinho de compras, dados de usuário logado na sessão.     |

⚠️ Atenção sobre Singletons: Como os Beans `singleton` são compartilhados por múltiplas threads simultâneas, eles devem ser stateless (não devem armazenar dados de usuários em variáveis de instância da classe).

### 4. Ciclo de Vida de um Bean (Lifecycle)
Quando a aplicação Spring inicializa, cada Bean passa por um ciclo de vida estruturado:
```text
Instanciação   ──>   Injeção de       ──>  Inicialização  ──>   Bean Pronto   ──>  Destruição
 (Construtor)         Dependências           (@PostConstruct)    para Uso           (@PreDestroy)
```
Hooks de Ciclo de Vida Úteis:
- `@PostConstruct`: Executa um método logo após o Spring ter injetado todas as dependências no Bean.
- `@PreDestroy`: Executa um método pouco antes do Spring destruir o Bean e encerrar a aplicação (ideal para fechar conexões ou liberar recursos).

```java
@Component
public class CacheManager {

    @PostConstruct
    public void init() {
        // Roda automaticamente após as dependências serem injetadas
        System.out.println("Carregando cache inicial...");
    }

    @PreDestroy
    public void cleanup() {
        // Roda antes da aplicação desligar
        System.out.println("Limpando recursos do cache...");
    }
}
```
💡 Regra de Ouro para a Documentação
"A nobreza do Spring está no reuso de Beans Singletons e Stateless. Deixe o gerenciamento do ciclo de vida, instanciação e destruição com o Spring e foque apenas na regra de negócio."

---