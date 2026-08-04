# Modelagem Postgresql


### 1. Mapeamento de Tipos de Dados
O PostgreSQL se destaca por uma variedade rica de tipos nativos. Saber escolher o tipo correto garante performance, integridade e economia de armazenamento.

|        Categoria        |           Tipos Comuns            |                                                                           Recomendação / Uso                                                                            |
|:-----------------------:|:---------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|     Identificadores     | `BIGINT` (ou `BIGSERIAL`), `UUID` | Use `UUID` (`gen_random_uuid()`) para microsserviços/sistemas distribuídos; `BIGSERIAL` / `BIGINT GENERATED ALWAYS AS IDENTITY` para tabelas internas de alta gravação. |
|          Texto          |       `VARCHAR(n)`, `TEXT`        |                 No Postgres, `TEXT` e `VARCHAR` têm a mesma performance interna. Prefira `TEXT` a menos que precise impor restrição rígida de tamanho.                  |
| Dados Não Estruturados  |              `JSONB`              |                       Prefira sempre `JSONB` em vez de `JSON` plano (o `JSONB` armazena em formato binário decomposto, permitindo indexação GIN).                       |
|       Data e Hora       |           `TIMESTAMPTZ`           |                                Sempre prefira `TIMESTAMPTZ` (timestamp com timezone) para evitar confusões de fuso horário em produção.                                 |
|       Enumerações       |    `ENUM` ou Check Constraint     |                                         Utilitário para valores fixos e conhecidos (ex: `STATUS` (`'PENDENTE'`, `'APROVADO'`)).                                         |

---

### 2. Integridade e Restrições (Constraints)
As restrições garantem a consistência dos dados direto na camada de banco de dados, aliviando a lógica de aplicação.
- Primary Key (`PK`): Garante unicidade e cria um índice B-tree por padrão.
- Foreign Key (`FK`): Mantém a integridade referencial com ações declarativas em exclusões/atualizações (`ON DELETE CASCADE`, `ON DELETE SET NULL`, `ON DELETE RESTRICT`).
- Check Constraints (`CHECK`): Valida condições lógicas no momento da inserção (ex: `CHECK (preco > 0)` ou `CHECK (status IN ('A', 'I'))`).
- Unique Constraints (`UNIQUE`): Impede duplicidade (ex: email do usuário).

### 3. Estratégias de Indexação
A escolha do índice certo é crucial para consultas eficientes no Postgres.
- B-Tree: O padrão. Ideal para comparações diretas e intervalos (`=`, `<`, `>`, `BETWEEN`, `ORDER BY`).
- GIN (Generalized Inverted Index): Ideal para tipos complexos que contêm múltiplos valores dentro de uma única coluna (campos `JSONB`, arrays, busca textual / full-text search).
- GiST (Generalized Search Tree): Muito utilizado para dados geométricos/espaciais (PostGIS) e pesquisas por intervalos sobrepostos (`range types`).
- BRIN (Block Range Index): Excelente para tabelas massivas de histórico/logs onde os dados são inseridos em ordem cronológica contínua.

- Abstração: Isolamento de características essenciais de um objeto, escondendo detalhes complexos.
- Encapsulamento: Proteção de dados dentro do objeto, permitindo acesso apenas por métodos autorizados.
- Herança: Capacidade de uma nova classe herdar características e comportamentos de uma classe existente.
- Polimorfismo: Propriedade que permite que classes diferentes respondam à mesma mensagem de formas diferentes.

### 4. Normalização vs. Desnormalização Estratégica
1. 3ª Forma Normal (3FN):
- O padrão para modelagem transacional (OLTP).
- Evita redundância, anomalias de atualização e reduz o tamanho das tabelas.
2. Uso Prático de JSONB para Desnormalizar:
- Quando um atributo varia muito entre registros (ex: metadados de um produto) ou requer flexibilidade extrema sem quebrar o schema.
- Permite guardar documentos semiestruturados dentro de uma estrutura relacional rígida.

### 5. Boas Práticas e Convenções de Nomenclatura
Padrão de Nomenclatura:
- Use snake_case e letras minúsculas em tudo (nome_usuario, data_criacao).
- Evite usar palavras reservadas do SQL como nome de coluna ou tabela (user, order, group, date).
- Utilize nomes no plural ou singular de forma consistente na equipe (ex: tabelas usuarios, pedidos).

```sql
-- Exemplo Prático de Tabela Bem Modelada
CREATE TABLE usuarios (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    nome VARCHAR(150) NOT NULL,
    preferencias JSONB DEFAULT '{}'::jsonb,
    criado_em TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP NOT NULL
);

-- Índice GIN para busca rápida dentro do JSONB
CREATE INDEX idx_usuarios_preferencias ON usuarios USING GIN (preferencias);
```

---