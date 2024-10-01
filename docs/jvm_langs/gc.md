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

:::danger em desenvolvimento

- tempo de pausa e intervalo 
- alterando tamanho das regiões do G1GC

:::