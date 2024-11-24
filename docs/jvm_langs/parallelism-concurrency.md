---
sidebar_position: 1000
published: false
---

# Paralelismo e concorrência 

---
## ForkJoinApi - definindo tamanho do thread pool padrão

Podemos alterar o tamanho padrão do thread pool da api de ForkJoin, para isso utilizamos a opção:
```shell
-Djava.util.concurrent.ForkJoinPool.common.parallelism=n
```