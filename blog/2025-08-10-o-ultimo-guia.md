---
title: O ultimo guia de configuração de aplicações spring do mundo
authors: ericklemos
tags: [ ]
hide_table_of_contents: false
---

Este guia tem como função ser a minha e a sua principal fonte de verdade para futuras configurações que você precise
trabalhar.

O foco deste tutorial é:

- Utilizar os melhores recursos disponíveis na JVM, em geral(GC Fine-tuning, NUMA, CDS, etc).
- Utilizar os melhores recursos disponíveis do Spring.
- Utilizar os melhores recursos disponíveis do Kubernetes e Docker.
- Ter um bom nível de observabilidade, com foco em ambientes de produção.
- Ter um bom desempenho (resultado dos testes inclusos).

<!-- truncate -->

## Pré-requisitos

Este projeto tem um repositório próprio onde você poderá consultar o resultado e
testá-lo: [link do repo](http://google.com).
Também estarei utilizando as seguintes ferramentas:

- Intellij IDEA
- Docker
- Kubernetes (k3s, distro local)
- JMC (Java Mission Control)
- Grafana K6 (para testes de carga)

## Criação e configuração da nossa aplicação

Para criar a aplicação utilizarei o [Spring Start/Initializr](https://start.spring.io/). A aplicação contém as seguintes
configurações:

- Kotlin e Gradle Kotlin (O tutorial não se prende a linguagem, com Java o fluxo é o mesmo).
- Spring Boot versão: 3.5.4.
- Java 21 (localmente estarei utilizando o vendor Azul JDK).
- Dependências do Spring: DevTools, Configuration processor, Actuator, Web, Prometheus (apenas uma preferência minha),
  Validation e Data MongoDB.

### Configurando os probes: liveness e readiness

Por padrão o Spring configura o seu path de healthcheck de forma mista (/actuator/health), é útil para usos simples,
porém podemos extrair um pouco mais dessa função habilitandoloc os additional
probes ([liveness e readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)):

```text
management.endpoint.health.probes.enabled=true
```

Agora temos os novos probes para utilizarmos no kubernetes:

- readiness: /actuator/health/readiness
- liveness: /actuator/health/liveness

### Modo de desligamento (shutdown)

Existem duas formas de desligamento: immediate e graceful. Mas o que ocorre em cada um deles?

- immediate: quando a aplicação recebe o sinal para desligar pelo docker ou kubernetes ela irá desligar-se imediatamente
  (obviamente), esse comportamento pode ser prejudicial, já que requisições que estariam sendo processadas nesse momento
  irão ser encerradas.
- graceful: quando a aplicação receber o sinal para desligar pelo docker ou kubernetes ela primeiro irá parar de receber
  novas requisições (importante ter os probes liveness/readiness habilitados), termina todos as requisições pendentes
  e então após isso ela desligar, tendo um comportamento mais resiliente.

Para habilitar o modo graceful, utilize:

```text
server.shutdown=graceful
```

:::danger MAS O SPRING JÁ NÃO FAZ ISSO POR PADRÃO?

O valor padrão pode variar dependendo da sua versão (2.x - immediate e 3.x - graceful), para evitar qualquer imprevisto
apenas altere o valor e durma bem as próximas noites.

:::

### Configuração básica do prometheus

Vamos também habilitar alguns endpoints para melhorar a observabilidade da aplicação:

```text
management.endpoints.web.exposure.include=health,info,prometheus
```

## Configurando o Dockerfile com o Class Data Sharing

Vou utilizar
a [documentação oficial do spring sobre imagens docker](https://docs.spring.io/spring-boot/reference/packaging/container-images/dockerfiles.html#packaging.container-images.dockerfiles.cds)
para configurar nosso Dockerfile com o cache CDS(Class
Data Sharing) habilitado, isso vai ajudar a termos mais desempenho na inicialização da aplicação. O resultado fica
mais ou menos assim:

```dockerfile
FROM gradle AS builder
WORKDIR /builder
COPY . .
RUN gradle build --no-daemon

FROM bellsoft/liberica-openjre-debian:24-cds AS builder-cds
WORKDIR /builder
COPY --from=builder /builder/build/libs/ultimoguia-1.0.jar application.jar
RUN java -Djarmode=tools -jar application.jar extract --layers --destination extracted

FROM bellsoft/liberica-openjre-debian:24-cds
WORKDIR /application

COPY --from=builder-cds /builder/extracted/dependencies/ ./
COPY --from=builder-cds /builder/extracted/spring-boot-loader/ ./
COPY --from=builder-cds /builder/extracted/snapshot-dependencies/ ./
COPY --from=builder-cds /builder/extracted/application/ ./

RUN java -XX:ArchiveClassesAtExit=application.jsa -Dspring.context.exit=onRefresh -jar application.jar
ENTRYPOINT ["java", "-XX:SharedArchiveFile=application.jsa", "-jar", "application.jar"]
```

:::info BENCHMARK: com e sem CDS

| Tentativas | Sem CDS | Com CDS |
|------------|---------|---------|
| Primeira   | 1.265s  | 0.803s  |
| Segunda    | 1.351s  | 0.859s  |
| Terceira   | 1.438s  | 0.895s  |

Temos um ganho médio de mais ou menos 58% nesses casos.

:::

## Configurando o Kubernetes

## Configurando a JVM