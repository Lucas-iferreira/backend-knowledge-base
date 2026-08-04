# Views

## O que é?
No PostgreSQL, uma View (visão) é essencialmente uma consulta SQL armazenada no banco de dados com um nome associado.
Ela funciona como uma tabela virtual: não armazena os dados fisicamente (na versão padrão), 
mas executa a consulta subjacente toda vez que você faz um `SELECT` nela.

## Para que servem as Views?
- Simplificação de Consultas Complexas: Esconda `JOIN`s pesados, agregações ou filtros recorrentes atrás de um simples `SELECT * FROM nome_da_view`.
- Segurança e Controle de Acesso: Permita que usuários acessem apenas colunas ou linhas específicas de uma tabela sem dar acesso direto à tabela base.
- Camada de Abstração: Se a estrutura das tabelas base mudar (ex: refatoração de colunas), você pode alterar a View sem quebrar as aplicações que dependem dela.
- Padronização: Garante que toda a equipe/sistema aplique as mesmas regras de negócio ao buscar determinados dados.

---

## Tipos de View
### 1. View Padrão (Simples ou Simulada)
Executa a query em tempo real sempre que é chamada. Não ocupa espaço em disco (além dos metadados da definição).

Sintaxe Básica:
 ```sql
CREATE OR REPLACE VIEW vw_pedidos_detalhados AS
SELECT 
    p.id AS pedido_id,
    c.nome AS cliente_nome,
    p.data_pedido,
    p.valor_total
FROM pedidos p
JOIN clientes c ON c.id = p.cliente_id
WHERE p.status = 'CONCLUIDO';
```

Como consultar:

```sql
SELECT * FROM vw_pedidos_detalhados WHERE valor_total > 100;
```

### 2. Materialized View (Visão Materializada)
Diferente da view padrão, a Materialized View salva o resultado da consulta fisicamente no disco.

É excelente para relatórios pesados e dashboards de BI, onde rodar a consulta em tempo real seria muito lento.

Criando uma Materialized View:

```sql
CREATE MATERIALIZED VIEW mv_vendas_por_mes AS
SELECT 
    DATE_TRUNC('month', data_pedido) AS mes,
    COUNT(id) AS total_pedidos,
    SUM(valor_total) AS faturamento
FROM pedidos
GROUP BY 1;
```

### Atualizando os dados (Refresh):
Como os dados ficam salvos em disco, eles não atualizam sozinhos quando a tabela base muda. Você precisa atualizar manualmente ou via job (cron/pg_cron):

```sql
-- Atualiza travando a leitura durante a execução:
REFRESH MATERIALIZED VIEW mv_vendas_por_mes;

-- Atualiza sem travar leituras (requer um índice único na view):
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_vendas_por_mes;
```


## ⚡ Views Atualizáveis (Updatable Views)
Em alguns cenários, você pode fazer `INSERT`, `UPDATE` ou `DELETE` diretamente na View, e o Postgres repassa essa alteração para a tabela base.

Para uma View ser atualizável automaticamente no Postgres, ela deve cumprir alguns requisitos:
- Ter exatamente uma tabela base na cláusula `FROM`.
- Não conter `GROUP BY`, `HAVING`, `LIMIT`, `OFFSET`, `DISTINCT`, `UNION` ou funções de agregação (`SUM`, `COUNT`, etc.).


```sql
CREATE VIEW vw_clientes_ativos AS
SELECT id, nome, email, status
FROM clientes
WHERE status = 'ATIVO';

-- Atualizando diretamente pela View:
UPDATE vw_clientes_ativos 
SET nome = 'Lucas Iago' 
WHERE id = 1;
```
Dica Pro (`WITH CHECK OPTION`): Se você inserir um registro através de uma view que não atende aos critérios do `WHERE` dela, o Postgres permite por padrão. Use `WITH CHECK OPTION` no final da view para impedir inserções/edições que "desapareceriam" da própria view.

## ⚖️ Tabela Comparativa: View Standard vs. Materialized View


|     CARACTERISTICA     |           View Padrão (`CREATE VIEW`)           | Materialized View (`CREATE MATERIALIZED VIEW`)  |
|:----------------------:|:---------------------------------------------:|:---------------------------------------------:|
|     Armazenamento      |       Apenas a definição SQL (Virtual)        |            Salva os dados no disco            |
| Performance de Leitura |     Depende da complexidade da query base     |   Extremamente rápida (lê direto do disco)    |
| Atualização dos Dados  |      Sempre em tempo real / instantânea       |       Requer `REFRESH MATERIALIZED VIEW `       |
|  Uso de Memória/Disco  |                     Nulo                      |    Ocupa espaço proporcional ao resultado     |
|       Ideal Para       | Abstração de regras, segurança, simplificação | Relatórios pesados, agregações, Dashboards/BI |


### 📌 Comandos Úteis para Documentação

```sql
-- Listar todas as views do schema atual
SELECT table_name 
FROM information_schema.views 
WHERE table_schema = 'public';

-- Ver a definição (código SQL) de uma view
SELECT definition 
FROM pg_views 
WHERE viewname = 'vw_pedidos_detalhados';

-- Excluir uma view
DROP VIEW IF EXISTS vw_pedidos_detalhados;
DROP MATERIALIZED VIEW IF EXISTS mv_vendas_por_mes;
```
---