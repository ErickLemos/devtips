---
sidebar_position: 3
---

# Java Memory Heap

Java Memory Heap é um dos subsistemas gerenciados pela JVM, sua função principal é gerenciar a memória da JVM.

## Definindo o tamanho de heap

Quando não definimos explicitamente as configurações que a JVM deve seguir, ela fará sozinha, esse processo é chamado de
[Java Ergonomics](https://docs.oracle.com/en/java/javase/22/gctuning/ergonomics.html). Veja como será definido o uso de
mémoria por padrão:

```text
- Initial heap size of 1/64 of physical memory 
- Maximum heap size of 1/4 of physical memory -- máximo de RAM usada será de apenas 1/4.
```

Isso é bastante prejudicial em um ambiente conteinerizado, já que não iriamos utilizar todo o recurso que liberamos para
o container. Então, para remediar esse problema, usamos os seguintes parâmetros:

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
Felizmente, Java possui suporte a essa tecnologia e podemos tirar proveito para conseguir mais velocidade para nossas
aplicações.

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

Então você pode habilitar o NUMA com a opção:

```shell
-XX:+UseNUMA
```

---

## Deduplicação de objetos string

A deduplicação é uma feature da JVM que evita o armazenamento de objetos String iguais, assim, reduzindo o uso de
mémoria
da sua aplicação.

Você pode habilitar a deduplicação de string utilizando a opção:

```shell
-XX:+UseStringDeduplication
```

:::warning

Habilitar a deduplicação irá diminuir o uso de mémoria da sua aplicação, em contrapartida, irá aumentar o uso de CPU.

:::

---

## Memória virtual e física - AlwaysPreTouch

A JVM aloca o heap na memória virtual em vez da memória física, então, gradualmente alocará as paginas na memória
física. Podemos alterar esse comportamento forçando o Java a alocar o heap diretamente na memória física, para isso
utilizamos a opção:

```shell
-XX:+AlwaysPreTouch
```

:::danger melhoria de desempenho em troca de tempo de inicialização

Utilizar o parâmetro "AlwaysPreTouch" irá melhorar o desempenho da sua aplicação, em contrapartida, o tempo de
inicialização
pode ser severamente afetado. Um bom uso para esse parâmetro é utilizar ele em testes de estresse, onde antecipadamente,
logo na inicialização, replicamos o cenário produtivo onde a JVM já alocou todo o heap na memória física.

:::

---
:::danger em desenvolvimento

- Java Large Pages
- Head Dump e Head Dump Path
- Resize Policy

:::

