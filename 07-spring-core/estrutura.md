# Estrutura do projeto

## Estrutura Recomendada
Uma boa estrutura de pastas segue os princípios da Arquitetura em Camadas (Layered Architecture) ou Clean Architecture, mantendo a Separação de Responsabilidades (SoC) bem clara.

Abaixo está o padrão recomendado para APIs RESTful utilizando Spring Data JPA, DTOs e Specifications.

### 1. Árvore de Diretórios (Visão Geral)

```text
src/
├── main/
│   ├── java/
│   │   └── com/empresa/projeto/
│   │       ├── MeuProjetoApplication.java        <-- Classe principal
│   │       │
│   │       ├── config/                           <-- Configurações gerais (Security, CORS, OpenAPI/Swagger)
│   │       ├── controller/                       <-- Camada de Exposição (Endpoints REST e DTOs de Entrada/Saída)
│   │       │   └── dto/
│   │       │       ├── request/
│   │       │       └── response/
│   │       │
│   │       ├── service/                          <-- Camada de Negócio e Regras
│   │       │   └── exception/                    <-- Exceções customizadas da camada de negócio
│   │       │
│   │       ├── repository/                       <-- Camada de Acesso a Dados
│   │       │   └── spec/                         <-- Specifications (Filtros dinâmicos / Criteria API)
│   │       │
│   │       ├── model/ (ou domain/entity)         <-- Entidades JPA do banco de dados
│   │       └── infra/                            <-- Tratamento global de erros e integrações externas
│   │           └── exception/                    <-- @ControllerAdvice / GlobalExceptionHandler
│   │
│   └── resources/
│       ├── application.properties (ou .yml)      <-- Configurações da aplicação (Banco, Porta, Log)
│       ├── application-dev.yml                   <-- Profile de desenvolvimento
│       ├── application-prod.yml                  <-- Profile de produção
│       └── db/migration/                         <-- Scripts Flyway / Liquibase (se utilizado)
```


### 2. Responsabilidade de Cada Camada

|   Camada / Pasta    |                                       Responsabilidade                                       |                                            O que DEVE ter                                             |                                    O que NÃO DEVE ter                                    |
|:-------------------:|:--------------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------:|
|    `controller`     | Receber requisições HTTP, validar dados de entrada e retornar respostas (`ResponseEntity`).  |               Injeção de `Service`, anotações `@GetMapping`, `@PostMapping`, `@Valid`.                |               Regras de negócio, consultas diretas ao repositório SQL/JPA.               |
|        `dto`        |                Contratos de entrada (`Request`) e saída (`Response`) da API.                 |             Atributos simples, anotações do `@BeanValidation` (`@NotNull`, `@NotBlank`).              |                  Anotações JPA (`@Entity`, `@Table`), lógica de banco.                   |
|      `service`      | Concentrar toda a regra de negócio da aplicação e orquestrar transações (`@Transactional`).  |       Validações de negócio, chamadas ao `Repository`, mapeamento DTO $\leftrightarrow$ Entity.       | Dependência do Spring MVC (não deve manipular `HttpServletRequest` ou `ResponseEntity`). |
|    `repository`     |                           Comunicação direta com o Banco de Dados.                           |    Interfaces estendendo `JpaRepository` e `JpaSpecificationExecutor`, queries `@Query` (`JPQL`).     |                        Lógica de negócio ou regras de validação.                         |
|       `spec`        |              Classes utilitárias com predicados da Criteria API/Specification.               |           Métodos estáticos que retornam `Specification<Entidade>` para filtros dinâmicos.            |                         Lógicas de atualização/escrita no banco.                         |
|  `model / domain`   |                     Mapeamento das tabelas relacionais em objetos Java.                      |        Anotações JPA (`@Entity`, `@Table`, `@Id`, `@ManyToOne`), atributos e relacionamentos.         |                       DTOs ou lógicas de apresentação/controllers.                       |
|  `infra/exception`  |                           Captura global de exceções da aplicação.                           | @RestControllerAdvice, tratamento de `MethodArgumentNotValidException`, `ResourceNotFoundException`.  |                    Regras de negócio específicas de um único domínio.                    |
---

### 3. Fluxo de Dados (Data Flow)
O ciclo de vida de uma requisição passa pelas camadas na seguinte ordem
```text
[ Cliente / Frontend ]
          │ (HTTP Request com DTO)
          ▼
    [ Controller ] ────────┐ Validar entrada (@Valid)
          │                └ Convert DTO -> Parameters
          ▼
     [ Service ] ──────────┐ Executa regras de negócio & Transações (@Transactional)
          │                └ Monta Specifications / JPQL
          ▼
    [ Repository ] ────────┐ Executa consultas no Banco (SQL)
          │                └ Aplica Paginação / Fetch Types (LAZY/EAGER)
          ▼
   [ Database (SQL) ]
```

 💡 Boas Práticas para a Documentação
- NUNCA exponha Entidades JPA nos Controllers: Sempre mapeie para DTOs de resposta (`ResponseDTO`). Expor a entidade
diretamente pode gerar loops de serialização JSON (devido a relacionamentos bidirecionais), vazar campos sensíveis (como senhas/hashes) e forçar o carregamento de atributos `LAZY` fora da transação.
- Organização por Pacotes (Package by Feature vs Package by Layer):
- - A estrutura acima é Package by Layer (ideal para projetos pequenos/médios).
- - Se o projeto for muito grande, considere Package by Feature (`com.empresa.projeto.usuario`, `com.empresa.projeto.produto`), onde cada pasta contém seu próprio Controller, Service, Repository e DTOs isolados. 

---