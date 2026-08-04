# Group BY


### 1. Regra de Ouro do `GROUP BY`
⚠️ A Regra: Qualquer coluna que estiver no `SELECT` deve obrigatoriamente estar na 
cláusula `GROUP BY` ou dentro de uma função de agregação (`SUM`, `COUNT`, `AVG`, etc.).

```sql
-- ❌ ERRO: 'nome' não está no GROUP BY nem numa função agregada
SELECT cliente_id, nome, COUNT(*) 
FROM pedidos 
GROUP BY cliente_id;

-- ✅ CORRETO
SELECT cliente_id, nome, COUNT(*) 
FROM pedidos 
GROUP BY cliente_id, nome;
```

---

### 2. Funções de Agregação Mais Usadas
|      FUNÇÃO      |                  O QUE FAZ?                   |     EXEMPLO DE USO?     |
|:----------------:|:---------------------------------------------:|:-----------------------:|
|    `COUNT(*)`    | Conta o número de linhas (incluindo `NULL`s). |       `COUNT(*)`        |
| `COUNT(coluna)`  |   Conta linhas onde a coluna não é `NULL`.    |     `COUNT(email)`      |
|  `SUM(coluna)`   |          Soma dos valores numéricos.          |      `SUM(valor)`       |
|  `AVG(coluna)`   |               Média aritmética.               |      `AVG(preco)`       |
|  `MIN(coluna)`   |                 Menor valor.                  |    `MIN(criado_em)`     |
|  `MAX(coluna)`   |                 Maior valor.                  |      `MAX(valor)`       |


### 3. O Filtro de Grupos: `WHERE` vs `HAVING`
- `WHERE`: Filtra as linhas ANTES de agrupar.
- `HAVING`: Filtra os grupos DEPOIS da agregação.
```sql
-- Busca total gasto por cliente, considerando apenas pedidos 'PAGOS',
-- e exibe apenas clientes que gastaram mais de R$ 1.000 no total.
SELECT cliente_id, SUM(valor) AS total_gasto
FROM pedidos
WHERE status = 'PAGO'          -- 1º Filtra linhas individuais
GROUP BY cliente_id            -- 2º Agrupa por cliente
HAVING SUM(valor) > 1000;      -- 3º Filtra o resultado dos grupos
```
### 4. Agregações Avançadas do Postgres
A. Filtrar Agregações com `FILTER` (Muito Útil no Postgres)
Evita ter que fazer múltiplos `CASE WHEN` para contar ou somar condições diferentes.

```sql
-- Conta pedidos normais e cancelados em uma única consulta
SELECT 
    cliente_id,
    COUNT(*) AS total_pedidos,
    COUNT(*) FILTER (WHERE status = 'CANCELADO') AS cancelados,
    SUM(valor) FILTER (WHERE status = 'PAGO') AS total_pago
FROM pedidos
GROUP BY cliente_id;
```
B. Agrupar em Texto com `STRING_AGG`
Junta valores de um grupo em uma única string separada por delimitador.
```sql
-- Lista os produtos comprados em cada pedido
SELECT 
    pedido_id,
    STRING_AGG(produto_nome, ', ' ORDER BY produto_nome) AS lista_produtos
FROM pedido_itens
GROUP BY pedido_id;
```

---