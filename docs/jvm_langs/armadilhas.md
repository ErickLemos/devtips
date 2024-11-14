---
sidebar_position: 6
---

# Armadilhas e dicas gerais

---
## Sempre atualize a sua versão LTS

Diferente de que muitos desenvolvedores acreditam, atualizar a versão da sua JVM é muito mais do que adicionar mais recursos
no nível de linguagem para facilitar o desenvolvimento. Apesar da linguagem caminhar a passos lentos na adoção de novos 
recursos, isso não é verdade para a JVM, cada release traz uma enxurrada de otimizações e novos recursos que muitas vezes
não vão ser descritos em artigos que encontra por aí na internet.

Se que melhorar o desempenho da sua aplicação, atualize a versão!
Será o caminho mais curto e fácil que pode optar.

---
## WildCard imports e Sugar Syntax

Veja o seguinte trecho de código:
```kotlin

import org.example.api.*

```

Você considera-o menos performático? Algumas pessoas dirão que sim, afinal, estou a importar tudo que está dentro do pacote
"api"! Porém, isso não é verdade, não há problema nenhum aqui.

Java assim como outras linguagens possuem recursos estéticos chamados "Syntactic Sugars" ou "Sugar Syntax", como o nome
aparenta, eles servem apenas para deixar o seu código mais bonito, **não causando nenhum impacto no desempenho da sua aplicação.**

Isso apenas evita o desenvolvedor de escrever um syntax parecida com essa:
```kotlin
fun main(): org.example.api.ClasseInterna {
....
}
```

:::danger em desenvolvimento...

- parallelStream

:::