# INDICES


### 1. O que são e para que servem?
Índices são estruturas de dados auxiliares que aceleram a busca de registros em consultas (`SELECT`, `WHERE`, `JOIN`), evitando que o banco precise varrer a tabela inteira (Seq Scan).

---

### 2. Tipos de Índices no Postgres

|             TIPO             |                                                       QUANDO USAR?                                                       |                          EXEMPLO                           |
|:----------------------------:|:------------------------------------------------------------------------------------------------------------------------:|:----------------------------------------------------------:|
|           `B-Tree`           |                             Padrão. Ideal para =, >, <, BETWEEN, IN e ordenação (ORDER BY).                              |               IDs, datas, valores, e-mails.                |
|            `GIN`             |                       Inverted Index. Ideal para valores compostos/múltiplos dentro de uma coluna.                       |          Colunas JSONB, arrays, Full-Text Search.          |
|            `GiST`            |                          Spatial/Range. Ideal para tipos geométricos e intervalos sobrepostos.                           |      Dados do PostGIS (latitude/longitude), daterange      |
|            `BRIN`            |  Block Range. Tabelas gigantes em que os dados são inseridos em ordem cronológica contínua. Consome quase nada de RAM.   |           Logs, históricos de eventos temporais.           |

### 3. Comandos Principais (Sintaxe)
A. Criar Índice Simples (B-Tree)
```sql
CREATE INDEX idx_pedidos_cliente_id ON pedidos(cliente_id);
```
B. Criar Índice Composto (Múltiplas Colunas)
A ordem das colunas importa: a coluna mais usada em filtros deve vir primeiro.
```sql
CREATE INDEX idx_pedidos_cliente_status ON pedidos(cliente_id, status);
```
C. Criar Índice GIN para JSONB
```sql
CREATE INDEX idx_usuarios_preferencias ON usuarios USING GIN (preferencias);
```
D. Criar Índice sem Bloquear a Tabela (`CONCURRENTLY`)
Em produção com tabelas grandes, use `CONCURRENTLY` para evitar travar a tabela para gravação enquanto o índice é construído.
```sql
CREATE INDEX CONCURRENTLY idx_clientes_email ON clientes(email);
```

### 4. Índices Especiais (Macetes do Postgres)
A. Índice Parcial (`WHERE`)
Indexa apenas um subconjunto da tabela. Economiza espaço em disco e melhora a performance.
```sql
-- Indexa apenas os pedidos pendentes (evita indexar histórico antigo de finalizados)
CREATE INDEX idx_pedidos_pendentes ON pedidos(criado_em) 
WHERE status = 'PENDENTE';
```
B. Índice de Expressão / Função
Útil quando você costuma buscar aplicando funções na coluna.
```sql
-- Acelera buscas usando LOWER() no e-mail
CREATE INDEX idx_clientes_email_lower ON clientes(LOWER(email));
```

### 5. Boas Práticas e Cuidados
- ⚠️ FKs não criam índices automaticamente: O Postgres não indexa chaves estrangeiras por padrão. Sempre crie índices manualmente nas colunas usadas em JOINs (`cliente_id`, `produto_id`).
- ⚠️ Custo de gravação: Cada índice deixa os comandos `INSERT`, `UPDATE` e `DELETE` ligeiramente mais lentos, pois o banco precisa atualizar o índice a cada mudança. Crie apenas os necessários.
- ⚠️ Índices não usados: Remova índices duplicados ou que não são utilizados pela aplicação:

```sql
DROP INDEX idx_nome_do_indice;
```

---