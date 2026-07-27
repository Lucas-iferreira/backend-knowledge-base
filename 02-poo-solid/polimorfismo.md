# Polimorfismo

## O que é?
Ocorre quando uma única ação pode ser executada de diferentes maneiras. Ele permite que uma classe filha herde o comportamento de uma classe mãe, mas altere a forma como esse comportamento funciona na prática

---

## Tipos
- Polimorfismo em tempo de compilação (Sobrecarga / Overloading): Vários métodos têm o mesmo nome, mas parâmetros diferentes na mesma classe.
- Polimorfismo em tempo de execução (Sobrescrita / Overriding): Uma classe filha reescreve um método idêntico da classe mãe usando a anotação `@Override`.

## Exemplo

O exemplo abaixo mostra como diferentes animais reagem ao mesmo método `emitirSom()`:

```java
// Classe Mãe
class Animal {
    public void emitirSom() {
        System.out.println("O animal faz um som.");
    }
}

// Classe Filha 1
class Cachorro extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("O cachorro morde o osso e faz: Au Au!");
    }
}

// Classe Filha 2
class Gato extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("O gato boceja e faz: Miau!");
    }
}

```

## Vantagens do Polimorfismo

- Reutilização de código: Você cria uma lógica geral na classe mãe e apenas ajusta as exceções nas filhas.
- Flexibilidade: Permite criar listas genéricas (como uma List<Animal>) que aceitam cachorros, gatos ou qualquer outro bicho sem quebrar o sistema.
---
