---
sidebar_position: 1
---

# Dicas gerais e armadilhas

---
## Sempre atualize a sua versão LTS

Diferente de que muitos desenvolvedores acreditam, atualizar a versão da sua JVM é muito mais do que adicionar mais recursos
no nível de linguagem para facilitar o desenvolvimento. Apesar da linguagem caminhar a passos lentos na adoção de novos 
recursos, isso não é verdade para a JVM, cada release traz uma enxurrada de otimizações e novos recursos que muitas vezes
não vão ser descritos em artigos que encontramos por aí na internet.

Se que melhorar o desempenho da sua aplicação, atualize a versão!
Será o caminho mais curto e fácil que pode optar.

:::tip exemplo - Java 9 e instruções PAUSE

Java 9 foi a primeira versão a receber o [JEP 285](https://openjdk.org/jeps/285), que visa resolver melhorar o polling 
ativo dos threads, cenário onde um thread bloqueia um recurso e as outras threads ficam esperando sua vez, levando essas 
mesmas threads realizar uma pesquisa continua para verificar se a sua vez chegou. O problema é que essa pesquisa acaba 
consumindo recursos de CPU e energia.

Para resolver esse problema foi utilizado como base a nova instrução PAUSE introduzida pela intel no [SSE2](https://www.intel.com/content/www/us/en/support/articles/000005779/processors.html),
permitindo então que a JVM a invocasse diretamente.

![Alt text](./imgs/spin-wait.png "grafico-spin-wait")

:::

---
## WildCard imports e Sugar Syntax

Veja o seguinte trecho de código:
```kotlin

import org.example.api.*

```

Você considera-o menos performático? Algumas pessoas dirão que sim, afinal, estou importando tudo que está dentro do pacote
"api"! Porém, isso não é verdade, não há problema nenhum aqui.

Java assim como outras linguagens possuem recursos estéticos chamados "Syntactic Sugars" ou "Sugar Syntax", como o nome
aparenta, eles servem apenas para deixar o seu código mais bonito, **não causando nenhum impacto no desempenho da sua aplicação.**

Isso apenas evita o desenvolvedor de escrever um syntax parecida com essa:
```kotlin
fun main(): org.example.api.ClasseInterna {
....
}
```

## Curiosidades

:::tip motivo do nome bytecode

O código de operação de máquina de pilha (opcode) é representado por um byte, por isso o nome bytecode.
Por esse mesmo motivo o conjunto total de operações permitidas são de apenas 255, que atualmente algo em torno de 200+ 
já estão em uso no Java 23.

:::