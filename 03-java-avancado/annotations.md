# Annotations

## O que é?
Anotações (Annotations) em Java são uma forma de metadados — ou seja, dados sobre os dados do seu código. Elas fornecem informações adicionais sobre o código sem alterar diretamente a sua lógica de execução.

Elas foram introduzidas no Java 5 e utilizam a sintaxe `@NomeDaAnotacao`.

---

### 1. Para que servem as Anotações?
As anotações têm três utilidades principais no ecossistema Java:

- Instruções para o Compilador: Avisam o compilador sobre erros ou alertas (ex: `@Override`, `@SuppressWarnings`).
- Processamento em Tempo de Compilação (Compile-time): Ferramentas analisam o código para gerar arquivos de configuração, código-fonte extra ou validar regras.
- Processamento em Tempo de Execução (Runtime via Reflection): Frameworks leem as anotações usando Reflection para mudar o comportamento da aplicação (ex: Spring, Hibernate/JPA, Jackson).


### 2. Anotações Embutidas do Java (Built-in Annotations)
O Java já vem com várias anotações prontas no pacote padrão:

|       Anotação       |                                                              Finalidade                                                               |
|:--------------------:|:-------------------------------------------------------------------------------------------------------------------------------------:|
|      @Override       | Garante que o método está sobrescrevendo um método da superclasse ou interface. Se houver erro de digitação, o compilador acusa erro. |
|     @Deprecated      |                        Marca um elemento como obsoleto, avisando aos desenvolvedores para não utilizá-lo mais.                        |
|  @SuppressWarnings   |                              Silencia avisos (warnings) do compilador em um trecho específico de código.                              |
| @FunctionalInterface |                          Garante que a interface possui exatamente um método abstrato (usada para Lambdas).                           |
```java
public class ExemploBuiltIn {
    
    @Override
    public String toString() {
        return "Sobrescrevendo o método toString com segurança";
    }

    @Deprecated(since = "2.0", forRemoval = true)
    public void metodoAntigo() {
        // Código legado
    }

    @SuppressWarnings("unchecked")
    public void limparAlertas() {
        // Código que geraria warnings de generics
    }
}
```

### 3. Criando sua Própria Anotação Customizada
Você pode definir suas próprias anotações usando a palavra-chave `@interface`. Para configurá-las, utilizamos as Meta-Anotações:

Meta-Anotações Essenciais:
- `@Retention`: Define até quando a anotação estará disponível:
- - `SOURCE`: Mantida apenas no código-fonte (descartada após compilação).
- - `CLASS`: Mantida no arquivo `.class`, mas não disponível em runtime.
- - `RUNTIME`: Mantida em tempo de execução (pode ser lida via Reflection).
- `@Target`: Define onde a anotação pode ser colocada (`TYPE` para classes/interfaces, `METHOD` para métodos, `FIELD` para atributos,
`PARAMETER` para parâmetros, etc.).

Exemplo Completo de Criar e Processar uma Anotação:
Passo 1: Definir a Anotação

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME) // Disponível em tempo de execução
@Target(ElementType.METHOD)         // Aplicável apenas em métodos
public @interface TabelaLog {
    String modulo() default "GERAL"; // Elemento com valor padrão
    int prioridade() default 1;
}
```
Passo 2: Usar a Anotação no Código
```java
public class ServicoUsuario {

    @TabelaLog(modulo = "AUTENTICACAO", prioridade = 3)
    public void fazerLogin() {
        System.out.println("Efetuando login...");
    }

    @TabelaLog // Usa os valores padrão (modulo="GERAL", prioridade=1)
    public void listarUsuarios() {
        System.out.println("Listando usuários...");
    }
}
```
Passo 3: Ler a Anotação com Reflection (Runtime)
```java
import java.lang.reflect.Method;

public class ProcessadorAnotacao {
    public static void main(String[] args) throws Exception {
        ServicoUsuario servico = new ServicoUsuario();
        Class<?> clazz = servico.getClass();

        // Varre todos os métodos da classe
        for (Method metodo : clazz.getDeclaredMethods()) {
            
            // Verifica se o método possui a anotação @TabelaLog
            if (metodo.isAnnotationPresent(TabelaLog.class)) {
                TabelaLog anotacao = metodo.getAnnotation(TabelaLog.class);
                
                System.out.println("Encontrado método anotado: " + metodo.getName());
                System.out.println(" - Módulo: " + anotacao.modulo());
                System.out.println(" - Prioridade: " + anotacao.prioridade());
                System.out.println("---");
            }
        }
    }
}
```
## 4. Anotações nos Frameworks do Mercado
No desenvolvimento moderno em Java, você raramente usará XML de configuração; tudo é feito via anotações:

- Spring Framework / Spring Boot => `@RestController, @Service, @Autowired, @GetMapping, @Transactional`.
- JPA / Hibernate => `@Entity, @Table, @Id, @GeneratedValue, @Column, @ManyToOne`.
- Lombok => `@Getter, @Setter, @NoArgsConstructor, @AllArgsConstructor, @Builder` (geram código em tempo de compilação sem você precisar escrever getters/setters).
- Jackson (JSON) =>  `@JsonProperty, @JsonIgnore, @JsonFormat`.

---