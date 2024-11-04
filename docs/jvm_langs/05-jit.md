---
sidebar_position: 6
---

# Just Time Compilation

---
## Visualizar compilações

Para visualizar as compilações, utilizamos:
```shell
-XX:+PrintCompilation
```

Para obter mais detalhes, utilizamos:
```shell
-XX:+UnlockDiagnosticVMOptions
-XX:+LogCompilation
```
Esta opção fará que a JVM gere um arquivo XML com detalhes mais profundos dos eventos de compilação. Para facilitar sua
leitura, utilize a ferramenta: JIT Watch.

---
## Reserved Code Cache

Code Cache é uma área do heap onde a JVM irá armazenar o código compilado. Seu tamanho é fixo, e por conta disso é possível
que sua aplicação consiga enchê-lo, levando a problemas de desempenho, evitando que sua aplicação atinja seu potêncial máximo.

Felizmente podemos alterar seu tamanho com a tag:
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


:::danger em desenvolvimento

- thresholds

:::