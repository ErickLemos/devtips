---
sidebar_position: 2
---

# Kotlin - Design Patterns

Alguns dos designs patterns que aprendemos no Java são diferentes quando aplicados no Kotlin. Por conta do seu design de
linguagem, o Kotlin reduz a complexidade da implementação e até remove a necessidade de algumas soluções.

:::info

Irei evitar explicar os conceitos por trás de cada Design Patterns, considero que você já tenha prévio conhecimento
deles, seus usos e benefícios.

:::

## Creational Patterns

---

### Builder

O modelo de construtor do Kotlin remove a necessidade de Builders comuns como vemos no Java, isso ocorre de duas formas:
valores padrões e referência.

Veja o seguinte exemplo:

```kotlin

class Example(
    var value01: String,
    var value02: Int = 0,
    var value03: Boolean = false
)

# Utilizando
val example01 = Example("example1")

val example02 = Example(
    value03 = true,
    value02 = 10,
    value01 = "example02"
)

```

Note que:

- Valores não declarados são preenchidos com os valores padrões.
- É possível alterar a ordem dos parâmetros, umas das principais funções esperadas no Builder Pattern.

#### EXTRA - Fluent Setters

Também é possível obter um resultado similar (em syntax) ao que temos no Java utilizando os "Fluent Setters":

```kotlin

class Example(
    var value01: String = "",
    var value02: Int = 0,
    var value03: Boolean = false
) {
    fun value01(value: String) = apply {
        value01 = value
    }

    fun value02(value: Int) = apply {
        value02 = value
    }

    fun value03(value: Boolean) = apply {
        value03 = value
    }
}

// utilizando: 

val exampleFluentSetter = Example()
            .value01("")
            .value02(1)
            .value03(false)

```

Note que:

- O "apply" retorna o objeto atual com o valor do atributo alterado.

Limitações:

- O construtor primário ainda é utilizado na construção do objeto, se houver algum atributo sem valor padrão, ele será
  solicitado.
- Caso opte por alterar os atributos para nullables (permitindo que não sejam solicitados no construtor primário),
  você precisará garantir que a classe não entre em estado inconsistente. Nesse caso, você pode utilizar uma
  derivação do Builder Pattern, o [Step Builder Pattern](https://java-design-patterns.com/patterns/step-builder).

---

### Singleton

Kotlin adiciona o [object](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview), um novo
tipo que substitui os Singletons comuns:

```kotlin

object SingletonExample {
    fun printLn(value: String) {
        println(value)
    }
}

```

---

### Prototype

Kotlin introduz nas Data Class o método: ".copy()". Sua função é copiar um objeto, mas também é possível alterar
atributos nesse processo.

````kotlin

val example = ExampleCopy(
    value01 = 10,
    value02 = 10
)

val exampleCopy = example.copy(
    value01 = 10,
    value02 = 11
)

````

Limitações:

- Até o momento, esse método só está disponível para "data class"

:::danger em desenvolvimento

Structural Patterns

- decorator
- adapter

:::