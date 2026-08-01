# Date e Time

## O que é?
No Java moderno (Java 8 e superior), a forma ideal e recomendada de trabalhar com datas e horas é 
através do pacote `java.time`.

---

## Usos Comuns

### 1. Pegar a Data e Hora Atual

- `LocalDate`: Apenas a data (ano, mês, dia).
- `LocalTime`: Apenas a hora (hora, minuto, segundo, nanossegundos).
- `LocalDateTime`: Data e hora juntas (sem fuso horário).
- `ZonedDateTime`: Data e hora com fuso horário específico.

```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;
import java.time.ZonedDateTime;
import java.time.ZoneId;

public class ExemploDataHora {
    public static void main(String[] args) {
        // Apenas a data atual
        LocalDate dataHoje = LocalDate.now();
        System.out.println("Data atual: " + dataHoje); // Ex: 2026-08-01

        // Apenas a hora atual
        LocalTime horaAtual = LocalTime.now();
        System.out.println("Hora atual: " + horaAtual); // Ex: 14:24:27.123

        // Data e hora juntas
        LocalDateTime dataHoraAtual = LocalDateTime.now();
        System.out.println("Data e hora: " + dataHoraAtual); // Ex: 2026-08-01T14:24:27.123

        // Data e hora com Fuso Horário (ex: São Paulo)
        ZonedDateTime dataHoraComFuso = ZonedDateTime.now(ZoneId.of("America/Sao_Paulo"));
        System.out.println("Com fuso: " + dataHoraComFuso);
    }
}
```

### 2. Formatar Data e Hora (Padrão Brasileiro dd/MM/yyyy)
Por padrão, o Java imprime no formato ISO ( `yyyy-MM-dd` ). 
Para formatar no padrão brasileiro, use `DateTimeFormatter`:

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class FormatarExemplo {
    public static void main(String[] args) {
        LocalDateTime agora = LocalDateTime.now();

        // Definindo o padrão de formatação
        DateTimeFormatter formatador = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");

        // Formatando para String
        String dataFormatada = agora.format(formatador);
        System.out.println("Formatado: " + dataFormatada); // Ex: 01/08/2026 14:24:27
    }
}
```

### 3. Converter uma String para Data (Parsing)
Quando você recebe uma data em texto (de um input ou banco de dados) e precisa converter para objeto Java:

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class ParseExemplo {
    public static void main(String[] args) {
        String dataTexto = "15/10/2026";
        
        DateTimeFormatter formatador = DateTimeFormatter.ofPattern("dd/MM/yyyy");
        LocalDate dataConvertida = LocalDate.parse(dataTexto, formatador);

        System.out.println("Objeto LocalDate: " + dataConvertida); // 2026-10-15
    }
}
```

### 4. Somar ou Subtrair Dias, Meses ou Anos
A API `java.time` é imutável e muito simples de manipular:
```java
import java.time.LocalDate;

public class OperacoesData {
    public static void main(String[] args) {
        LocalDate hoje = LocalDate.now();

        LocalDate proximaSemana = hoje.plusDays(7);
        LocalDate mesPassado = hoje.minusMonths(1);
        LocalDate anoQueVem = hoje.plusYears(1);

        System.out.println("Daqui a 7 dias: " + proximaSemana);
        System.out.println("Mês passado: " + mesPassado);
    }
}
```

## Resumo Rápido de Padrões( `DateTimeFormatter` )


|  Símbolo  |       Significado       |  Exemplo   |
|:---------:|:-----------------------:|:----------:|
|   `dd`    |  Dia do mês 2 digitos   | `01`,`05`  |
|   `MM`    |    Mês com 2 digitos    | `08`,`12`  |
|  `yyyy`   |    Ano com 4 digitos    |   `2026`   |
|   `HH`    |  Hora no formato 00-23  | `14`,`09`  |
|   `mm`    |         Minutos         | `05`,`45`  |
|   `ss`    |        Segundos         | `00`,`30`  |

⚠️ Nota histórica: Evite usar as classes antigas `java.util.Date` e `java.util.Calendar`. Elas possuem diversos problemas de design e mutabilidade. Use sempre o pacote `java.time`.

---