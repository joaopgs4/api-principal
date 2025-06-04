## Objetivo

Fazer um microserviço de exchange para nossa api que faça requisição para um serviço terceiro utilizando python e fastAPI

## Montagem do Exercicio

Para a realizaçao deste exercicio, precisamos primeiro criar nossa aplicação e testa-la, uma vez funcionando adicionamos ao compose da API

### Criação Exercicio

Clone o repositório com o exercicio pronto para ver a estrutura de arquivos escolhida, mas ela se resume em uma pasta "app" com todos os arquivos .py, sendo eles o main.py e um routers.py com nossas rotas e seus códigos respectivos.

=== "main.py"

    ``` { .py .copy .select linenums='1' title="main.py" }
    --8<-- "https://raw.githubusercontent.com/joaopgs4/exchange-service/refs/heads/main/app/main.py"
    ```

=== "routers.py"

    ``` { .py .copy .select linenums='1' title="routers.py" }
    --8<-- "https://raw.githubusercontent.com/joaopgs4/exchange-service/refs/heads/main/app/routers.py"
    ```


### Criação dockerfile

Para este dockerfile ser adequado com o fast-api, utilizamos um script uvicorn.sh para iniciar a instancia de python corretamente e um requirements.txt com todas as dependencias necessarias.

=== "dockerfile"

    ``` { .copy .select linenums='1' title="dockerfile" }
    --8<-- "https://raw.githubusercontent.com/joaopgs4/exchange-service/refs/heads/main/dockerfile"
    ```

=== "uvicorn.sh"

    ``` { .sh .copy .select linenums='1' title="uvicorn.sh" }
    --8<-- "https://raw.githubusercontent.com/joaopgs4/exchange-service/refs/heads/main/uvicorn.sh"
    ```

=== "requirements.txt"

    ``` { .txt .copy .select linenums='1' title="requirements.txt" }
    --8<-- "https://raw.githubusercontent.com/joaopgs4/exchange-service/refs/heads/main/requirements.txt"
    ```

### Rotas:
!!! info "GET /exchange/{from}/{to}"

    Get the current of a coin from one currency to another. E.g. `GET /coin/USD/EUR`.

    === "Response"

        ``` { .json .copy .select linenums='1' }
        {
            "sell": 0.82,
            "buy": 0.80,
            "date": "2021-09-01 14:23:42",
            "id-account": "0195ae95-5be7-7dd3-b35d-7a7d87c404fb"
        }
        ```
        ```bash
        Response code: 200 (ok)
        ```