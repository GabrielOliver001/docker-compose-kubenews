# Kube-news: Exemplo de Aplicação em NodeJS com Containers

O **Kube-news** é uma aplicação de exemplo criada com o objetivo de demonstrar o uso de containers em projetos. A aplicação é desenvolvida em Node.js e utiliza o banco de dados PostgreSQL.

## Objetivo
O projeto é ideal para aprender e testar conceitos de contêineres, como:
- Configuração de redes entre contêineres.
- Uso de volumes para persistência de dados.
- Configuração de variáveis de ambiente para integração entre contêineres.

---

## Requisitos

- **Docker**: Certifique-se de ter o Docker instalado em sua máquina. 
- **PostgreSQL**: Não é necessário instalar localmente, pois será executado em um contêiner.

---

## Configuração da Aplicação

A aplicação depende de algumas variáveis de ambiente para configurar o acesso ao banco de dados:

- **DB_DATABASE**: Nome do banco de dados que será usado.  
- **DB_USERNAME**: Usuário do banco de dados.  
- **DB_PASSWORD**: Senha do usuário.  
- **DB_HOST**: Endereço do banco de dados (nome do contêiner no caso de redes Docker).  

---

## Como Configurar

### 1. Criação do Dockerfile da Aplicação

```dockerfile
FROM node:22.12.0-alpine3.20
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
EXPOSE 8080
ENTRYPOINT [ "node", "server.js" ]
```

### 2. Criar Rede e Volume no Docker
Crie uma rede para os contêineres se comunicarem e um volume para persistência de dados do PostgreSQL:
```bash
docker network create kubenews-net
docker volume create kubenews-volume
```

### 3. Subir o Contêiner do Banco de Dados
Execute o comando abaixo para criar e iniciar o banco de dados PostgreSQL em um contêiner:
```bash
docker run -d \
  -p 5432:5432 \
  --name kubenews_db \
  -e POSTGRES_DB=kubenews \
  -e POSTGRES_USER=kubenews \
  -e POSTGRES_PASSWORD=Pg1234 \
  --network kubenews-net \
  -v kubenews-volume:/var/lib/postgresql/data \
  postgres:14.15-alpine3.20
```

### 4. Subir o Contêiner da Aplicação
Inicie o contêiner da aplicação configurando as variáveis de ambiente necessárias:
```bash
docker run -d \
  -p 8080:8080 \
  --name kubenews_app \
  --network kubenews-net \
  -e DB_DATABASE=kubenews \
  -e DB_USERNAME=kubenews \
  -e DB_PASSWORD=Pg1234 \
  -e DB_HOST=kubenews_db \
  gabrieloliver001/kube-news-docker:v1
```

---

## Testando a Aplicação

Acesse a aplicação no navegador ou com ferramentas como **cURL** ou **Postman** em:  
```
http://localhost:8080
```

![image](https://github.com/user-attachments/assets/cd18244e-fa91-4873-b3f5-5967b57e38be)
