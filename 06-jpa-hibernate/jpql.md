# JPQL

## O que é?
O JPQL é a linguagem de consulta orientada a objetos do JPA. Em vez de fazer consultas diretamente nas tabelas e colunas do banco de dados (como no SQL), você faz consultas nas Entidades e atributos do seu código Java.

---

### 1. JPQL vs SQL Tradicional

|    Característica    |                                        JPQL                                         |                                      SQL                                      |
|:--------------------:|:-----------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------:|
|         Foco         |                        Entidades Java (Classes e Atributos)                         |                      Tabelas e Colunas do Banco de Dados                      |
|    Portabilidade     |            Alta (o Hibernate traduz para o dialeto do banco configurado)            |             Média/Baixa (depende da sintaxe específica do banco)              |
|      Mapeamento      |                    Retorna objetos gerenciados (`Entity`) ou DTOs                     |                         Retorna tabelas/linhas puras                          |
|   Case Sensitivity   | Sensível ao caso nos nomes de classes e atributos Java (`Usuario` $\neq$ `usuario`) |                   Geralmente insensível (depende do banco)                    |


### 2. Sintaxe Básica e Exemplos
Consulta Simples

```jpaql
-- SQL: SELECT * FROM tb_usuario WHERE status_usuario = 'ATIVO'
SELECT u FROM Usuario u WHERE u.status = 'ATIVO'
```
Parâmetros Nomeados e Posicionais
```jpaql
-- Parâmetro Nomeado (Recomendado)
SELECT u FROM Usuario u WHERE u.email = :email

-- Parâmetro Posicional
SELECT u FROM Usuario u WHERE u.email = ?1
```


### 3. A Peça-Chave: JOIN FETCH (Resolvendo o Problema do LAZY)
Como visto no artigo de `FetchType`, relacioamentos `LAZY` podem causar a exceção `LazyInitializationException` ou o problema de consultas N+1. O `JOIN FETCH` força o Hibernate a trazer os dados associados em uma única consulta SQL.

```jpaql
-- Sem FETCH: Traz o Cliente, mas ao acessar cliente.getPedidos() faz N novas queries
SELECT c FROM Cliente c

-- Com FETCH: Traz o Cliente E todos os Pedidos em 1 única query SQL
SELECT c FROM Cliente c JOIN FETCH c.pedidos WHERE c.id = :id
```
### 4. Projeções DTO (Alta Performance)
Para telas ou APIs que precisam apenas de alguns campos (sem carregar a entidade inteira na memória), você pode instanciar DTOs direto na JPQL:

```jpaql
SELECT new com.seuapp.dto.UsuarioResumoDTO(u.id, u.nome, u.email) 
FROM Usuario u 
WHERE u.ativo = true
```

### 5. Tipos de Operações (DML) e Paginação
Consultas de Agregação

```jpaql
SELECT c.categoria.nome, COUNT(c) 
FROM Produto p 
GROUP BY p.categoria.nome 
HAVING COUNT(p) > 5
```

### 6. Modificação/Exclusão em Lote (`UPDATE` / `DELETE`)
```jpaql
-- Atualização direta no banco
UPDATE Usuario u SET u.status = 'INATIVO' WHERE u.ultimoAcesso < :dataLimite

-- Deleção direta
DELETE FROM Log l WHERE l.dataCriacao < :dataCriacao
```

💡 Boas Práticas para a Documentação
Sempre prefira Parâmetros Nomeados (`:nome`) em vez de concatenação manual de Strings para evitar JPQL Injection.

Use `JOIN FETCH` para casos de uso específicos onde a tela/fluxo precisa ler coleções `LAZY`.

Para mutações em lote (`UPDATE`/`DELETE`), lembre-se que o JPQL altera o banco diretamente sem passar pelo Contexto de Persistência do JPA. Chame o método de atualizar o EntityManager ou limpe o cache se necessário.

---