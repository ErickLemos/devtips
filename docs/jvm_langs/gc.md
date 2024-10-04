---
sidebar_position: 2
---

# Garbage Collector

Limpeza de dados inúteis é a principal função do GC, porém, essa operação pode causar danos severos a performance geral da aplicação.


Selecionar o coletor de lixo apropriado para sua aplicação é indispensável, caso não faça, a JVM seguirá o processo do Java Ergonomics, assim como na definição de uso de memória.


Para selecionar um GC, temos as seguintes opções:

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

#### Qual gc escolher?

Para a maioria dos casos utilize o G1GC ou ZGC, eles são os GCs mais novos e possuem formas mais avançadas e performáticas de lidar com o heap.
Também são as principais recomendações para aplicações que possuem requisitos de tempo (onde as operações devem estar na casa dos milissegundos. Aplicações Rest, por exemplo).

São várias as técnicas aplicadas, mas a mais relevante é a regionalização, onde o GC "quebra" a mémoria em várias regiões. 
Essa quebra permite o GC pausar apenas regiões especificas quando precisar realizar a coleta de lixo, evitando pausas STW e 
melhorando a coleta paralela. 

Minha recomendação é iniciar com o G1GC e alterar caso sinta necessidade. Apesar de mais novo o ZGC possui um ecossistema mais complexo e por consequência, mais difícil de gerenciar.

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