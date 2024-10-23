---
sidebar_position: 2
---

# Kotlin - Dicas e truques

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

Se compilarmos essa classe, veremos que o kotlin irá transformar qualquer acesso ao VALOR001 em uma chamada via 
"get method", **tornando seu acesso mais pesado**.

Para verificar esse comportamento mais de perto, podemos analisar o código gerado:

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

Também é provável que a JVM aproveite a natureza imutável e estática do VALOR002 para aplicar otimizações "inline" em
tempo de execução:

```kotlin

// levando a linha:
println(Constants.VALOR002)

// ser transformada em algo similar a:
println("valor_002") // <-- removendo qualquer forma de acesso pelo valor integral da constante

```

Pelo simples fato de não haver um método nesse processo, faz com que o fluxo seja mais rápido. 


:::tip então tenho que remover todos os gets e sets? 

Podemos pensar: já que métodos get e set são prejudiciais, então um código orientado a objetos é extremamente lento, certo?
Não, a boa notícia é que você não precisa deixar de lado seus gets e sets, por padrão a JVM realiza uma otimização em 
tempo de execução chamada "inline". Essa otimização visa remover os métodos gets e sets simples por uma referência direta:

```kotlin

// levando esse código:
objeto.setMethod(valor)

// ser substituido por esse:
objeto.method = valor

```

Legal, né? Tenha em mente que essa otimização acontecerá independente se o atributo for privado ou publico, só existe um 
único mecanismo real de encapsulamento na JVM, que são os módulos.

Por fim, o que fizemos aqui foi só agilizar esse processo com as contantes.

:::



---
## Closure functions

Dentro do paradigma orientado objetos armazenamos o estado dentro do objeto.
E no paradigma funcional? Podemos armazenar em funções? Sim! Bizarro, mas sim!

Essas são as closures, veja um exemplo:

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

Você já deve ter se deparado com essa syntax:
```kotlin

method(valor) { print... } <-- Call Suffix

```

São as "call suffix", e para criar um exemplo similar basta definirmos o último parâmetro do método como uma função:

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

Type Alias é um recurso que nos permite criarmos "alias" (similar ao que temos no linux). 
Um "alias", em resumo, é uma forma de fazer com que um "algo" chamado "X", também possa ser chamado por A, B, C, D, etc.

Veja o seguinte exemplo:

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
 
Note que não criei nenhuma nova classe, todos eles são apenas formas extras de chamar o objeto String. 
Esse recurso pode ser bastante utíl para criação de DSLs.

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

:::