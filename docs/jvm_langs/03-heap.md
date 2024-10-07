---
sidebar_position: 3
---

# Java Memory Heap

## Definindo uso de memória

Por padrão o java não irá utilizar toda a mémoria disponível, nem mesmo metade disso, mas sim seguirá o processo do 
[Java Ergonomics](https://docs.oracle.com/en/java/javase/22/gctuning/ergonomics.html). 
Por esse motivo é de suma importância definirmos manualmente a quantidade de memória que queremos que a JVM utilize.


Temos dois modos para definir a quantidade de memória que sua aplicação deve utilizar, são eles:

Modo estático:
```shell
-Xms512m # tamanho inicial
-Xmx4g # tamanho máximo, até onde irá expandir
```

Modo dinâmico:
```shell
-XX:InitialRAMPercentage=10 # tamanho inicial
-XX:MaxRAMPercentage=70 # tamanho máximo, até onde irá expandir
```

:::warning XX:MinRAMPercentage - um erro de nomenclatura

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
:::danger em desenvolvimento

- Java Large Pages
- String Deduplication
- Head Dump e Head Dump Path
- Resize Policy

:::