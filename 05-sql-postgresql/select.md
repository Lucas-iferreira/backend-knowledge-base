# SELECT

## 1. Estrutura Básica e Ordem de Execução
No código escrevemos em uma ordem, mas o PostgreSQL executa em outra. Entender isso evita erros de sintaxe e lógica em consultas complexas.

```sql
-- Ordem de Escrita                    -- Ordem de Execução Interna
SELECT coluna1, COUNT(*)              -- 4. SELECT (projeta colunas e executa funções)
FROM tabela                           -- 1. FROM (carrega as tabelas/joins)
WHERE condicao                        -- 2. WHERE (filtra linhas individuais)
GROUP BY coluna1                      -- 3. GROUP BY (agrupa os dados)
HAVING COUNT(*) > 1                   -- 5. HAVING (filtra os grupos gerados)
ORDER BY coluna1 DESC                 -- 6. ORDER BY (ordena o resultado final)
LIMIT 10 OFFSET 0;                    -- 7. LIMIT / OFFSET (pagina o resultado)
```
⚠️ Ponto de Atenção: Você não pode usar um alias do `SELECT` dentro do `WHERE` porque o `WHERE` roda antes do `SELECT`.

---
### 2. Tipos de JOINs (Resumo Visual)


|       Tipo        |                                     O que faz?                                      |
|:-----------------:|:-----------------------------------------------------------------------------------:|
|   `INNER JOIN`    |                  Apenas registros que existem em ambas as tabelas.                  |
|    `LEFT JOIN`    | Todos da esquerda + correspondentes da direita (preenche com `NULL` se não houver). |
|   `RIGHT JOIN`    |   Todos da direita + correspondentes da esquerda (raro uso, prefira `LEFT JOIN`).   |
| `FULL OUTER JOIN` |  Todos os registros de ambas as tabelas (com `NULL` onde não há correspondência).   |
|   `CROSS JOIN`    |           Produto cartesiano (múltiplas combinações de todos com todos).            |


### 3. Cláusulas de Filtro Rápidas (`WHERE`)
- Valores Múltiplos: `WHERE status IN ('PENDENTE', 'EM_PROCESSAMENTO')`
- Busca por Padrão (Texto):
- - `LIKE 'Luc%'` (case-sensitive)
- - `ILIKE 'luc%'` (Postgres-specific: case-insensitive)
- Tratamento de Nulos: `WHERE deletado_em IS NULL` (nunca use = NULL)
- Intervalos: `WHERE criado_em BETWEEN '2026-01-01' AND '2026-12-31'`

### 4. Recursos Úteis do Postgres para o `SELECT`
`DISTINCT ON` (Específico do Postgres)
Retorna a primeira linha de cada grupo com base em uma ordenação específica.

```sql
-- Retorna apenas o último pedido de cada cliente
SELECT DISTINCT ON (cliente_id) cliente_id, id, valor, criado_em
FROM pedidos
ORDER BY cliente_id, criado_em DESC;
```

Expressão `CASE` (Condicional)
```sql
SELECT nome,
       CASE 
           WHEN pontos > 1000 THEN 'VIP'
           ELSE 'PADRAO'
       END AS categoria
FROM clientes;
```

Consultando JSONB
```sql
-- Extrai o valor do campo "tema" dentro do JSONB 'preferencias'
SELECT nome, preferencias->>'tema' AS tema 
FROM usuarios;
```

---