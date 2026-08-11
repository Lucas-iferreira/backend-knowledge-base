# Paginação

## O que é?
Em aplicações de produção, retornar grandes volumes de dados de uma só vez gera problemas de memória no servidor e lentidão no consumo de APIs. A paginação resolve isso dividindo os resultados em páginas gerenciáveis no banco de dados através das cláusulas `LIMIT` e `OFFSET` do SQL.

---

### 1. As Peças Principais do Spring Data
O Spring abstrai a paginação utilizando duas interfaces principais:
- `Pageable` (A Requisição): Representa os parâmetros de entrada enviados pelo cliente. Contém o número da página (baseado em zero), o tamanho da página e a ordenação.
- `Page<T>` (A Resposta): Envelope que encapsula a lista de resultados da página atual + metadados (total de elementos, total de páginas, se é a última página, etc.).

###2. Como Aplicar no Repository
Basta adicionar o parâmetro `Pageable` no final da assinatura de qualquer método no seu repositório:

```java
@Repository
public interface ProdutoRepository extends JpaRepository<Produto, Long> {

    // Método derivado (Spring constrói a query paginada)
    Page<Produto> findByCategoriaNome(String nome, Pageable pageable);

    // Com JPQL
    @Query("SELECT p FROM Produto p WHERE p.preco >= :precoMinimo")
    Page<Produto> buscarPorPrecoMinimo(BigDecimal precoMinimo, Pageable pageable);
}
```

### 3. Recebendo no Controller e Passando no Service
O Spring MVC consegue converter automaticamente os parâmetros de query da requisição HTTP (`?page=0&size=10&sort=nome,asc`) para um objeto `Pageable`:

```java
@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    @Autowired
    private ProdutoService service;

    @GetMapping
    public ResponseEntity<Page<ProdutoDTO>> listarTodos(
            // @PageableDefault define os valores padrão caso o cliente não envie nada
            @PageableDefault(page = 0, size = 20, sort = "nome", direction = Sort.Direction.ASC) Pageable pageable) {

        Page<ProdutoDTO> pagina = service.buscarProdutos(pageable);
        return ResponseEntity.ok(pagina);
    }
}
```

### 4. `Page<T>` vs `Slice<T>` vs `List<T>`
Ao pedir dados paginados, você pode escolher o tipo de retorno no repositório de acordo com a necessidade:

|    Tipo de Retorno    |                   Executa Query de `COUNT`?                    |                           Metadados Retornados                           |                            Caso de Uso Ideal                             |
|:---------------------:|:--------------------------------------------------------------:|:------------------------------------------------------------------------:|:------------------------------------------------------------------------:|
|       `Page<T>`       |                  Sim (Faz 2 queries no banco)                  |      Total de páginas, total de elementos, número da página atual.       |    Tabelas paginadas tradicionais com números de página (1, 2, 3...).    |
|      `Slice<T>`       |            Não (Consulta apenas $N + 1$ registros)             |             Sabe apenas se existe uma próxima página ou não.             |          Infinite Scroll (gfeed de redes sociais, apps móbile).          |
|       `List<T>`       |                              Não                               |       Nenhum metadado. Retorna apenas os registros daquela página.       | Lógicas internas de backend sem necessidade de metadados para o cliente. |

### 5. O Estrangulamento da Query `COUNT` (Performance)
Quando você usa `Page<T>`, o Spring executa duas consultas SQL:
- `SELECT ... LIMIT 20 OFFSET 0`(Para pegar os dados da página)
- `SELECT COUNT(...)` (Para calcular o total de registros)

Em tabelas gigantescas (milhões de linhas), o `COUNT(*)` com `JOIN`s complexos pode tornar a consulta muito lenta.

Como Resolver:
Opção A: Query de Count Personalizada (JPQL)
Se sua query principal tem vários JOINs desnecessários para a contagem, defina um countQuery otimizado:

```java
@Query(
    value = "SELECT p FROM Produto p JOIN FETCH p.categoria WHERE p.ativo = true",
    countQuery = "SELECT COUNT(p) FROM Produto p WHERE p.ativo = true" // Query mais leve sem o FETCH
)
Page<Produto> buscarAtivosOtimizado(Pageable pageable);
```

💡 Boas Práticas para a Documentação
- Página Base Zero: Lembre-se que no Spring Data a primeira página é `0` (`page=0`).
- Limite o `size` Máximo: Sempre defina um limite máximo para o parâmetro `size` no backend ou DTO para evitar que um cliente mal-intencionado envie `?size=1000000` e derrube a aplicação (Out of Memory).
- Mapeie `Page<Entity>` para `Page<DTO>`: Use o método `.map()` do próprio `Page` para transformar entidades em DTOs sem perder os metadados de paginação:

```java
Page<ProdutoDTO> dtoPage = entityPage.map(ProdutoDTO::new);
```
---