# Profiles

## O que é?
O recurso de Spring Profiles permite segregar a configuração da sua aplicação para diferentes ambientes (ex: desenvolvimento, testes, homologação e produção).

Com ele, você pode alterar propriedades do sistema, credenciais de banco de dados e até trocar implementações de Beans ativando uma simples chave, sem precisar alterar uma única linha de código.

---

### 1. Estrutura de Arquivos de Propriedades
O Spring Boot adota uma convenção simples para arquivos baseada no nome do perfil: `application-{profile}.properties` ou `application-{profile}.yml`.
```text
src/main/resources/
├── application.yml          <-- Configurações globais/padrão
├── application-dev.yml      <-- Perfil de Desenvolvimento (Banco local H2 / Postgres Docker)
├── application-test.yml     <-- Perfil de Testes (Banco em memória / Mocks)
└── application-prod.yml     <-- Perfil de Produção (Banco de dados AWS RDS / Logs restritos)
```
Exemplo prático (`application-dev.yml` vs `application-prod.yml`):

```properties
# application-dev.yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    username: sa
  jpa:
    show-sql: true # Exibe queries no console para debug
```

```properties
# application-prod.yml
spring:
  datasource:
    url: jdbc:postgresql://db.minhaempresa.com:5432/vendas
    username: ${DB_USER} # Variável de ambiente do servidor
    password: ${DB_PASS}
  jpa:
    show-sql: false # Oculta SQLs para manter os logs limpos e seguros
```


### 2. Como Ativar um Perfil
Existem várias maneiras de definir qual perfil estará ativo ao subir a aplicação:

A) No arquivo principal (`application.yml`)
```properties
spring:
  profiles:
    active: dev
```

B) Via Parâmetro de Linha de Comando (CLI / JAR)
Ideal para scripts de deploy e contêineres Docker:
```properties
java -jar meu-projeto.jar --spring.profiles.active=prod
```
C) Via Variável de Ambiente do Sistema Operacional
```properties
export SPRING_PROFILES_ACTIVE=prod
```

### 3. Alternando Beans no Código com `@Profile`
Você pode usar a anotação @Profile sobre classes `@Component`, `@Service` ou `@Bean` para garantir que um componente específico só seja instanciado se aquele perfil estiver ativo.

Exemplo: Alternando Serviços de Notificação por Ambiente
```java
public interface NotificacaoService {
    void enviar(String mensagem);
}

// Ativo APENAS em DEV e TEST
@Profile({"dev", "test"})
@Service
public class LogNotificacaoService implements NotificacaoService {
    @Override
    public void enviar(String mensagem) {
        System.out.println("[DEV/MOCK] Apenas printando log: " + mensagem);
    }
}

// Ativo APENAS em PROD
@Profile("prod")
@Service
public class AwsSnsNotificacaoService implements NotificacaoService {
    @Override
    public void enviar(String mensagem) {
        // Código real integrando com AWS SNS...
    }
}
```

## 4. Onde Usar Profiles na Prática?
- Bancos de Dados diferentes: H2/SQLite para testes, PostgreSQL local para dev, PostgreSQL em nuvem para prod.
- Integrações Externas: Mocks ou sandbox para dev, gateways de pagamento e APIs reais para prod.
- Nível de Log (Logging): Nível `DEBUG` ou `TRACE` em dev; nível `INFO` ou `ERROR` em prod.
- Carregamento de Dados Iniciais: Script SQL de carga (`import.sql` / `data.sql`) ativo apenas no perfil `dev`.


### 💡 Boas Práticas para a Documentação
- Nunca suba credenciais de produção no `application-prod.yml`: Use referências a Variáveis de Ambiente (ex: `${DB_PASSWORD}`) para ler senhas e chaves diretamente do ambiente do servidor, `Docker` ou Secret Managers (AWS Secrets Manager, Vault).
- Defina um perfil padrão para desenvolvedores: Mantenha `spring.profiles.active=dev` configurado no `application.yml` base para que novos desenvolvedores consigam clonar o repositório e rodar o projeto sem configurações extras.

---