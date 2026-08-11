# Configurações

## Gerenciamento de Configurações no Spring Boot
O Spring Boot fornece um modelo altamente flexível para gerenciar configurações externalizadas. Ele permite que o comportamento da aplicação (conexões com banco, portas, timeouts, chaves de API) seja alterado sem a necessidade de recompilar o código.


### 1. Formatos de Arquivo: `.properties` vs `.yml` (YAML)
O Spring aceita dois formatos de arquivo de configuração localizados na pasta `src/main/resources`:

- `application.properties` (Chave-Valor tradicional):
```properties
server.port=8080
spring.datasource.url=jdbc:postgresql://localhost:5432/db
spring.datasource.username=postgres
```
- application.yml (Estruturado em árvore - Recomendado):
```properties
server:
port: 8080

spring:
datasource:
url: jdbc:postgresql://localhost:5432/db
username: postgres
```

### 2. Formas de Consumir Configurações no Código
Existem duas formas principais de ler propriedades de configuração dentro das suas classes Java:

A) Via Anotação `@Value` (Injeção Pontual)
Ideal para ler valores isolados ou simples diretamente em variáveis.

```java
@Component
public class EmailService {

    // Lê a propriedade 'app.email.remetente' com um valor default caso não exista
    @Value("${app.email.remetente:noreply@empresa.com}")
    private String remetente;

    @Value("${app.email.timeout-segundos:30}")
    private int timeout;
}
```

B) Via `@ConfigurationProperties` (Mapeamento Tipado - Boa Prática)
Ideal para agrupar configurações relacionadas em um objeto tipado com validação (Type-Safe Configuration).

- Defina as propriedades no `application.yml`:
```properties
app:
pagamento:
gateway-url: https://api.pagamento.com
chaves:
publica: pub_12345
privada: priv_67890
max-tentativas: 3
```
- Crie a classe Java de mapeamento:
```java
@Configuration
@ConfigurationProperties(prefix = "app.pagamento")
@Getter
@Setter
public class PagamentoProperties {

    private String gatewayUrl;
    private Chaves chaves = new Chaves();
    private int maxTentativas;

    @Getter
    @Setter
    public static class Chaves {
        private String publica;
        private String privada;
    }
}
```
- Ordem de Precedência das Configurações (Hierarchy)
O Spring Boot segue uma hierarquia estrita para resolver propriedades. Se a mesma propriedade for definida em múltiplos lugares, o nível mais alto sobrescreve o mais baixo:
```text
[ MAIOR PRECEDÊNCIA ] (Sobrescreve tudo)
   1. Argumentos de Linha de Comando (ex: --server.port=9090)
   2. Variáveis de Ambiente do S.O. (ex: SERVER_PORT=9090)
   3. Arquivos específicos do perfil (application-prod.yml)
   4. Arquivo padrão de configurações (application.yml)
 [ MENOR PRECEDÊNCIA ]
```

## 4. Mapeamento de Variáveis de Ambiente (Relaxed Binding)
O Spring realiza a conversão automática de nomes entre variáveis de ambiente do sistema operacional e o `application.yml`:

| Propriedade no application.yml | Variável de Ambiente Equivalente no S.O. / Docker |
|:------------------------------:|:-------------------------------------------------:|
|    `spring.datasource.url`     |              `SPRING_DATASOURCE_URL`              |
|  `app.pagamento.gateway-url`   |            `APP_PAGAMENTO_GATEWAY_URL`            |
|         `server.port`          |                   `SERVER_PORT`                   |

💡 Regras de Ouro para a Documentação
- Mantenha os Segredos Fora do Git: NUNCA faça commit de senhas, chaves privadas ou tokens em arquivos `application.yml`. Use variáveis de ambiente `${VARIAVEL}` no arquivo para que o valor seja injetado no ambiente de execução/Docker.
- Prefira `@ConfigurationProperties` para grupos de propriedades: Mapear propriedades relacionadas em classes fortalece a validação dos dados na inicialização e facilita testes.

---