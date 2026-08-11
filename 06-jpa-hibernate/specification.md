# Specifications


## O que é?

O padrão Specification (baseado na Criteria API) é a solução ideal quando você precisa de consultas dinâmicas — como telas de busca com múltiplos filtros opcionais (ex: "buscar por nome E/OU data E/OU status").

Diferente do JPQL (que é uma String fixa), a Specification permite construir queries programaticamente em código Java, com type safety (segurança de tipos em tempo de compilação).

---

### 1. JPQL vs Specifications (Criteria API)

|       Característica        |                        JPQL                         |               Specifications(Criteria API)                |
|:---------------------------:|:---------------------------------------------------:|:---------------------------------------------------------:|
|           Estilo            |                  String (Textual)                   |           Código Java (Programático / Fluente)            |
|          Uso ideal          |      Consultas fixas, conhecidas e otimizadas       |     Filtros dinâmicos com múltiplos campos opcionais      |
|      Segurança de Tipo      |    Erros de sintaxe/campo só aparecem em runtime    |      Erros de campo aparecem em tempo de compilação       |
| Manutenibilidade em Filtros |   Ruim (vários `if`/`else` concatenando Strings SQL)    |  Excelente (composição de pequenos blocos reutilizáveis)  |



### 2. Como Configurar
Passo 1: Estender `JpaSpecificationExecutor` no Repositório
```java
@Repository
public interface UsuarioRepository extends JpaRepository<Usuario, Long>, JpaSpecificationExecutor<Usuario> {
    // O JpaSpecificationExecutor adiciona métodos como:
    // findAll(Specification<T> spec)
    // findAll(Specification<T> spec, Pageable pageable)
}
```

### 3. Criando Specifications
Uma `Specification<T>` é uma interface funcional que implementa a Criteria API do JPA sob o capô:
```java
public class UsuarioSpecs {

    // Filtro por Nome (LIKE %nome%)
    public static Specification<Usuario> comNome(String nome) {
        return (root, query, builder) -> {
            if (nome == null || nome.isBlank()) return null; // Ignora o filtro se nulo
            return builder.like(builder.lower(root.get("nome")), "%" + nome.toLowerCase() + "%");
        };
    }

    // Filtro por Status
    public static Specification<Usuario> comStatus(StatusUsuario status) {
        return (root, query, builder) -> 
            status == null ? null : builder.equal(root.get("status"), status);
    }

    // Otimização: JOIN FETCH programático para evitar N+1
    public static Specification<Usuario> comPerfil() {
        return (root, query, builder) -> {
            if (Long.class.equals(query.getResultType())) return null; // Evita fetch em count()
            root.fetch("perfis", JoinType.LEFT);
            return null;
        };
    }
}
```

### 4. Usando na Camada de Service / Controller
Você pode combinar specifications usando métodos encadeados como `and()`, `or()` e `not()`:

```java
@Service
public class UsuarioService {

    @Autowired
    private UsuarioRepository repository;

    public Page<Usuario> buscarComFiltros(UsuarioFiltroDTO filtro, Pageable pageable) {
        
        // Combina os filtros dinamicamente
        Specification<Usuario> spec = Specification
                .where(UsuarioSpecs.comPerfil())
                .and(UsuarioSpecs.comNome(filtro.getNome()))
                .and(UsuarioSpecs.comStatus(filtro.getStatus()));

        return repository.findAll(spec, pageable);
    }
}
```

### 5. Elementos-Chave da Criteria API
Ao escrever uma Specification, você interage com três componentes principais:
- `Root<T>`: Representa a Entidade principal da consulta (equivale ao `FROM Usuario u`). Usado para pegar atributos (`root.get("nome")`) ou fazer joins (`root.join("pedidos")`).
- `CriteriaQuery<?>`: Representa a estrutura da query em si (`SELECT`, `GROUP BY`, `ORDER BY`).
- `CriteriaBuilder`: Fábrica de condições e predicados (`equal`, `like`, `between`, `greaterThan`, `and`, `or`).

💡 Boas Práticas para a Documentação
- Trate os Nulos dentro das Spec Specs: Sempre verifique se o parâmetro do filtro é `null` e retorne `null` na Specification. O Spring Data ignora automaticamente predicados nulos no `.and()`.
- Atenção com Paginação e Fetch (`root.fetch`): Ao usar `root.fetch` para evitar N+1 em Specifications, valide se `query.getResultType()` não é um `Long.class` (pois o Spring executa uma query de `COUNT` para paginar, e `FETCH` em `COUNT` gera exceção).
- Reutilização: Crie classes de Specifications estáticas e puras para modularizar e testar os filtros de forma isolada.

---