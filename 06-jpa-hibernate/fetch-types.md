# Fetch Types (stratégias de Carregamento)

## O que é?
O FetchType define quando o Hibernate/JPA deve carregar do banco de dados os relacionamentos (entidades associadas) de uma classe.

Existem duas estratégias: LAZY (Sob Demanda) e EAGER (Ansioso/Imediato).

---

### 1. Tabela Comparativa Rápidas
| Característica  |                                 `FetchType.LAZY`                                 |                               `FetchType.EAGER`                               |
|:---------------:|:--------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------:|
|  Comportamento  |     Carrega os dados da associação somente quando forem acessados no código.     | Carrega os dados da associação imediatamente junto com a entidade principal.  |
|    Mecanismo    |    Cria um Proxy (substituto) do objeto. A query só roda ao chamar o `get()`.    | Faz um `JOIN` na query inicial ou executa queries secundárias imediatamente.  |
| Uso Recomendado |         Padrão de mercado para quase tudo. Ideal para coleções e listas.         |   Apenas quando a entidade associada sempre é usada junto com a principal.    |
|    Vantagens    |       Melhor performance, menor consumo de memória e consultas mais leves.       |      Evita tratar sessões abertas depois; os dados já estão na memória.       |
|     Riscos      |            `LazyInitializationException` e problema das queries N+1.             |  Consultas lentas (carrega "o banco inteiro") e risco de Produto Cartesiano.  |


### 2. Comportamentos Padrão do JPA
Por padrão, as anotações do JPA já vêm com um FetchType pré-configurado se você não explicitar:

```java
// Padrão: EAGER (Cuidado!)
@ManyToOne 
@OneToOne

// Padrão: LAZY (Recomendado)
@OneToMany 
@ManyToMany
```
⚠️ Boa Prática: Sobrescreva o padrão de @ManyToOne e @OneToOne para LAZY explicitamente:
```java
@ManyToOne(fetch = FetchType.LAZY)
private Categoria categoria;
```

---


### 3. Problemas Comuns e Como Evitar

- `LazyInitializationException` (com LAZY): Ocorre quando você tenta acessar um atributo `LAZY` (ex: `cliente.getPedidos()`) após a sessão/transação do banco ter sido fechada.
-  - Solução: Traga os dados na consulta inicial usando `JOIN FETCH` no JPQL ou `@EntityGraph`.
- Problema das Queries N+1 (com LAZY e EAGER): Buscar $N$ registros no banco gera 1 query para a lista principal + $N$ queries individuais para carregar os relacionamentos.
-  - Solução: Use `JOIN FETCH` nas suas consultas do repositório.
- Produto Cartesiano (com EAGER): Ao usar `EAGER` em múltiplas coleções (`@OneToMany`), o Hibernate gera combinações multiplicar de linhas, derrubando a performance.

💡 Regra de Ouro para a Documentação
"Trate tudo como LAZY por padrão. Quando precisar de dados associados para um caso de uso específico, faça o carregamento ansioso via Query (JOIN FETCH ou EntityGraph) no seu repositório."


---