---
sidebar_position: 1000
published: false
---

# Paralelismo e concorrência 

---
## ForkJoinApi - definindo tamanho do thread pool padrão

É possível alterar o tamanho padrão do thread pool da api de ForkJoin, para isso utilizamos o parâmetro:
```shell
-Djava.util.concurrent.ForkJoinPool.common.parallelism=n
```

:::tip problema na identificação

Essa função é bastante útil para o cenário onde a JVM tem dificuldade de identificar o numéro exato de processadores. 
Há um caso relatado no livro Optimizing Cloud Native Java, onde, ao executar o java num servidor com vários processadores 
(sockets), que por vez, possuem vários cores, que também possuem vários cores virtuais
(ou seja, numero de processadores * numero de núcleos físicos * numero de núcleos virtuais), a JVM acabou retornando apenas 
o número de processadores/sockets disponíveis (obs: essa não é uma falha da JVM, na verdade, é realizado uma consulta para 
o sistema operacional, que por vez retorna esse valor). 

:::