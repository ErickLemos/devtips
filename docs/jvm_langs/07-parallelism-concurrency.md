---
sidebar_position: 7
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

## Virtual Threads

Benefícios:
- muito mais leves, utilizam menos mémoria. Você pode criar milhares deles sem se preocupar em esgotar a mémoria.
- virtuais threads são gerenciados pela JVM, tendo melhor rendimento no uso da CPU.
- foram pensados para se integrar em bases de código existentes.

Desvantagens:
- processos que necessitam de afinidade com CPU podem ser prejudicados.

:::tip Não é sobre velocidade

Virtual Threads não possuem velocidade como foco, mas sim escalabilidade. O princípio por trás do seu desenvolvimento é
a lei de Little, que visa oferecer maio rendimento ao executar mais tarefas simultâneas 

:::

### Formas de utilização

Há algumas formas básicas de utilizar virtual threads, sendo elas:

Instanciar um virtual thread com inicialização automática:
```java
void main() throws InterruptedException {
   Thread vThread = Thread.startVirtualThread(() -> {
          System.out.println("Virtual threads make concurrency effortless! See for yourself.");
   });
   vThread.join();
}
```

Instanciar sem inicialização automática:
```java
var unstartedThread = Thread.ofVirtual()
       .unstarted(() -> System.out.println("Hello world!"));

unstartedThread.start();
```

E via Executors:
```java
try (var virtualExecutor = Executors.newVirtualThreadPerTaskExecutor()) { 
   Future<String> future = virtualExecutor.submit(this::fetchExternalServiceResponse);
}
```

:::tip Daemon por padrão

Virtual Threads são daemons por padrão (utilizar o método "setDaemon" não terá efeito algum), isso significa que a JVM
não irá esperar sua conclusão, podendo ser encerrados abruptamente. No exemplo anterior utilizei o "join()" para esperar
sua conclusão e a mensagem ser exibido, caso contrário seria possível que a aplicação fosse encerrada antes disso.

:::

---
### Definindo tamanho do thread pool padrão

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

---
### Problema de pinning

Em certos cenários o Virtual Thread pode ficar vinculado estritamente ao Platform Thread, causando contenção do mesmo, 
esse problema é chamado de "pinning". 
Há duas ocasiões em que você pode presenciar esse comportamento:

- Synchronized block: pinning problem ao acessar um método sincronizado.
- Native method: pinning problem ao acessar um método nativo.

Felizmente podemos contornar o primeiro cenário utilizando ReentrantLock (Há uma explicação de seu uso nesta página). Já
o segundo cenário só é resolvido com refatoração, eliminando o acesso ao método nativo.

Para verificar se nossa aplicação possui esse problema, utilizamos:

```shell
-Djdk.tracePinnedThreads=full # log de todos os eventos 
-Djdk.tracePinnedThreads=short # log dos eventos mais problemáticos
```

---
## ReentrantLock - uma alternativa ao synchronized method

Synchronized keyword não é a única forma de garantir o lock dos métodos, há também uma alternativa direta: ReentrantLock.
Sua principal diferença é a flexibilidade de utilização, adquirindo o lock e liberando de acordo com sua própria lógica,
veja um exemplo:

```kotlin
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors
import java.util.concurrent.atomic.AtomicInteger
import java.util.concurrent.locks.ReentrantLock

class Context {
    private val counter = AtomicInteger()
    private val reentrantlock = ReentrantLock() // <-- criação do ReentrantLock

    fun increment() {
        try {
            reentrantlock.lock() // <-- aquisição do lock
            counter.incrementAndGet()
            println("${Thread.currentThread().name}:${reentrantlock.isLocked} - incremented")
            Thread.sleep(2000)
        } finally {
            reentrantlock.unlock() // <-- liberação do lock
        }
    }
}

val context = Context()
val pool: ExecutorService = Executors.newFixedThreadPool(4)

repeat(4) {
    pool.submit { context.increment() }
}

pool.shutdown()
```

:::danger em desenvolvimento

- tip: pinning problem

:::

---
## Semaphore - limitar quantidade de acesso a um método

Semaphore é um utilitário que nos permite limitar o número de acesso simultâneos a um trecho de código.
Sua forma de utilização é bastante similar ao ReentrantLock:

```kotlin
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors
import java.util.concurrent.Semaphore
import java.util.concurrent.atomic.AtomicInteger

class Context {
    private var counter: AtomicInteger = AtomicInteger()
    private val semaphone: Semaphore = Semaphore(2) // <-- criação do Semaphore com quantidade de acessos permitidos

    fun increment() {
        try {
            semaphone.acquire() // <-- adquirindo uma "licença", quando não houver mais licenças o thread será bloqueado
            counter.getAndIncrement()
            println("${Thread.currentThread().name}:${semaphone.availablePermits()} - incremented")
            Thread.sleep(2000)
        } finally {
            semaphone.release() // <-- liberando a licença
        }
    }
}

val context = Context()
val pool: ExecutorService = Executors.newFixedThreadPool(4)

repeat(4) {
    pool.submit { context.increment() }
}

pool.shutdown()
```

---
:::danger em desenvolvimento

- garantindo ThreadSafe
- continuation
- tipos de thread pool e ExecutorService interface

:::
