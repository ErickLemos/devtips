---
sidebar_position: 2
---

# Garbage Collector

## Selecionando GC

```shell

# habilitando SerialGC
-XX:+UseSerialGC

# habilitando ParallelGC
-XX:+UseParallelGC

# habilitando G1GC
-XX:+UseG1GC

# habilitando ZGC
-XX:UseZGC
```

:::info qual algoritmo gc escolher?

G1GC e ZGC são os algoritmos mais novos, eles lidam com o heap utilizando técnicas e estratégias mais avançadas do que
o SerialGC e ParallelGC. São recomendados para aplicações que possuem requisitos de tempo (aplicações Rest, por exemplo),
mas podem beneficiar outros tipos de aplicações.

São várias as técnicas aplicadas, mas a mais relevante é a regionalização, onde o GC "quebra" a mémoria em várias regiões. 
Essa quebra permite o GC pausar apenas regiões especificas quando precisar realizar a coleta, evitando pausas STW e 
melhorando a coleta paralela.

Você pode conferir mais detalhes na [documentação oficial](https://docs.oracle.com/en/java/javase/17/gctuning/available-collectors.html)

:::

---
## Tempo de pausa

Para informar ao GC quanto tempo ele deve levar em cada coleta, utilize:

```shell
-XX:MaxGCPauseTimeMillis=MILISEGUNDOS 
```

:::warning

Este parâmetro vai alterar diretamente como o GC se comporta, podendo levar a melhora ou piora do desempenho.
Utilize com cuidado e mantenha uma suíte de testes para validar suas alterações.

:::

---
## Alterando tamanho das regiões do G1GC

Por padrão o G1GC "quebra" a mémoria em várias regiões, essa estratégia permite o GC trabalhar em cima de uma região 
sem afetar outra. Para alterar o tamanho da região, utilize:

```shell
-XX:G1HeapRegionSize=TAMANHO
```

---
## GC Threads

JVM realiza um ótimo trabalho gerenciando os Threads do GC, mas é possível alterar essas configurações para obter um
melhor desempenho para sua aplicação.

Para número de threads paralelos, utilize:
```shell
-XX:ParallelGCThreads=n
```

Para número de threads paralelos para a fase de marcação, utilize:
```shell
-XX:ConcGCThreads=n
```

---
## Referências

- [Oracle Oficial Documentation](https://www.oracle.com/technical-resources/articles/java/g1gc.html)
- JVM Performance Engineering: Inside OpenJDK and the HotSpot Java Virtual Machine by Monica Beckwith