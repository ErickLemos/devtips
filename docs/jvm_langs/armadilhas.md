---
sidebar_position: 6
---

# Armadilhas e dicas gerais

---
## WildCard imports e Sugar Syntax

Veja o seguinte trecho de código:
```kotlin

import org.example.api.*

```

Você considera-o menos performático? Algumas pessoas dirão que sim, afinal, estou importando tudo que está dentro do pacote
"api"! Porém, isso não é verdade, não há problema nenhum aqui.

Java assim como outras linguagens possuem recursos estéticos chamados de "Syntactic Sugars" ou "Sugar Syntax", como o nome
aparenta, eles servem apenas para deixar seu código mais bonito, **não causando nenhum impacto no desempenho da sua aplicação.**

Isso apenas evita o desenvolvedor de escrever um syntax parecida com essa:
```kotlin
fun main(): org.example.api.ClasseInterna {
....
}
```

:::danger em desenvolvimento...

- parallelStream

:::