# Reflection

## O que é?
Reflection (ou Reflexão) é um recurso avançado do Java que permite a um programa examinar, introspectar e modificar sua própria estrutura e comportamento em tempo de execução (runtime).
Através da API de Reflexão (pacote `java.lang.reflect`), você consegue navegar pelas classes, descobrir métodos, campos, construtores e até acessar membros privados sem conhecer a estrutura da classe em tempo de compilação.

---

## Modos de utilizar:

### 1. O Ponto de Partida: A Classe `Class<T>`
Tudo na reflexão começa obtendo uma referência do objeto `Class`. Existem 3 formas principais de fazer isso:


```java
// 1. A partir do nome da classe (método estático)
Class<?> clazz1 = Class.forName("com.exemplo.Pessoa");

// 2. A partir da própria classe (.class)
Class<Pessoa> clazz2 = Pessoa.class;

// 3. A partir de uma instância existente (.getClass())
Pessoa p = new Pessoa();
Class<?> clazz3 = p.getClass();
```

### 2. Exemplos Práticos de Uso
Para os exemplos abaixo, considere a seguinte classe de teste:


```java
package com.exemplo;

public class Usuario {
    private String nome;
    public int idade;

    public Usuario() {
        this.nome = "Sem nome";
    }

    private Usuario(String nome) {
        this.nome = nome;
    }

    private void dizerOla(String mensagem) {
        System.out.println(nome + " diz: " + mensagem);
    }
}
```
A. Criar Instâncias Dinamicamente (Construtores)
Você pode instanciar objetos chamando construtores públicos ou privados em tempo de execução:
```java
import java.lang.reflect.Constructor;

public class TesteInstancia {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = Class.forName("com.exemplo.Usuario");

        // 1. Usando construtor público padrão
        Usuario u1 = (Usuario) clazz.getDeclaredConstructor().newInstance();

        // 2. Acessando construtor PRIVADO com parâmetros
        Constructor<?> construtorPrivado = clazz.getDeclaredConstructor(String.class);
        construtorPrivado.setAccessible(true); // Desativa a checagem de acesso
        Usuario u2 = (Usuario) construtorPrivado.newInstance("Carlos");
    }
}
```

B. Inspecionar e Modificar Atributos (Campos Privados)
É possível ler e alterar valores de variáveis, mesmo que sejam `private`:
```java
import java.lang.reflect.Field;

public class TesteCampo {
    public static void main(String[] args) throws Exception {
        Usuario usuario = new Usuario();

        Class<?> clazz = usuario.getClass();

        // Obtém o campo privado 'nome'
        Field campoNome = clazz.getDeclaredField("nome");
        campoNome.setAccessible(true); // Permite acesso a membro private

        // Lendo o valor original
        System.out.println("Valor antigo: " + campoNome.get(usuario)); // "Sem nome"

        // Alterando o valor em tempo de execução
        campoNome.set(usuario, "Ana Silva");

        // Lendo o novo valor
        System.out.println("Valor novo: " + campoNome.get(usuario)); // "Ana Silva"
    }
}
```

C. Invocar Métodos Privados Dinamicamente
Você pode localizar um método pelo nome e tipos de parâmetros e executá-lo via `.invoke()`:
```java
import java.lang.reflect.Method;

public class TesteMetodo {
    public static void main(String[] args) throws Exception {
        Usuario usuario = new Usuario();

        Class<?> clazz = usuario.getClass();

        // Busca o método 'dizerOla' que aceita uma String como parâmetro
        Method metodo = clazz.getDeclaredMethod("dizerOla", String.class);
        metodo.setAccessible(true); // Libera acesso ao método private

        // Invoca o método no objeto 'usuario' passando o argumento
        metodo.invoke(usuario, "Bem-vindo ao Java Reflection!");
        // Saída: Sem nome diz: Bem-vindo ao Java Reflection!
    }
}
```

### 3. Principais Métodos da API `java.lang.reflect`

|                    Método                     |                                          Descrição                                           |
|:---------------------------------------------:|:--------------------------------------------------------------------------------------------:|
|       `getFields()` / `getDeclaredFields()`       |          Retorna campos públicos / todos os campos declarados (incluindo privados).          |
|      `getMethods()` / `getDeclaredMethods()`      |                   Retorna métodos públicos / todos os métodos declarados.                    |
| `getConstructors()` / `getDeclaredConstructors()` |              Retorna construtores públicos / todos os construtores declarados.               |
|              `setAccessible(true)`              | Suprime a checagem de acesso do Java, permitindo manipular membros `private` ou `protected`. |
|               `getAnnotations()`                |            Retorna as anotações presentes no elemento (classe, método ou campo).             |

- Abstração: Isolamento de características essenciais de um objeto, escondendo detalhes complexos.
- Encapsulamento: Proteção de dados dentro do objeto, permitindo acesso apenas por métodos autorizados.
- Herança: Capacidade de uma nova classe herdar características e comportamentos de uma classe existente.
- Polimorfismo: Propriedade que permite que classes diferentes respondam à mesma mensagem de formas diferentes.

### 4. Onde a Reflexão é Utilizada no Mundo Real?
- Spring Framework: Para Injeção de Dependências (@Autowired), varredura de componentes (@Component) e criação de proxies dinâmicos.
- Hibernate / JPA: Para ler anotações como @Entity, @Column, @Id e mapear campos privados de objetos diretamente para colunas do banco de dados.
- Jackson / Gson: Para converter objetos Java em JSON e vice-versa sem exigir getters/setters públicos.
- JUnit: Para identificar e rodar métodos de teste anotados com @Test.


## ⚠️ Vantagens e Cuidados
Vantagens:
- Extensibilidade e Flexibilidade: Permite criar frameworks genéricos e ferramentas de automação.
- Desacoplamento: O código pode lidar com classes que nem existiam quando o programa foi compilado. 

Desvantagens e Riscos:
- Perda de Performance: Chamadas via reflexão são significativamente mais lentas do que invocações diretas de métodos (embora o JIT otimize parte disso).
- Quebra de Encapsulamento: Permite violar regras de visibilidade (`private`), o que pode gerar comportamentos inesperados ou falhas de segurança.
- Erros em Runtime: Erros de digitação nos nomes de métodos ou campos não são pegos pelo compilador, resultando em exceções como `NoSuchMethodException` ou `ClassNotFoundException` durante a execução.


---