---
sidebar_position: 4
---

# Garbage Collector

## Selecionando o GC

Limpeza de objetos não utilizados é a principal função do GC, porém, essa operação pode causar danos severos ao desempenho geral da aplicação.
Selecionar o coletor de lixo apropriado é indispensável, caso não faça, a JVM seguirá o processo do [Java Ergonomics](https://docs.oracle.com/en/java/javase/22/gctuning/ergonomics.html)
selecionando um coletor automaticamente.

Podemos selecionar os seguintes GCs:

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

### Qual GC escolher?

Para a maioria dos casos utilize o G1GC ou ZGC, eles são mais novos e possuem formas mais avançadas e performáticas de lidar com o heap.
Também são as principais recomendações para aplicações que possuem requisitos de tempo (onde as operações devem estar na casa dos milissegundos. Aplicações Rest, por exemplo).

São várias as técnicas aplicadas, mas a mais relevante é a regionalização, onde o GC "quebra" a mémoria em várias regiões. 
Essa quebra permite o GC pausar apenas regiões especificas quando precisar realizar a coleta de lixo, evitando pausas STW completas e 
melhorando a coleta paralela. 

Minha recomendação é iniciar com o G1GC e alterar caso sinta necessidade. Apesar de mais novo, o ZGC possui um ecossistema mais complexo e por consequência, mais difícil de gerenciar.

Você pode conferir mais detalhes na [documentação oficial](https://docs.oracle.com/en/java/javase/17/gctuning/available-collectors.html)


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

Por padrão o G1GC "quebra" a mémoria em várias regiões, essa estratégia permite o GC paralelizar seu trabalho, atuando sobre uma região sem afetar outra. 

Para alterar o tamanho da região, utilize:

```shell
-XX:G1HeapRegionSize=TAMANHO
```

---
## GC Threads

JVM realiza um ótimo trabalho gerenciando os Threads do GC, mas é possível alterar essas configurações.

Para número de threads paralelos, utilize:
```shell
-XX:ParallelGCThreads=n
```

:::info impacto

Esses threads serão utilizados na fase de coleta, aumentá-los irá melhorar os tempos do GC em troca de maior uso 
da CPU.

:::

Para número de threads paralelos para a fase de marcação, utilize:
```shell
-XX:ConcGCThreads=n
```

:::info impacto

Esses threads serão utilizados na fase de marcação (onde o GC marca os objetos que continuam sendo utilizados, aqueles
que não são marcados, serão removidos). Aumentá-los irá melhorar o desempenho da sua aplicação em trocar de maior uso 
da CPU.

:::

---
:::danger em desenvolvimento

- Desabilitar o GC explicito

:::