# Relacionamentos JPA

## O que é?
Os Relacionamentos no JPA servem para mapear as conexões de Chave Estrangeira (FK) entre tabelas do banco de dados relacional para a estrutura de objetos no Java.

---

## Conectores de Relacionamento
### 1. `@ManyToOne` (Muitos para Um)
 É o relacionamento mais comum (ex: Vários Pedidos pertencem a Um Cliente).
- Regra: Fica do lado da tabela que possui a Chave Estrangeira (FK).
- Anotação auxiliar: `@JoinColumn(name = "nome_da_fk")` define o nome da coluna de FK no banco.

```sql
@Entity
public class Pedido {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "cliente_id", nullable = false)
    private Cliente cliente;
}
```
### 2. @OneToMany (Um para Muitos)
Representa o lado oposto do `@ManyToOne` (ex: Um Cliente possui Vários Pedidos).
- Unidirecional: Cria uma tabela intermediária desnecessária se usado sozinho (evite).
- Bidirecional: Usa o atributo `mappedBy` para indicar que o relacionamento é gerenciado pelo outro lado.
 

```sql
@Entity
public class Cliente {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // mappedBy aponta para o NOME DO ATRIBUTO "cliente" dentro da classe Pedido
    @OneToMany(mappedBy = "cliente")
    private List<Pedido> pedidos = new ArrayList<>();
}
```

### 3. `@OneToOne` (Um para Um)
Associa um registro a exatamente outro registro (ex: Usuário e seu Perfil).

```sql
@Entity
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne
    @JoinColumn(name = "perfil_id", unique = true) // FK com restrição UNIQUE
    private Perfil perfil;
}
```

### 4. `@ManyToMany` (Muitos para Muitos)
Associa múltiplos registros de ambos os lados (ex: Produtos e Categorias).
- Cria automaticamente uma tabela intermediária (junção) no banco usando `@JoinTable`.

```sql
@Entity
public class Produto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToMany
    @JoinTable(
        name = "tb_produto_categoria", // Nome da tabela intermediária
        joinColumns = @JoinColumn(name = "produto_id"),
        inverseJoinColumns = @JoinColumn(name = "categoria_id")
    )
    private Set<Categoria> categorias = new HashSet<>();
}
```
## Conceitos Fundamentais
Lado Proprietário vs. Lado Inverso (Mapeado)
- Lado Proprietário (Dono da FK): É a entidade que tem a anotação `@JoinColumn`. É quem realmente atualiza a Chave Estrangeira no banco de dados.
- Lado Inverso (Espelho): É a entidade que usa o parâmetro `mappedBy`. Serve apenas para leitura e navegação em memória no Java.
⚠️ Atenção: Em relacionamentos bidirecionais, ao associar dois objetos em Java, garanta que atualizou os dois lados (ou crie um método auxiliar do tipo `addPedido(Pedido pedido)`).

|    RELACIONAMENTOS     |      Anotação de Conexão      |    Mapeamento Bidirecional    |           Onde fica a FK?            |
|:----------------------:|:-----------------------------:|:-----------------------------:|:------------------------------------:|
|     Muitos para Um     |          `@ManyToOne`           | `@OneToMany(mappedBy = "...")`  |           Na tabela atual            |
|     Um para Muitos     |          `@OneToMany`           |          `@ManyToOne`           |           Na outra tabela            |
|       Um para Um       |           `@OneToOne`           |  `@OneToOne(mappedBy = "...")`  |   Na tabela que tem o `@JoinColumn`    |
|   Muitos para Muitos   |          `@ManyToMany`          | `@ManyToMany(mappedBy = "...")` | Na Tabela Intermediária (`@JoinTable`) |


---