---
sidebar_position: 1000
published: false
---

# Paralelismo e concorrência 

## Virtual Threads - formas básicas de utilização

```java
void main() throws InterruptedException {
   Thread vThread = Thread.startVirtualThread(() -> {
          System.out.println("Virtual threads make concurrency effortless! See for yourself.");
   });
   vThread.join();
}
```

:::tip Daemon por padrão

Virtual Threads são daemons por padrão (utilizar o método "setDaemon" não terá efeito algum), isso significa que a JVM 
não irá esperar sua conclusão, podendo ser encerrados abruptamente. No exemplo anterior utilizei o "join()" para esperar 
sua conclusão e a mensagem ser exibido, caso contrário seria possível que a aplicação fosse encerrada antes disso.

:::

Podemos criar um virtual thread sem precisar executá-lo imediatamente:
```java
var unstartedThread = Thread.ofVirtual()
       .unstarted(() -> System.out.println("Hello world!"));

unstartedThread.start();
```

Podemos também utilizar o Executors:
```java
try (var virtualExecutor = Executors.newVirtualThreadPerTaskExecutor()) { 
   Future<String> future = virtualExecutor.submit(this::fetchExternalServiceResponse);
}
```

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
## Virtual Threads - defifindo tamanho do thread pool padrão

Podemos alterar o tamanho padrão do thread pool do virtual thread, para isso utilizamos a opção:
```shell
-Djdk.virtualThreadScheduler.parallelism=n
```

Ou podemos utilizar no código:
```java
System.setProperty("jdk.virtualThreadScheduler.parallelism", "4");
```

:::warning Spring, Quarkus e similares

Defina a configuração antes da inicialização do contexto da aplicação. 

:::