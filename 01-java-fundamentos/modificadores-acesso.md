# Modificadores de acesso

## O que é?

Modificadores de acesso são palavras-chave que definem a visibilidade (quem pode enxergar e chamar) de classes, atributos, métodos e construtores.

---

## Tipos

### 1 `public` (Acesso Total)
Qualquer classe em qualquer pacote do projeto pode acessar.

- Onde usar: Classes, métodos principais (como APIs/controllers), constantes globais.
```java
public class ProdutoService {
    public void buscarProdutos() { ... }
}

```

### 2 `protected` (Família + Vizinhos)
Acessível por classes do mesmo pacote e por subclasses (filhos), mesmo se a subclasse estiver em outro pacote.

- Onde usar: Métodos de classes abstratas ou atributos que você quer que apenas as classes filhas herdem e manipulem diretamente.

```java
public class Forma {
    protected String cor; // Classes filhas (ex: Circulo) conseguem acessar 'cor'
}
```

### 3 `Package-Private / Default` (Apenas no mesmo Pacote)
Quando você não escreve nada antes do tipo/método. Só quem está no mesmo pacote consegue enxergar. Subclasses em outros pacotes não têm acesso.

- Onde usar: Classes/métodos auxiliares que só fazem sentido dentro daquele pacote específico e não devem vazar para o resto do sistema.

```java
class ValidadorInterno { // Sem modificador -> 'default'
    void validarEmail(String email) { ... }
}
```

### 4 `private` (Ultra Restrito)

Apenas o código dentro da própria classe pode enxergar ou alterar.

- Onde usar: Na grande maioria dos atributos/campos da classe (base do Encapsulamento).

```java
public class Usuario {
    private String senha; // Ninguém fora desta classe acessa 'senha' diretamente
    
    // O acesso externo é feito por métodos públicos controlados
    public boolean validarSenha(String input) {
        return this.senha.equals(input);
    }
}
```
---
