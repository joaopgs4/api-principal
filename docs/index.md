# Template de Entrega


???+ info end "Bottlenecks Realizados"

    * Grafana
    * Prometeus
    * Redis


## Aluno

1. João Sarti


!!! tip "Instruções"

    Esta é minha entrega do trabalho individual da materia de microserviços e APIs do 5 semestre de Ciência da Computação.  
    Os k8s.yaml de cada microserviço e dos checkpoints estarão em suas abas separadas, na homepage deixarei apenas o compose (que é utilizado inicialmente e posteriormente deixado de lado).  
      
    Para executar os códigos local ou globalmente, tal como utilizar o script de clonagem siga as instruções do README.md do projeto

## Entregas

- [x] Exchange API - Data 02/06/2025
- [X] Product API - Data 02/06/2025
- [X] Order API - Data 02/06/2025
- [X] Jenkins - Data 03/06/2025
- [X] MiniKube - Data 03/06/2025
- [X] Bottlenecks - Data 03/06/2025

## Diagramas
``` mermaid
flowchart LR
    subgraph api [Trusted Layer]
        direction TB
        gateway --> account
        gateway --> auth
        account --> db@{ shape: cyl, label: "Database" }
        auth --> account
        gateway --> exchange
        gateway --> product
        gateway --> order:::red
        product --> db
        order --> db
        order --> product
        %% Redis caching for product
        product e2@==> redis[(Redis Cache)]
    end

    %% Monitoring
    grafana["Grafana + Prometheus"]
    grafana e3@-.-> gateway

    %% External API and internet
    exchange --> 3partyapi@{label: "3rd-party API"}
    internet e1@==>|request| gateway

    %% Animations
    e1@{ animate: true }
    e2@{ animate: true }
    e3@{ animate: true }

    %% Styles and links
    classDef red fill:#fcc
    click order "#order-api" "Order API"
```



## Códigos Gerais

=== "Estrutura de pastas geral do projeto"

    ``` { .yaml .copy .select linenums='1' title="Diagrama de Pastas" }
    API/ (Inexistente em um cenario real, apenas para organizar no github e testes locais)
    ├── exchange-service/ (Microserviço independente)
    │   ├── app/
    │   │   ├── main.py
    │   │   └── routers.py
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   ├── uvicorn.sh
    │   └── requirements.txt
    ├── k8s (Kubernets da infra-estrutura)
    │   ├── db.yaml
    │   ├── grafana.yaml
    │   ├── prometheus-config.yaml
    │   ├── prometheus.yaml
    │   └── redis.yaml
    ├── gateway-service/ (Microserviço independente)
    │   ├── src/
    │   │   └── main/
    │   │       ├── java/...
    │   │       └── resources/
    │   │           ├── application.yml
    │   │           └── ...
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   └── pom.xml
    ├── order/ (Dependencia do microserviço)
    │   ├── src/
    │   │   └── main/
    │   │       └── java/...
    │   └── pom.xml
    ├── order-service/ (Microserviço independente)
    │   ├── src/
    │   │   └── main/
    │   │       ├── java/...
    │   │       └── resources/
    │   │           ├── application.yml
    │   │           └── ...
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   └── pom.xml
    ├── product/ (Dependencia do microserviço)
    │   ├── src/
    │   │   └── main/
    │   │       └── java/...
    │   └── pom.xml
    ├── product-service/ (Microserviço independente)
    │   ├── src/
    │   │   └── main/
    │   │       ├── java/...
    │   │       └── resources/
    │   │           ├── application.yml
    │   │           └── ...
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   └── pom.xml
    ├── account/ (Dependencia do microserviço)
    │   ├── src/
    │   │   └── main/
    │   │       └── java/...
    │   └── pom.xml
    ├── account-service/ (Microserviço independente)
    │   ├── src/
    │   │   └── main/
    │   │       ├── java/...
    │   │       └── resources/
    │   │           ├── application.yml
    │   │           └── ...
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   └── pom.xml
    ├── auth/ (Dependencia do microserviço)
    │   ├── src/
    │   │   └── main/
    │   │       └── java/...
    │   └── pom.xml
    ├── auth-service/ (Microserviço independente)
    │   ├── src/
    │   │   └── main/
    │   │       ├── java/...
    │   │       └── resources/
    │   │           ├── application.yml
    │   │           └── ...
    │   ├── k8s/
    │   │   └── k8s.yaml
    │   ├── Dockerfile
    │   ├── Jenkinsfile
    │   └── pom.xml
    ```

=== "Docker Compose (para os exercicios 1-3)"

    ``` { .yaml title="api/compose.yaml" }
    # compose.yaml
    services:
    db:
        image: postgres:latest
        hostname: db
        environment:
        POSTGRES_DB: ${POSTGRES_DB:-store}
        POSTGRES_USER: ${POSTGRES_USER:-store}
        POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-store}
        volumes:
        - postgres-data:/var/lib/postgresql/data
        networks:
        - store-project
    
    exchange:
        build:
        context: ./exchange-service
        dockerfile: Dockerfile
        networks:
        - store-project

    account:
        hostname: account
        build:
        context: ./account-service
        dockerfile: Dockerfile
        environment:
        DATABASE_HOST: db
        DATABASE_USER: ${POSTGRES_USER:-store}
        DATABASE_PASSWORD: ${POSTGRES_PASSWORD:-store}
        depends_on:
        - db
        networks:
        - store-project

    auth:
        hostname: auth
        build:
        context: ./auth-service
        dockerfile: Dockerfile
        environment:
        JWT_SECRET_KEY: ${JWT_SECRET_KEY:-yrBBgYlvJQeslzFlgX9MFZccToI2fjRFqualquercoisa}
        networks:
        - store-project

    gateway:
        hostname: gateway
        build:
        context: ./gateway-service
        dockerfile: Dockerfile
        environment:
        - LOGGING_LEVEL_STORE=${LOGGING_LEVEL_STORE:-debug}
        ports:
        - 8080:8080
        depends_on:
        - account
        - auth
        - product
        - order
        - exchange
        networks:
        - store-project

    product:
        hostname: product
        build:
        context: ./product-service
        dockerfile: Dockerfile
        environment:
        DATABASE_HOST: db
        DATABASE_USER: ${POSTGRES_USER:-store}
        DATABASE_PASSWORD: ${POSTGRES_PASSWORD:-store}
        REDIS_HOST: redis
        depends_on:
        - db
        - redis
        networks:
        - store-project

    order:
        hostname: order
        build:
        context: ./order-service
        dockerfile: Dockerfile
        environment:
        DATABASE_HOST: db
        DATABASE_USER: ${POSTGRES_USER:-store}
        DATABASE_PASSWORD: ${POSTGRES_PASSWORD:-store}
        depends_on:
        - db
        networks:
        - store-project

    prometheus:
        image: prom/prometheus:latest
        hostname: prometheus
        ports:
        - 9090:9090
        volumes:
        - prometheus-config:/etc/prometheus
        networks:
        - store-project

    grafana:
        image: grafana/grafana-enterprise
        hostname: grafana
        ports:
        - 3000:3000
        environment:
        - GF_SECURITY_ADMIN_PASSWORD=admin
        user: "472"
        volumes:
        - grafana-data:/var/lib/grafana
        - grafana-provisioning:/etc/grafana/provisioning/datasources
        depends_on:
        - prometheus
        networks:
        - store-project

    redis:
        image: redis:latest
        hostname: redis
        ports:
        - 6379:6379
        networks:
        - store-project

    volumes:
    postgres-data:
    prometheus-config:
    grafana-data:
    grafana-provisioning:

    networks:
    store-project:
        driver: bridge
    ```

    Todas as variaveis de ambiente utilizadas estão disponiveis no .env deste repositório, que foi fornecido pelo professor


## Exemplo de vídeo

Video com exemplo do projeto sendo executado em minikube e testando as rotas e autenticação

<iframe width="100%" height="470" src="https://www.youtube.com/watch?v=jRRyDczod_4" allowfullscreen></iframe>  

### Link caso o video não carregue:  
https://www.youtube.com/watch?v=jRRyDczod_4

## Referências

[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/reference/){:target='_blank'}