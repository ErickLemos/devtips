---
sidebar_position: 1
---

# Kotlin - Design Patterns

Alguns dos designs patterns que aprendemos no Java são diferentes quando 
aplicados no Kotlin. Por conta do seu design de linguagem, o Kotlin reduz 
a complexidade da implementação e até remove a necessidade de algumas soluções.

:::info

Vou evitar explicar em profundidade cada Design Patterns.
Considero que você já tenha conhecimento deles, seus usos e benefícios.

:::

## Creational Patterns

---
### Builder

#### Problema

Precisamos facilitar a criação de uma classe com muitos parâmetros.

#### Solução

Diferente do Java, o Kotlin não precisa de nenhuma classe extra para isso. 
O seu modelo de construtor remove a necessidade de Builders como vemos no Java, 
isso ocorre de duas formas: valores padrões e referência.

Veja o seguinte exemplo:
```kotlin

class Example(
    var value01: String,
    var value02: Int = 0,
    var value03: Boolean = false
)

# Utilizando
val example = Example("example1")

val example02 = Example(
    value03 = true,
    value02 = 10,
    value01 = "example02"
)

```

- Valores não declarados são preenchidos com os valores padrões. 
- É possível alterar a ordem dos parâmetros.

#### EXTRA - Fluent Setters

Você também pode obter um resultado similar ao que temos no Java utilizando o
que chamamos de "Fluent Setters", veja um exemplo:

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

- O "apply" retorna o objeto atual, isso permite ter uma implementação similar ao Builder comum.

---
### Singleton

#### Problema

Singletons são classes que possuem apenas uma única instância.

#### Solução

Podemos solucionar este problema utilizando os "objects", [veja um pouco mais
sobre eles](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview)

```kotlin

object SingletonExample {
    fun printLn(value: String) {
        println(value)
    }
}

```

---
### Prototype

#### Problema

Você gostaria de copiar objetos alterando atributos específicos. Ex:
Example(10, 10, 10) -> ExampleCopia(10, 11, 10).

#### Solução

Uma forma simples de chegar a esse resultado é utilizar o método ".copy" integrado
nas "data class".

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

obs: Note que este método só está disponível para "data class".


:::danger em desenvolvimento

Structural Patterns
- decorator
- adapter

:::