---
sidebar_position: 1000
title: Docker e Kubernetes
---


## Aplicação spring não recebendo sinais SIGTERM

Tenha cuidado com a forma com que você inicializa sua aplicação dentro do seu Dockerfile:
```dockerfile
CMD ["sh", "-c", "java -jar app.jar"]
# OU
CMD java -jar app.jar
```
Essas duas formas podem apresentar problemas para repassar os sinais SIGTERM.
Isso acontece porque essas abordagens iniciam um processo de shell como o processo principal (PID 1) do contêiner.
A maioria dos shells não repassa sinais do sistema (como o SIGTERM) para seus processos filhos.
Como resultado, a aplicação nunca é notificada á desligar.

:::tip solução

Para garantir que a aplicação Java seja o processo principal (PID 1), usando a "forma exec" do CMD ou ENTRYPOINT. Assim, ela recebe o SIGTERM diretamente.
```dockerfile
ENTRYPOINT [ \
    "java", \
    "-jar", \
    "app.jar" \
]
```

:::