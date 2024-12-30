# Kube-news: Aplicação Exemplo com Docker Compose

Este repositório contém a configuração de uma aplicação em **Node.js** que utiliza o **PostgreSQL** como banco de dados, implementada e gerenciada com **Docker Compose**.

---

## Objetivo

O objetivo do projeto é demonstrar conceitos essenciais de containers, incluindo:

- Configuração de redes entre containers.
- Persistência de dados utilizando volumes.
- Integração de containers com variáveis de ambiente.

---

## Estrutura do Projeto

### `compose.yaml`
Abaixo está a configuração principal do projeto:

```yaml
services:
  postgresql:
    image: postgres:14.15-alpine3.21
    ports:
      - 5432:5432
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_DB: ${POSTGRES_DB}
    volumes:
      - postgre_vol:/var/lib/postgresql/data
    networks:
      - kubenews_net
  
  kube_news:
    image: gabrieloliver001/kube-news-docker:${IMAGE_TAG:-latest}
    build:
      context: ./src
      dockerfile: Dockerfile
    ports:
      - 8080:8080
    networks:
      - kubenews_net
    depends_on:
      - postgresql
    environment:
      DB_PASSWORD: ${POSTGRES_PASSWORD}
      DB_USERNAME: ${POSTGRES_USER}
      DB_DATABASE: ${POSTGRES_DB}
      DB_HOST: postgresql

volumes:
  postgre_vol:
    name: postgre_vol

networks:
  kubenews_net:
    driver: bridge
```

---

## Requisitos

Certifique-se de ter os seguintes softwares instalados:

- **Docker**: Para criar e gerenciar containers.
- **Docker Compose**: Para gerenciar os containers definidos no arquivo `compose.yaml`.

---

## Configurando o Projeto

### 1. Variáveis de Ambiente

Crie um arquivo `.env` na raiz do projeto e defina as variáveis:

```env
POSTGRES_PASSWORD=Pg123
POSTGRES_USER=kubenews
POSTGRES_DB=kubenews
IMAGE_TAG=v1
```

### 2. Construir e Iniciar os Containers

Use os comandos abaixo para iniciar o projeto:

1. Construa e inicie os containers:
   ```bash
   docker-compose up -d --build
   ```

2. Verifique se os containers estão em execução:
   ```bash
   docker-compose ps
   ```

---

## Testando a Aplicação

A aplicação ficará disponível no navegador ou por ferramentas como **Postman** ou **cURL**:

- URL da aplicação:
  ```
  http://localhost:8080
  ```

---

## Estrutura do Dockerfile

O `Dockerfile` utilizado para construir a aplicação está configurado conforme abaixo:

```dockerfile
FROM node:22.12.0-alpine3.20
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
EXPOSE 8080
ENTRYPOINT [ "node", "server.js" ]
```

---

## Limpeza

Para remover os containers, rede e volumes criados, utilize os comandos:

1. Parar os containers:
   ```bash
   docker-compose down
   ```

2. Remover os volumes (caso não queira persistência):
   ```bash
   docker-compose down -v
   ```
