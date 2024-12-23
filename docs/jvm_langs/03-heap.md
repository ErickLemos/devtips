---
sidebar_position: 3
---

# Java Memory Heap

Java Memory Heap é um dos subsistemas gerenciados pela JVM, a sua função é gerir o uso de memória da JVM.

## Definindo o tamanho de heap

Por padrão o java não **irá utilizar toda a mémoria disponível**, nem mesmo metade disso, mas sim seguirá o processo do 
[Java Ergonomics](https://docs.oracle.com/en/java/javase/22/gctuning/ergonomics.html). 
Por esse motivo é de **suma importância definirmos manualmente a quantidade de memória que queremos que a JVM utilize, caso 
contrário, vamos acabar pagando por recursos que nunca serão utilizados.**

Podemos definir o tamanho do heap utilizando os seguintes parâmetros:
```shell
# primeiro modo - estático:
-Xms512m # tamanho inicial
-Xmx4g # tamanho máximo, até onde irá expandir

# segundo modo - por percentual:
-XX:InitialRAMPercentage=10 # tamanho inicial
-XX:MaxRAMPercentage=70 # tamanho máximo, até onde irá expandir
```

:::danger XX:MinRAMPercentage - um erro de nomenclatura

Há outro parâmetro chamado -XX:MinRAMPercentage, que apesar do seu nome, não tem a função de definir o tamanho
mínimo do heap, mas sim o tamanho máximo para heaps inferiores a 200Mb (estranho, eu sei).
[Veja mais sobre](https://www.baeldung.com/java-jvm-parameters-rampercentage.).


:::

---
## Habilitando NUMA

Em resumo, Numa é uma tecnologia que visa reduzir a latência do acesso da CPU à mémoria. 
Felizmente, Java possui suporte a essa tecnologia e podemos tirar proveito para conseguir mais velocidade para nossas aplicações.


Antes de habilitarmos o NUMA, precisamos verificar se a maquina atual possui suporte:
```shell
numactl --hardware
```

Você deve receber uma saída similar a essa:
```text
available: 1 nodes (0)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27
node 0 size: 15755 MB
node 0 free: 1747 MB
node distances:
node   0 
  0:  10 
```

:::tip preciso instalar o numactl?

Não, a grande maioria das distribuições linux já vem com o numactl incluso.

:::

Então você pode habilitar o NUMA com a tag:
```shell
-XX:+UseNUMA
```

---
## Deduplicação de objetos string

A deduplicação é uma feature da JVM que evita o armazenamento de objetos String iguais, assim, reduzindo o uso de mémoria
da sua aplicação.

Você pode habilitar a deduplicação de string utilizando a tag:
```shell
-XX:+UseStringDeduplication
```

:::warning

Habilitar a deduplicação irá diminuir o uso de mémoria da sua aplicação, em contrapartida, irá aumentar o uso de CPU.

:::

---
## Memória virtual e física - AlwaysPreTouch

A JVM aloca o heap na memória virtual em vez da memória física, então, gradualmente ela irá alocar as paginas na memória física. 
Podemos alterar esse comportamento forçando o Java a alocar o heap diretamente na memória física, para isso utilizamos a tag:

```shell
-XX:+AlwaysPreTouch
```

:::danger melhoria de desempenho em troca de tempo de inicialização

Utilizar o parâmetro "AlwaysPreTouch" irá melhorar o desempenho da sua aplicação, em contrapartida, o tempo de inicialização 
pode ser severamente afetado. Um bom uso para esse parâmetro é utilizar ele em testes de estresse, onde antecipadamente,
logo na inicialização, replicamos o cenário produtivo onde a JVM já alocou todo o heap na memória física.

:::

---
:::danger em desenvolvimento

- Java Large Pages
- Head Dump e Head Dump Path
- Resize Policy

:::

