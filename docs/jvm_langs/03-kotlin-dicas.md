---
sidebar_position: 2
---

# Kotlin - Dicas e truques

---

## Constantes - const

Você já deve ter se perguntado qual a diferença de uma const para uma variável comum e por qual motivo devo utilizar?
A resposta é: forma de acesso e seu impacto no desempenho.

```kotlin

object Constants {
    val VALOR001 = "valor_001"
    const val VALOR002 = "valor_002"
}

fun main() {
    println(Constants.VALOR001)
    println(Constants.VALOR002)
}

```

Se compilarmos essa classe, veremos que o kotlin irá transformar qualquer acesso ao VALOR001 em uma chamada via
"get method", **tornando seu acesso mais pesado**.

Podemos verificar esse comportamento de perto:

```shell
javap -p Constants.class

Compiled from "Main.kt"
public final class org.example.Constants {
  public static final org.example.Constants INSTANCE;
  private static final java.lang.String VALOR001; <-- note aqui, foi definido como "private"
  public static final java.lang.String VALOR002;
  private org.example.Constants();
  public final java.lang.String getVALOR001(); <-- e aqui, seu método get 

# Note também que o VALOR002 foi definido como publico e estático,
# não tendo nenhum metodo intermediário.

```

Também é provável que a JVM aproveite a natureza imutável e estática do VALOR002 para aplicar otimizações em tempo de
execução:

```kotlin

// levando a linha:
println(Constants.VALOR002)

// ser transformada em algo similar a:
println("valor_002") // <-- removendo qualquer forma de acesso pelo valor integral da constante

```

:::tip devemos realmente focar em micro-otimizações?

Algumas micro-otimizações são tão simples de serem realizadas (como a citada acima) que seu custo é praticamente
inexistente. Alguns desenvolvedores frequentemente criticam esse tipo de cuidado, mas para esses casos, tais críticas
mais revelam deficiências técnicas do que um alto raciocínio critíco.

:::

---

## Use function - try-with-resources

Use function é uma forma de simplificar ainda mais a syntax do try-with-resources.

Exemplo em java:

```kotlin
try (var example = new BufferedReader(new FileReader("./path/to/file"))) {
    System.out.println(example.readLine());
} catch (IOException e) {
    throw new RuntimeException(e);
}
```

Uso do Use Function:

```kotlin
val example = BufferedReader(FileReader("./path/to/file"))
example.use {
    println(it.readLines())
}

## ou
BufferedReader(FileReader("./path/to/file")).use { 
    it.readLine() 
}
```

---

## Call Suffix - Trailing Lambda

Você já deve ter se deparado com essa syntax:

```kotlin

method(valor) { print... } <-- Call Suffix

```

Essas são as "call suffix" ou "trailing lambda".
Você consegue reproduzir esse modelo colocando o ultímo parâmetro de um método como um lambda:

```kotlin
fun method(valor: String, function: (String) -> Unit) {
    function.invoke(valor)
}

fun main() {
    method("Hello World") { println(it) }
}
```

---

## Type Alias

Type Alias é um recurso que nos permite criar "alias", uma segunda forma de chamar um elemento. Veja o seguinte exemplo:

```kotlin

typealias Cpf = String
typealias Endereco = String
typealias Nome = String

fun main() {
    val cpf: Cpf = ""
    val endereco: Endereco = ""
    val nome: Nome = ""
}

```

Note que:

- não criei nenhuma nova classe, todos eles são apenas formas extras de chamar o objeto String. Esse recurso pode ser
  bastante utíl para criação de DSLs.

---
:::danger em desenvolvimento

- scopes functions
- repeat function
- tailrec
- @Synchronized e @Volatile
- onEach vs forEach
- sequences
- require e check
- operator overloading
- extensions
- varargs
- inline functions
- closure functions

:::