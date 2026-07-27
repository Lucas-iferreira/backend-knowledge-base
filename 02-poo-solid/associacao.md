# Associação

## O que é?
Associação é o relacionamento entre duas classes independentes, onde uma classe "usa" ou "conhece" a outra.
Ao contrário da herança (que é um relacionamento do tipo "É UM"), a associação define um relacionamento do tipo "TEM UM" ou "USA UM".

---

## tipos

|         Tipo         |                         Relação                         |                                      Dependência / Ciclo de Vida                                      |              Exemplo              |
|:--------------------:|:-------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------:|:---------------------------------:|
|  Associação Simples  |                        "Usa um"                         |                 Totalmente independentes. Um objeto apenas se relaciona com o outro.                  |      `Pessoa` usa `Endereco`      |
|      Agregação       |                    "Tem um" (fraco)                     | Os objetos existem de forma independente. Se o objeto pai for destruído, o filho continua existindo.  | `Departamento` possui `Professor` |
|      Composição      |                    "Tem um" (forte)                     |         Relação de posse estrita. O objeto filho não faz sentido nem existe sem o objeto pai.         |      `Carro` possui `Motor`       |


## Exemplos Práticos em Java

### 1. Associação Simples
Os objetos conversam, mas nenhum é "dono" do outro.

```java
public class Endereco {
    private String rua;
    private String cidade;

    public Endereco(String rua, String cidade) {
        this.rua = rua;
        this.cidade = cidade;
    }
}

public class Pessoa {
    private String nome;
    private Endereco endereco; // Associação: Pessoa "tem um" Endereço

    public Pessoa(String nome, Endereco endereco) {
        this.nome = nome;
        this.endereco = endereco;
    }
}
```

### 2. Agregação (Relacionamento Fraco)
O Departamento agrupa vários Professores. Se o Departamento for extinto, os Professores continuam existindo na faculdade.
```java
import java.util.List;

public class Professor {
    private String nome;

    public Professor(String nome) {
        this.nome = nome;
    }
}

public class Departamento {
    private String nome;
    private List<Professor> professores; // Agregação

    // Os professores são criados fora e passados para o departamento
    public Departamento(String nome, List<Professor> professores) {
        this.nome = nome;
        this.professores = professores;
    }
}
```

### 3. Composição (Relacionamento Forte)
O Motor é criado dentro do Carro. Se o Carro for destruído no sistema, o Motor também deixa de existir.

```java
public class Motor {
    private int potencia;

    public Motor(int potencia) {
        this.potencia = potencia;
    }
}

public class Carro {
    private String modelo;
    private Motor motor; // Composição

    public Carro(String modelo, int potenciaMotor) {
        this.modelo = modelo;
        // O próprio Carro gerencia o ciclo de vida do seu Motor
        this.motor = new Motor(potenciaMotor); 
    }
}
```
- 💡 Regra de ouro: Prefira composição sobre herança sempre que possível ("composição sobre herança"). Isso deixa seu código muito mais flexível, menos acoplado e mais fácil de manter.

---