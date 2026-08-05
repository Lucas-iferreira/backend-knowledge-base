# Cascade

## O que é?
O Cascade (Cascata) define como as operações realizadas na Entidade Pai devem se propagar automaticamente para as Entidades Filhas associadas no banco de dados.

---

## Tipos de `CascadeType`

| CascadeType |                             O que faz?                             |                    Exemplo Prático                     |
|:-----------:|:------------------------------------------------------------------:|:------------------------------------------------------:|
|  `PERSIST`  |    Ao salvar (`persist`) o Pai, salva os Filhos automaticamente.     |       Salvar um `Pedido` já salva seus `ItemPedido`.       |
|   `MERGE`   |      Ao atualizar (`merge`) o Pai, atualiza os Filhos no banco.      |   Atualizar o `Pedido` reflete nos itens modificados.    |
|  `REMOVE`   |   Ao deletar (`remove`) o Pai, deleta todos os Filhos vinculados.    |   Deletar um `Pedido` remove todos os seus `ItemPedido`.   |
|  `REFRESH`  | Recarrega do banco o Pai e atualiza os Filhos com os dados atuais. |   Reverter dados em memória para o estado do banco.    |
|  `DETACH`   |   Ao desvincular (`detach`) o Pai da sessão, desvincula os Filhos.   | Tirar o `Pedido` do estado Managed tira os itens também. |
|    `ALL`    |            Aplica todas as operações acima em cascata.             |    Atalho para aplicar todas as regras de uma vez.     |


### orphanRemoval = true vs. CascadeType.REMOVE
Apesar de parecidos, eles tratam exclusões de formas diferentes:
- `CascadeType.REMOVE`: Exclui os filhos do banco apenas quando a entidade Pai é deletada.
- `orphanRemoval = true`: Além de deletar quando o Pai é excluído, também deleta o Filho do banco se ele for removido da coleção/lista do Pai em Java (`pedido.getItens().remove(0)`).

## Exemplos 
```sql
@Entity
public class Pedido {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Propaga inserções/alterações e remove os itens desvinculados da lista
    @OneToMany(
        mappedBy = "pedido", 
        cascade = CascadeType.ALL, 
        orphanRemoval = true
    )
    private List<ItemPedido> itens = new ArrayList<>();
}
```
### ⚠️ Boas Práticas e Cuidados
- Cuidado com `CascadeType.ALL` e `REMOVE` em `@ManyToOne`: Nunca use cascata de remoção do filho para o pai. Deletar um `ItemPedido` apagaria o `Pedido` inteiro (ou até mesmo o `Cliente`).
- Cuidado em `@ManyToMany`: Nunca use `CascadeType.REMOVE` em relacionamentos muitos-para-muitos. Deletar um `Produto` acabaria deletando a `Categoria` associada que outros produtos utilizam.
- Uso Recomendado: `CascadeType.ALL` + `orphanRemoval` = true é o padrão ideal para Agregados FORTES (relação Pai/Filho estrita, onde o filho não existe sem o pai, ex: `Pedido` -> `ItemPedido`).

---