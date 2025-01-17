---
sidebar_position: 6
---

# Just In Time Compilation

JIT Compiler é um dos subsistemas gerenciados da JVM, sua principal função é otimizar e compilar o bytecode em tempo de 
execução. Internamente a JVM irá metrificar e selecionar quais métodos e classes são aptos a sofrerem otimizações (o mecanismo 
HotSpot é responsável por essa analíse), essas otimizações se tornarão cada vez mais agressivas (esse fluxo de otimização
também é conhecido como Tiered Compilation), restando apenas instruções assembly no final de tudo.

---
## Visualizando compilações

Para visualizar as compilações, utilize:
```shell
-XX:+PrintCompilation
```

Para obter mais detalhes, utilize:
```shell
-XX:+UnlockDiagnosticVMOptions
-XX:+LogCompilation
```
Esta opção fará que a JVM gere um arquivo XML com detalhes mais profundos dos eventos de compilação. Para facilitar sua
leitura utilize a ferramenta: JIT Watch.

---
## Reserved Code Cache

Code Cache é uma área do heap onde a JVM irá armazenar o código compilado. Seu tamanho é fixo, e por conta disso é possível
que sua aplicação consiga enchê-lo, levando a problemas de desempenho, evitando que sua aplicação atinja seu potêncial máximo.

Podemos alterar seu tamanho com a opção:
```shell
-XX:ReservedCodeCacheSize=n
```

---
## Definindo conjunto de instruções da CPU

Por padrão, a JVM irá identificar o modelo da CPU e suas especificidades, definindo o conjunto de instruções que irá utilizar.
Não há necessidade de alterar esse comportamento, mas caso queira experimentar alterar o conjunto de instruções, utilize:

```shell
-XX:UseAVX=n
```

Consulte a documentação oficial do JDK utilizado para verificar quais são os conjuntos disponíveis.

---
## Curiosidades

:::tip bytecodes e seu peso

Alguns tipos de bytecode são mais fáceis de serem otimizados e são normalmente otimizados com antecedência, bytecodes de
operações aritméticas são um exemplo.

:::

---
:::danger em desenvolvimento

- thresholds

:::