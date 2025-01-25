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

Ou podemos utilizar no código:
```java
System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "4");
```

---
### Virtual Threads - defifindo tamanho do thread pool padrão

Podemos alterar o tamanho padrão do thread pool do virtual thread, para isso utilizamos a opção:
```shell
-Djdk.virtualThreadScheduler.parallelism=n
```

Ou podemos utilizar no código:
```java
System.setProperty("jdk.virtualThreadScheduler.parallelism", "4");
```