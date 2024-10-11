---
sidebar_position: 2
---

# Kotlin - Dicas e truques

## Closure functions

Dentro do paradigma orientado objetos, armazenamos o estado dentro do objeto.
Mas também podemos armazenar estados dentro de funções (paradigma funcional), essas são as closures, veja um exemplo:

```kotlin

fun counter(): () -> Int {
    var i = 0
    return { i++ }
}

fun main() {
    val counterVal = counter()
    println(counterVal())
    println(counterVal())
    println(counterVal())
    println(counterVal())
}

```

```text

# SAIDA
# 0
# 1
# 2
# 3

```

- Desde que você mantenha a referência da função "counter" (counterVal) o estado será mantido.

---
## Use function - try-with-resources

Use function é uma forma de simplificar ainda mais a syntax do try-with-resources, veja um exemplo:
```kotlin
val example = BufferedReader(FileReader("./path/to/file"))
example.use {
    println(it.readLines())
}
```

---
## Call Suffix - Trailing Lambda

Você já deve se deparado com essa syntax:
```kotlin

method(valor) { print... } <-- Call Suffix

```

Para criar um exemplo similar basta definirmos o último parâmetro do método como uma função:

```kotlin
fun method(valor: String, function: (String) -> Unit) {
    function.invoke(valor)
}

fun main() {
    method("Hello World") { println(it) }
}
```

---
## Constantes - const

Você já deve ter se perguntado qual a diferença de uma const para uma variável comum e por qual motivo devo utilizar?
A resposta é: forma de acesso e seu impacto no desempenho.

Veja o seguinte exemplo:

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

Quando compilamos esse código o kotlin transforma qualquer acesso ao VALOR001 em uma chamada via "get method", 
**tornando seu acesso mais pesado**. 
Podemos tirar a prova disso analisando o código gerado:

```shell
javap -p Constants.class

Compiled from "Main.kt"
public final class org.example.Constants {
  public static final org.example.Constants INSTANCE;
  private static final java.lang.String VALOR001; <-- note aqui, foi definido como "private"
  public static final java.lang.String VALOR002;
  private org.example.Constants();
  public final java.lang.String getVALOR001(); <-- e aqui, seu método get 

```

Note também que o VALOR002 foi definido como publico e estático, não tendo nenhum método intermediário. 

É muito provável que a JVM aproveite a natureza imutável e estática do VALOR002 para aplicar otimizações de "inline" em
tempo de execução:

```kotlin

// levando a linha:
println(Constants.VALOR002)

// se transformada em algo similar a:
println("valor_002") // <-- removendo qualquer forma de acesso pelo valor integral da constante

```

---
:::danger em desenvolvimento

- scopes functions
- repeat function
- tailrec
- @Synchronized e @Volatile
- onEach vs forEach
- sequences
- require e check
- type alias
- operator overloading
- extensions
- const
- varargs
- inline functions

:::