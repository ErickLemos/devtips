---
sidebar_position: 1
---

# Kotlin Design Patterns

Alguns dos designs patterns que aprendemos no Java são diferentes quando 
aplicados ao Kotlin. Por conta do seu design de linguagem, o Kotlin reduz 
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
    val value01: String,
    val value02: Int = 0,
    val value03: Boolean = false
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

## Structural Patterns

### Decorator

### Adapter