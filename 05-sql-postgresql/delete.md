# DELETE

### 1. Sintaxe Básica
Remove linhas de uma tabela com base em uma condição.
```sql
-- Deleta registros específicos
DELETE FROM clientes 
WHERE status = 'INATIVO' 
  AND ultimo_login < '2025-01-01';
```
⚠️ Atenção: Executar `DELETE FROM` tabela; sem a cláusula `WHERE` apaga todos os registros da tabela.

---

### 2. Uso do RETURNING no DELETE
Muito útil para capturar os dados que foram removidos (para salvar em logs de auditoria ou retornar na API sem precisar fazer `SELECT` antes).

 ```sql
-- Apaga e já devolve o que foi deletado
DELETE FROM pedidos 
WHERE status = 'RASCUNHO' 
RETURNING id, cliente_id, criado_em;
```
### 3. DELETE com Condições em Outra Tabela (USING)
Quando você precisa deletar registros baseando-se em dados de outra tabela, use a cláusula `USING` (o equivalente ao `JOIN` para o `DELETE`).

```sql
-- Deleta itens de pedidos que pertencem a pedidos já cancelados
DELETE FROM pedido_itens pi
USING pedidos p
WHERE pi.pedido_id = p.id
  AND p.status = 'CANCELADO';
```

### 4. `DELETE` vs `TRUNCATE`
Quando o objetivo é apagar todos os dados de uma tabela, escolha a ferramenta certa:


|       Caracteristica       |               `DELETE FROM tabela;`               |               `TRUNCATE TABLE tabela;`                |
|:--------------------------:|:-------------------------------------------------:|:-----------------------------------------------------:|
|         Mecanismo          |              Remove linha por linha.              |  Limpa a tabela alocando blocos de disco de uma vez.  |
|         Velocidade         |             Lento em tabelas grandes.             |                 Extremamente rápido.                  |
|      Filtro (`WHERE`)      |                     Permite.                      |               Não permite (limpa tudo).               |
|      Espaço em Disco       | Não libera o espaço de imediato (exige `VACUUM`). |        Libera o espaço em disco imediatamente.        |
| Reiniciar IDs (`IDENTITY`) |             Mantém a sequência atual.             | Pode reiniciar a contagem de IDs (RESTART IDENTITY).  |

```sql
-- Limpa a tabela inteira e reinicia os IDs do zero
TRUNCATE TABLE logs RESTART IDENTITY;
```


### 5. Boas Práticas: Soft Delete (Exclusão Lógica)
Em sistemas produtivos, evite apagar dados fisicamente com `DELETE`. Prefira usar uma coluna de controle (ex: `deletado_em`).
```sql
-- Em vez de DELETE, use Soft Delete via UPDATE:
UPDATE clientes 
SET deletado_em = CURRENT_TIMESTAMP 
WHERE id = 'c39d8920-5b12-4217-9154-1a3ef944b15d';

-- Nas consultas SELECT, basta filtrar:
SELECT * FROM clientes WHERE deletado_em IS NULL;
``` 

---