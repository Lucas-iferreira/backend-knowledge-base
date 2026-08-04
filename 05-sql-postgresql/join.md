# JOIN


### 1. Guia Rápido de Seleção

|            TIPO             |                SINTAXE                 |                                                            O QUE FAZ/ QUANDO USAR?                                                             |
|:---------------------------:|:--------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------:|
|        `INNER JOIN`         |    `FROM A JOIN B ON A.id = B.a_id`    |                                      Intersecção: Traz apenas registros que existem em ambas as tabelas.                                       |
|         `LEFT JOIN`         | `FROM A LEFT JOIN B ON A.id = B.a_id`  |                       Tudo da esquerda: Traz todos da tabela A + dados da B se existirem (se não, preenche com `NULL`).                        |
|        `RIGHT JOIN`         | `FROM A RIGHT JOIN B ON A.id = B.a_id` | Tudo da direita: Inverso do `LEFT`. Raramente usado na prática (prefira inverter a ordem das tabelas no `LEFT JOIN` para facilitar a leitura). |
|         `FULL JOIN`         | `FROM A FULL JOIN B ON A.id = B.a_id`  |               Tudo de ambas: Traz todos os registros de A e de B, ligando onde houver correspondência e usando `NULL` no resto.                |
|        `CROSS JOIN`         |         `FROM A CROSS JOIN B`          |                               Produto cartesiano: Multiplica todas as linhas de A por todas de B. Não usa `ON`.                                |

---

### 2. Exemplos Práticos
A. `INNER JOIN` (Apenas Clientes com Pedidos)

```sql
SELECT c.nome, p.id AS pedido_id, p.valor
FROM clientes c
INNER JOIN pedidos p ON c.id = p.cliente_id;
```

B. `LEFT JOIN` (Todos os Clientes, Tendo Pedidos ou Não)
Excelente para encontrar "órfãos" (ex: clientes que nunca compraram).

```sql
SELECT c.nome, p.id AS pedido_id
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
WHERE p.id IS NULL; -- Filtra apenas clientes SEM pedidos
```
C. `FULL OUTER JOIN` (Gera Matriz Completa)
```sql
SELECT c.nome, p.id AS pedido_id
FROM clientes c
FULL JOIN pedidos p ON c.id = p.cliente_id;
```
D. `CROSS JOIN` (Combinações Múltiplas)
Útil para gerar tabelas de combinação (ex: produtos × tamanhos).

```sql
SELECT p.nome AS produto, t.tamanho
FROM produtos p
CROSS JOIN tamanhos t;
```

### 3. Atalho de Sintaxe (`USING`)
Quando a chave estrangeira e a chave primária têm exatamente o mesmo nome nas duas tabelas, você pode trocar o `ON A.id = B.id` por `USING (id)`:
```sql
-- Sintaxe tradicional
SELECT * 
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.cliente_id;

-- Atalho limpo usando USING
SELECT * 
FROM pedidos p
JOIN clientes c USING (cliente_id);
```
### 4. `Self JOIN` (Tabela Comigo Mesma)
Usado em estruturas hierárquicas (ex: tabela de funcionários com `gerente_id` apontando para a própria tabela de funcionários).
```sql
SELECT 
    f.nome AS funcionario,
    g.nome AS gerente
FROM funcionarios f
LEFT JOIN funcionarios g ON f.gerente_id = g.id;
```

### 5. Dica Prática de Performance (Postgres)
Verifique Índices nas FKs: O PostgreSQL não cria índices em Foreign Keys automaticamente. Para garantir que seus `JOIN`s fiquem rápidos em tabelas grandes, crie índices nas colunas usadas na condição `ON`:
```sql
CREATE INDEX idx_pedidos_cliente_id ON pedidos(cliente_id);
```
---