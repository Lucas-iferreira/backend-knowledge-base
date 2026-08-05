# Entidades

## O que é?
Uma Entidade é um POJO Java que representa uma tabela no banco de dados. Cada instância é uma linha e cada atributo é uma coluna.

---

### 1. Requisitos Obrigatórios
- Anotação `@Entity`.
- Identificador @`Id` (Chave Primária).
- Construtor sem argumentos (`public` ou `protected`).
- Classe não pode ser `final`.

### 2. Mapeamentos Principais
- Entity: Declara a classe como entidade.
- @Table(name = "tb_nome"): Mapeia o nome exato da tabela.
- @Id + @GeneratedValue(strategy = ...): Define a PK.
- Estratégias: IDENTITY (auto-incremento/SERIAL), SEQUENCE, UUID.
- @Column(name="...", nullable=false, length=50, unique=true): Personaliza a coluna.

### 3. Tipos Especiais
- num: @Enumerated(EnumType.STRING) (Sempre em String para evitar problemas ao alterar a ordem do Enum).
- Datas (java.time): LocalDateTime (TIMESTAMP), LocalDate (DATE).
- Ignorar Campo: @Transient (Campo existe só no Java, não vira coluna

### 4. Reuso de Estrutura
- MappedSuperclass: Herança Java sem criar tabela pai (útil para atributos comuns como id e dataCriacao).
- @Embeddable / @Embedded: Agrupa atributos reutilizáveis sem ID (ex: Endereco) e embute as colunas na tabela principal.
 

### 5. Exemplo Mínimo

```sql
@Entity
@Table(name = "tb_produtos")
public class Produto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String nome;

    @Enumerated(EnumType.STRING)
    private StatusProduto status;

    public Produto() {} // Construtor obrigatório
}
```
---