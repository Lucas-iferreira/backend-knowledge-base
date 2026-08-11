# Spring Initializr

## O que é?
O Spring Initializr é a ferramenta oficial do ecossistema Spring criada para acelerar a inicialização de projetos Java e Kotlin. Ele gera a estrutura base do projeto (boilerplate) com todas as dependências configuradas no arquivo de build (`pom.xml` para Maven ou `build.gradle` para Gradle).

---

### 1. Modos de Uso
Existem três formas principais de utilizar o Spring Initializr:
- Interface Web (UI): Através do site start.spring.io (ideal para criar projetos rapidamente no navegador).
- Integrado na IDE: Diretamente dentro do IntelliJ IDEA, Eclipse ou VS Code.
- Linha de Comando (CLI ou cURL):
```properties
curl https://start.spring.io/starter.zip \
  -d dependencies=web,data-jpa,postgresql \
  -d name=meu-projeto \
  -o meu-projeto.zip
```
### 2. Principais Opções de Configuração
Ao criar um novo projeto, você precisará definir os seguintes metadados:

|       Opção       |                                           Descrição / Recomendação                                            |
|:-----------------:|:-------------------------------------------------------------------------------------------------------------:|
|      Project      |                           Maven ou Gradle (Defina o padrão da sua equipe/empresa).                            |
|     Language      |                                            Java, Kotlin ou Groovy.                                            |
|    Spring Boot    |        Escolha sempre a versão estável mais recente (evite versões com SNAPSHOT ou M/RC em produção).         |
| Project Metadata  |           Group (ex: `com.suaempresa`), Artifact (ex: `api-vendas`), Java (ex: Java 17 ou 21 LTS).            |
|     Packaging     | Jar (padrão recomendado para servidores modernos/Docker) ou War (para deploys em servidores Tomcat legados).  |


### 3. Dependências Essenciais para APIs REST com Banco de Dados
Para montar uma stack clássica de backend (como a que vimos de JPA, JPQL e Paginação), marque as seguintes dependências no Initializr:
- `Spring Web`: Cria APIs RESTful com Spring MVC e Tomcat embutido.
- `Spring Data JPA`: Inclui Hibernate, Spring Data, Specifications e suporte a repositórios.
- `Validation`: Adiciona validação de DTOs via anotações (`@NotNull, @Size, @Email`).
- Driver do Banco de Dados: Ex: `PostgreSQL Driver`, `MySQL Driver` ou `H2 Database` (para testes/desenvolvimento local).
- `Lombok`: Opcional. Biblioteca de anotações para reduzir código repetitivo (getters, setters, constructors).
- `Spring Boot DevTools`: Fornece live reload e reinicialização rápida da aplicação durante o desenvolvimento.


### 4. O que o Spring Initializr Gera?
A estrutura do projeto baixado vem assim:
```text
meu-projeto/
├── src/
│   ├── main/
│   │   ├── java/com/suaempresa/meuprojeto/
│   │   │   └── MeuProjetoApplication.java  <-- Ponto de entrada (@SpringBootApplication)
│   │   └── resources/
│   │       ├── application.properties    <-- Configurações (Banco de Dados, Porta, etc.)
│   │       ├── static/
│   │       └── templates/
│   └── test/                             <-- Estrutura pronta para testes de integração
├── .gitignore                            <-- Pré-configurado para Java, IDEs e arquivos de build
├── pom.xml / build.gradle                <-- Gerenciador de dependências e plugins
└── mvnw / gradlew                        <-- Wrappers para executar o projeto sem instalar o Maven/Gradle na máquina
```

💡 Boas Práticas para a Documentação
Use os Wrappers (`mvnw` / `./gradlew`): Oriente os desenvolvedores a sempre executarem os comandos usando o wrapper que vem no projeto gerado. Isso garante que todos na equipe usem exatamente a mesma versão da ferramenta de build, evitando o clássico "funciona na minha máquina".

Salve o Link de Inicialização: O site do Spring Initializr permite gerar URLs personalizadas com todas as dependências pré-selecionadas para a sua empresa. Você pode salvar esse link diretamente na documentação interna para padronizar novos microsserviços.

---