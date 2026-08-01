# Builder

## O que é?
Permite construir objetos complexos passo a passo, separando a criação da sua representação. É a solução perfeita para evitar construtores gigantescos (Telescoping Constructor) e objetos em estados inválidos/incompletos.

---

### ❌ O Problema (Código Ruim)
Construtores com muitos parâmetros (frequentemente do mesmo tipo) tornam o código confuso, propenso a erros de ordem de argumentos e difícil de ler. O uso de múltiplos setters pode deixar o objeto parcialmente construído ou mutável involuntariamente:

```java
// Construtor "Telescópico" (muito ruim de ler e usar)
public class Usuario {
    public Usuario(String nome, String email, String cpf, String telefone, int idade, String endereco) { ... }
}

// Uso confuso: Qual String é o CPF e qual é o telefone?
Usuario u = new Usuario("Carlos", "carlos@email.com", "123456789", "99999999", 30, "Rua A");
```

### ✅ A Solução com Builder
```java
public class Usuario {
    // Campos imutáveis (opcional, mas recomendado)
    private final String nome;      // Obrigatório
    private final String email;     // Obrigatório
    private final String telefone;  // Opcional
    private final int idade;        // Opcional

    // Construtor privado: só o Builder pode chamar
    private Usuario(Builder builder) {
        this.nome = builder.nome;
        this.email = builder.email;
        this.telefone = builder.telefone;
        this.idade = builder.idade;
    }

    // Builder como classe estática interna
    public static class Builder {
        private final String nome;  // Requerido
        private final String email; // Requerido
        private String telefone;    // Opcional
        private int idade;          // Opcional

        public Builder(String nome, String email) { // Campos obrigatórios no construtor do Builder
            this.nome = nome;
            this.email = email;
        }

        public Builder telefone(String telefone) {
            this.telefone = telefone;
            return this; // Retorna o próprio Builder para encadeamento (fluent interface)
        }

        public Builder idade(int idade) {
            this.idade = idade;
            return this;
        }

        public Usuario build() {
            return new Usuario(this);
        }
    }
}
```

### Uso Limpo e Fluente (Fluent API):

```java
// Claro, legível e seguro!
Usuario usuario = new Usuario.Builder("Carlos", "carlos@email.com")
        .telefone("11999999999")
        .idade(30)
        .build();
```

## ⚡ Quando Usar?
- Quando um objeto precisa de muitos parâmetros no construtor (geralmente mais de 4 ou 5).
- Quando há muitos parâmetros opcionais e você quer evitar combinações infinitas de construtores sobrecarregados.
- Quando você deseja garantir que o objeto seja imutável (sem métodos set) após a sua criação.
💡 Dica Lombok: Em projetos Java modernos, a anotação @Builder da biblioteca Lombok gera toda essa estrutura automaticamente em tempo de compilação!

---