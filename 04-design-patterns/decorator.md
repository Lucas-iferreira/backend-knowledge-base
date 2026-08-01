# Decorator

## O que é?
Permite adicionar novos comportamentos a um objeto dinamicamente sem alterar seu código original e sem precisar criar uma explosão de subclasses via herança. Ele envelopa (wraps) o objeto original dentro de objetos "decoradores".

---

### ❌ O Problema (Código Ruim)
Usar herança excessiva para combinar diferentes funcionalidades ou variações de um mesmo conceito. Isso gera um número enorme de classes engessadas:

```java
// Explosão de subclasses para cobrir todas as combinações de um café:
public class Cafe { public double getCusto() { return 5.0; } }

public class CafeComLeite extends Cafe { ... }
public class CafeComChocolat extends Cafe { ... }
public class CafeComLeiteEChocolat extends Cafe { ... } // E se quiser chantilly? +1 classe!
```

### ✅ A Solução com Decorator
```java
// 1. Interface Comum (Componente)
public interface Cafe {
    double getCusto();
    String getDescricao();
}

// 2. Objeto Base (Componente Concreto)
public class CafeSimples implements Cafe {
    @Override
    public double getCusto() { return 5.0; }

    @Override
    public String getDescricao() { return "Café Simples"; }
}

// 3. Decorador Base (Abstrato)
public abstract class CafeDecorator implements Cafe {
    protected final Cafe cafeDecorado; // Composição sobre Herança!

    public CafeDecorator(Cafe cafe) {
        this.cafeDecorado = cafe;
    }

    @Override
    public double getCusto() { return cafeDecorado.getCusto(); }

    @Override
    public String getDescricao() { return cafeDecorado.getDescricao(); }
}

// 4. Decoradores Concretos
public class LeiteDecorator extends CafeDecorator {
    public LeiteDecorator(Cafe cafe) { super(cafe); }

    @Override
    public double getCusto() { return super.getCusto() + 2.0; }

    @Override
    public String getDescricao() { return super.getDescricao() + " + Leite"; }
}

public class ChocolateDecorator extends CafeDecorator {
    public ChocolateDecorator(Cafe cafe) { super(cafe); }

    @Override
    public double getCusto() { return super.getCusto() + 3.5; }

    @Override
    public String getDescricao() { return super.getDescricao() + " + Chocolate"; }
}
```
### Uso Limpo no Sistema:
```java
// Começa com o café base (R$ 5.0)
Cafe meuCafe = new CafeSimples();

// Adiciona Leite (R$ 5.0 + R$ 2.0)
meuCafe = new LeiteDecorator(meuCafe);

// Adiciona Chocolate no café que JÁ TEM leite (R$ 7.0 + R$ 3.5)
meuCafe = new ChocolateDecorator(meuCafe);

System.out.println(meuCafe.getDescricao()); // Café Simples + Leite + Chocolate
        System.out.println("R$ " + meuCafe.getCusto()); // R$ 10.5
```
## ⚡ Quando Usar?
- Quando você precisa adicionar ou remover responsabilidades de objetos em tempo de execução sem afetar outros objetos.
- Quando estender o comportamento por herança é impraticável ou geraria subclasses demais.
- Exemplo real da JDK: `java.io (new BufferedReader(new FileReader("file.txt"))` ).

---