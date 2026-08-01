# Singleton

## O que é?
Garante que uma classe tenha apenas uma única instância em toda a aplicação e fornece um ponto de acesso global a ela.

---

### ❌ O Problema (Código Ruim)
Criar múltiplas instâncias de classes que deveriam ser centralizadas consome memória desnecessária e pode causar comportamentos inconsistentes (ex: conexões de banco de dados duplicadas ou logs fora de ordem):

```java
// Toda vez que alguém precisa do banco, cria um novo pool de conexões
public class AppService {
    public void salvar() {
        ConexaoBanco conexao = new ConexaoBanco(); // Instanciado várias vezes!
        conexao.conectar();
    }
}
```

### ✅ A Solução com Singleton
Existem duas abordagens clássicas em Java:

1. Abordagem Clássica (Lazy Initialization / Thread-Safe)
```java
public class ConexaoBanco {
    // 1. Atributo estático privado para guardar a única instância
    private static volatile ConexaoBanco instancia;

    // 2. Construtor PRIVADO impede que outros deem 'new'
    private ConexaoBanco() {
        // Inicialização de recursos pesados
    }

    // 3. Método público estático para obter a instância
    public static ConexaoBanco getInstancia() {
        if (instancia == null) {
            synchronized (ConexaoBanco.class) { // Thread-safe
                if (instancia == null) {
                    instancia = new ConexaoBanco();
                }
            }
        }
        return instancia;
    }

    public void executarQuery(String sql) {
        System.out.println("Executando: " + sql);
    }
}
```
2. Abordagem Moderna Recomendada (Via `Enum`)
Em Java, usar `Enum` é a maneira mais segura e concisa de criar Singletons, pois trata concorrência e serialização automaticamente:

```java
public enum ConexaoBanco {
    INSTANCIA; // Única instância garantida pela JVM

    public void executarQuery(String sql) {
        System.out.println("Executando: " + sql);
    }
}
```
Uso Limpo:
```java
// Sempre retorna o mesmo objeto na memória!
ConexaoBanco conexao = ConexaoBanco.getInstancia();
conexao.executarQuery("SELECT * FROM usuarios");

// Versão Enum:
// ConexaoBanco.INSTANCIA.executarQuery("SELECT * FROM usuarios");
```
## ⚡ Quando Usar?
- Para recursos compartilhados e pesados: Pool de Conexões de Banco de Dados, Gerenciadores de Log, Configurações da Aplicação ou Caches.
- Quando você precisa de controle estrito sobre o acesso a um recurso centralizado.
⚠️ Cuidado: Use com moderação! Singletons introduzem um estado global na aplicação e podem dificultar a 
escrita de testes de unidade caso não sejam abstraídos via Injeção 
de Dependência (como o Spring faz com o escopo `@Component/@Service`).

---