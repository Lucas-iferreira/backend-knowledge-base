# Normalização de Dados

## O que é?
A normalização é o processo estruturado de organizar as tabelas e colunas de um banco de dados relacional para reduzir a redundância e eliminar anomalias de inserção, atualização e exclusão.

---

### 1. As Anomalias que a Normalização Evita
Antes das formas normais, vale documentar os problemas que ocorrem em tabelas desnormalizadas:
- Anomalia de Inserção: Impossibilidade de adicionar um registro sem dependurar dados fictícios ou nulos em outros campos.
- Anomalia de Atualização: Ter que alterar a mesma informação em múltiplos lugares (se esquecer um, gera inconsistência).
- Anomalia de Exclusão: Apagar um dado e, acidentalmente, perder outra informação importante junto.

### 2. As Formas Normais (FN) na Prática
No dia a dia do desenvolvimento, chegar até a 3ª Forma Normal (3FN) é o padrão recomendado para sistemas transacionais (OLTP).
```sql
[ Dados Brutos ] ──► 1FN (Atomicidade) ──► 2FN (Dependência Total) ──► 3FN (Sem Transitividade)
```
1ª Forma Normal (1FN) — Atomicidade
Cada coluna deve conter apenas valores atômicos (indivisíveis) e não devem existir grupos repetidos ou arrays de dados simples.
- ❌ Incorreto: Coluna `telefones` guardando `1199999999, 1188888888` na mesma célula.
- ✅ Correto: Mover os telefones para uma tabela filha (`usuario_telefones`) ou ter linhas separadas, garantindo 1 valor por célula.

2ª Forma Normal (2FN) — Dependência Funcional Total
Estar na 1FN E todos os atributos não-chave devem depender de toda a Chave Primária (relevante para chaves primárias compostas).
- ❌ Incorreto: Em uma tabela de `itens_pedido` (PK: `pedido_id`, `produto_id`), colocar a coluna `nome_produto`. O nome depende apenas de `produto_id`, não de `pedido_id`.
- ✅ Correto: Mover `nome_produto` para a tabela `produtos`.

3ª Forma Normal (3FN) — Eliminação de Dependências Transitivas
Estar na 2FN E nenhum atributo não-chave deve depender de outro atributo não-chave (dado depende apenas da PK, de nada mais).
- ❌ Incorreto: Na tabela `clientes`, ter as colunas `cep`, `rua`, `bairro` e `cidade`. A `cidade` depende do `cep`, que por sua vez depende do `id` do cliente.
- ✅ Correto: Separar os endereços em uma tabela de CEPs/Endereços ou referenciar apenas a chave do endereço.

### 3. Resumo Comparativo das Formas Normais

| Forma Normal |                            Regra Principal                             |                 O que Resolve?                 |
|:------------:|:----------------------------------------------------------------------:|:----------------------------------------------:|
|     1FN      |   Sem colunas com múltiplos valores ou listas separadas por vírgula.   |    Evita colunas complexas e indivisíveis.     |
|     2FN      | Todos os campos dependem da chave primária inteira (em PKs compostas). | Evita duplicação em tabelas de relacionamento. |
|     3FN      |        Nenhum campo não-chave depende de outro campo não-chave.        |  Evita dependências em cadeia (transitivas).   |

### 4. Quando Desnormalizar? (Exceções Conscientes)
Apesar da normalização ser a regra, em cenários específicos do PostgreSQL a desnormalização é aplicada deliberadamente:
- Performance de Leitura (Sistemas OLAP/Dashboards): Evitar `JOIN`s complexos em tabelas de milhões de registros unificando visões (ou usando `MATERIALIZED VIEWS`).
- Uso de `JSONB` no Postgres: Guardar metadados flexíveis ou logs de auditoria diretamente na tabela pai em vez de criar 5 tabelas filhas pequenas.
- Histórico Temporal de Preços/Dados: Copiar o preço do produto para a tabela `itens_pedido` no momento da compra. Isso não é erro de normalização, é preservação do histórico do evento transacional.


### 5. Exemplo de Evolução de Modelagem
```sql
-- ❌ ANTES (Não Normalizado / 0FN)
-- Problemas: Múltiplos telefones na mesma coluna (viola 1FN), nome do produto repetido (viola 2FN)
CREATE TABLE pedidos_baguncados (
    pedido_id INT,
    cliente_nome VARCHAR(100),
    cliente_telefones TEXT, -- "1199999999, 1188888888"
    produto_id INT,
    produto_nome VARCHAR(100), -- Repetido a cada pedido
    preco_unitario NUMERIC(10,2)
);

-- ✅ DEPOIS (Normalizado até 3FN)
CREATE TABLE clientes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(100) NOT NULL
);

CREATE TABLE cliente_telefones (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    cliente_id UUID REFERENCES clientes(id),
    numero VARCHAR(20) NOT NULL
);

CREATE TABLE produtos (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(100) NOT NULL,
    preco_atual NUMERIC(10,2) NOT NULL
);

CREATE TABLE pedidos (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    cliente_id UUID REFERENCES clientes(id),
    criado_em TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE pedido_itens (
    pedido_id UUID REFERENCES pedidos(id),
    produto_id UUID REFERENCES produtos(id),
    quantidade INT NOT NULL CHECK (quantidade > 0),
    preco_aplicado NUMERIC(10,2) NOT NULL, -- Preserva o valor no momento da venda
    PRIMARY KEY (pedido_id, produto_id)
);
```
---