# HAVING

## O que é?
O `HAVING` é o "`WHERE` das agregações". Ele serve exclusivamente para filtrar os resultados gerados pelo `GROUP BY`.

---

### 1. Diferença Crucial: `WHERE` vs `HAVING`

|     CLAUSULA     |                    QUANDO ATUA?                    |         Pode usar funções de agregação?          |          Exemplo          |
|:----------------:|:--------------------------------------------------:|:------------------------------------------------:|:-------------------------:|
|     `WHERE`      | Antes do agrupamento (filtra linhas individuais).  | ❌ Não (ex: `WHERE SUM(valor) > 100` gera erro). |  `WHERE status = 'PAGO'`  |
|     `HAVING`     | Depois do agrupamento (filtra os grupos formados). |            ✅ Sim (feito para isso).             | `HAVING SUM(valor) > 100` |


### 2. Sintaxe e Fluxo na Prática
```sql
SELECT 
    categoria_id,
    COUNT(*) AS total_produtos,
    AVG(preco) AS media_preco
FROM produtos
WHERE ativo = true                -- 1. Filtra apenas produtos ativos
GROUP BY categoria_id             -- 2. Agrupa os produtos por categoria
HAVING COUNT(*) >= 5              -- 3. Mantém apenas categorias com 5+ produtos
   AND AVG(preco) > 50.00;        --    e cuja média de preço seja superior a R$ 50
```

### 3. Casos de Uso Comuns no Dia a Dia
A. Encontrar Duplicidades no Banco
Procura registros que aparecem mais de uma vez na tabela.
```sql
-- Encontra e-mails cadastrados em duplicidade
SELECT email, COUNT(*) AS quantidade
FROM usuarios
GROUP BY email
HAVING COUNT(*) > 1;
```

B. Filtrar Métricas Acumuladas
```sql
-- Busca clientes com alto volume de compras (mais de R$ 5.000 total)
SELECT cliente_id, SUM(valor_total) AS faturamento_cliente
FROM pedidos
GROUP BY cliente_id
HAVING SUM(valor_total) > 5000;
```

### 4. Dica Rápida de Sintaxe
💡 Aliases no HAVING: O PostgreSQL permite usar aliases criados no `SELECT` dentro do `ORDER BY`, mas não dentro do `HAVING` ou `WHERE`. Repita a função agregada no `HAVING`.
- ❌ `HAVING total_vendas > 10` (erro de identificador não existente)
- ✅ `HAVING COUNT(*) > 10`(correto)

---