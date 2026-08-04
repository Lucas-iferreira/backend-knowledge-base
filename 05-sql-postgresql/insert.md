# INSERT


### 1. Sintaxe Básica e Múltiplos Inserts
No Postgres, você pode inserir uma única linha ou empilhar vários registros em um único comando 
(o que é muito mais performático que disparar múltiplos `INSERT`s individuais).

```sql
-- Inserção Simples
INSERT INTO clientes (nome, email) 
VALUES ('Lucas', 'lucas@email.com');

-- Inserção Múltipla (Multi-row Insert)
INSERT INTO clientes (nome, email) 
VALUES 
    ('Ana', 'ana@email.com'),
    ('Carlos', 'carlos@email.com'),
    ('Beatriz', 'beatriz@email.com');
```

---

### 2. O Poder do `RETURNING` (Específico do Postgres)
Evita ter que fazer um `SELECT` logo após o `INSERT` para descobrir o ID gerado automaticamente ou a data/hora padrão inserida pelo banco.

```sql
-- Retorna o ID gerado e a data de criação no mesmo comando
INSERT INTO clientes (nome, email) 
VALUES ('Gabriel', 'gabriel@email.com')
RETURNING id, criado_em;

-- Retorna a linha inteira recém-inserida
INSERT INTO clientes (nome, email) 
VALUES ('Mariana', 'mariana@email.com')
RETURNING *;
```

### 3. Tratar Conflitos (`UPSERT` / `ON CONFLICT`)
O Postgres resolve a necessidade de "inserir ou atualizar se já existir" usando a cláusula ON `CONFLICT`. 
Exige uma restrição de unicidade (`UNIQUE` ou `PRIMARY KEY`) na coluna.

Opção A: Ignorar se já existir (`DO NOTHING`)

```sql
-- Se o e-mail já existir na tabela, simplesmente não faz nada e não lança erro
INSERT INTO clientes (nome, email)
VALUES ('Lucas', 'lucas@email.com')
ON CONFLICT (email) DO NOTHING;
```

Opção B: Atualizar se já existir (`DO UPDATE` / `Upsert`)
A palavra-chave `EXCLUDED` faz referência ao valor que você tentou inserir no comando.
```sql
-- Se o e-mail já existir, atualiza o nome e a data de atualização
INSERT INTO clientes (nome, email)
VALUES ('Lucas Iago', 'lucas@email.com')
ON CONFLICT (email) 
DO UPDATE SET 
    nome = EXCLUDED.nome,
    atualizado_em = CURRENT_TIMESTAMP;
```

### 4. `INSERT INTO ... SELECT` (Cópia/Migração)
Útil para popular tabelas a partir do resultado de uma consulta.
```sql
-- Insere na tabela de 'clientes_vip' todos os clientes com mais de 1000 pontos
INSERT INTO clientes_vip (cliente_id, pontos)
SELECT id, pontos
FROM clientes
WHERE pontos >= 1000;
```

### 5. Inserindo em Colunas `JSONB`
Para inserir objetos JSON dentro de colunas JSONB, passe a string válida formatada com a coerção do tipo `::jsonb`.
```sql
INSERT INTO usuarios (nome, preferencias)
VALUES (
    'Lucas', 
    '{"tema": "dark", "notificacoes": true}'::jsonb
);
```

---