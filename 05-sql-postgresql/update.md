# UPDATE

## 1. Sintaxe Básica e Segurança
O `UPDATE` altera registros existentes. Sempre valide o WHERE para evitar atualizar a tabela inteira por engano.

```sql
-- Atualização Simples
UPDATE clientes 
SET nome = 'Lucas Iago', 
    atualizado_em = CURRENT_TIMESTAMP
WHERE id = 'c39d8920-5b12-4217-9154-1a3ef944b15d';
```
⚠️ Dica de Segurança: Em scripts de manutenção manual, execute primeiro um `SELECT` com a mesma condição do `WHERE` para verificar quais linhas serão afetadas.

---

### 2. Uso do `RETURNING` no `UPDATE`
Assim como no `INSERT`, o Postgres permite retornar os dados alterados na mesma instrução sem precisar de um `SELECT` adicional.
```sql
-- Retorna os novos valores logo após atualizar
UPDATE produtos 
SET preco = preco * 1.10 -- Aumento de 10%
WHERE categoria = 'Eletrônicos'
RETURNING id, nome, preco AS novo_preco;
```

### 3. UPDATE com Junção de Tabelas (`FROM`)
Quando você precisa atualizar uma tabela com base em dados de outra tabela,
utilize a cláusula `FROM` (equivalente ao `JOIN` no `UPDATE`).
```sql
-- Atualiza o status de pedidos para 'CANCELADO' se o cliente estiver inativo
UPDATE pedidos p
SET status = 'CANCELADO'
FROM clientes c
WHERE p.cliente_id = c.id
  AND c.status = 'INATIVO'
  AND p.status = 'PENDENTE';
```

### 4. Atualizando Campos em Objetos `JSONB`
Para modificar propriedades específicas de um documento JSON sem sobrescrever o objeto inteiro,
use as funções nativas de JSONB:

Atualizar/Inserir uma chave específica (`jsonb_set`)
```sql
-- Altera ou cria o campo "tema" dentro da coluna JSONB 'preferencias'
UPDATE usuarios 
SET preferencias = jsonb_set(
    preferencias, 
    '{tema}', 
    '"light"'::jsonb
)
WHERE id = 'c39d8920-5b12-4217-9154-1a3ef944b15d';
```

Concatenar/Mesclar JSONs (`||`)
```sql
-- Adiciona ou sobrescreve chaves diretamente via concatenação
UPDATE usuarios 
SET preferencias = preferencias || '{"notificacoes": false, "linguagem": "pt-BR"}'::jsonb
WHERE id = 'c39d8920-5b12-4217-9154-1a3ef944b15d';
```

---