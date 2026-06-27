<h1 align="center">Social Media API</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-green.svg" alt="Python Version">
  <img src="https://img.shields.io/badge/FastAPI-0.104+-red.svg" alt="FastAPI Version">
</p>

<p align="center">
  <b>Uma API completa de rede social — conecte pessoas, compartilhe momentos e construa comunidades.</b>
</p>

---

### Funcionalidades principais:

- Sistema completo de autenticação JWT
- Sistema de seguir/deixar de seguir usuários
- CRUD completo de posts com imagens
- Sistema de comentários
- Sistema de likes/unlikes
- Feed personalizado de posts
- Estatísticas de seguidores e seguindo

---

## Instalação & Configuração

### Pré-requisitos

- [Python](https://python.org/) 3.10+
- [PostgreSQL](https://postgresql.org/) 14+
- [Docker](https://docs.docker.com/get-docker/) (Opcional)
- [Docker Compose](https://docs.docker.com/compose/) (Opcional)

### Tutorial rápido com Docker Compose

```bash
# Clone do repositório
git clone https://github.com/seu-usuario/social-media-api.git
cd social-media-api

# Configuração de ambiente
cp .env.example .env
# Edite as variáveis necessárias (.env)

# Execução com Docker Compose
docker-compose up -d

# Verificação dos containers
docker-compose ps

# Logs (se necessário)
docker-compose logs -f

# Para parar
docker-compose down
```

### Instalação Local

```bash
# Crie um ambiente virtual
python -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate

# Instale as dependências
pip install -r requirements.txt

# Configure o banco de dados
cp .env.example .env
# Edite as variáveis de ambiente

# Execute as migrações
alembic upgrade head

# Inicie o servidor
uvicorn app.main:app --reload
```

> **Dica:** Edite `.env` com suas credenciais do banco de dados e chave secreta JWT.

---

## Como Funciona

### Arquitetura

```mermaid
flowchart TD
    Client[Cliente / App Mobile] -->|HTTP/JWT| Nginx[Nginx<br/>Reverse Proxy / Load Balancer]

    Nginx --> API1[FastAPI - Instância 1]
    Nginx --> API2[FastAPI - Instância 2]

    API1 -->|SQLAlchemy ORM| DB[(PostgreSQL)]
    API2 -->|SQLAlchemy ORM| DB[(PostgreSQL)]

    subgraph "Módulos da API"
        Users[Users]
        Posts[Posts]
        Comments[Comments]
        Auth[Auth]
    end

    API1 --> Users
    API1 --> Posts
    API1 --> Comments
    API1 --> Auth

    API2 --> Users
    API2 --> Posts
    API2 --> Comments
    API2 --> Auth

```

- **FastAPI:** Framework moderno e performático
- **PostgreSQL:** Banco relacional para dados consistentes
- **SQLAlchemy:** ORM assíncrono para operações otimizadas
- **JWT:** Autenticação stateless e segura

### Fluxo de dados

1. Cliente faz login e recebe token JWT
2. Token é enviado em todas as requisições protegidas
3. API valida token e executa operação solicitada
4. Dados são persistidos no PostgreSQL
5. Resposta JSON é retornada ao cliente

### Tecnologias

| Tecnologia         | Motivo de escolha                    |
|-------------------|--------------------------------------|
| Python/FastAPI    | Performance async e tipagem forte    |
| PostgreSQL        | Banco relacional robusto e ACID      |
| SQLAlchemy        | ORM moderno com suporte async        |
| Pydantic          | Validação de dados automática        |
| JWT               | Autenticação stateless               |
| Alembic           | Migrações de banco versionadas       |

### Estrutura de Pastas

```
social-media-api/
├── app/                    # Código principal da API
│   ├── main.py            # Arquivo principal FastAPI
│   ├── models.py          # Modelos SQLAlchemy
│   ├── schemas.py         # Schemas Pydantic
│   ├── database.py        # Configuração do banco
│   ├── security.py        # JWT e autenticação
│   └── routers/          # Endpoints organizados
│       ├── users.py      # Rotas de usuários
│       ├── posts.py      # Rotas de posts
│       └── comments.py   # Rotas de comentários
├── migrations/            # Migrações Alembic
├── docker-compose.yml     # Orquestração Docker
├── requirements.txt       # Dependências Python
├── .env.example          # Exemplo de variáveis
└── README.md             # Este arquivo
```

---

## Documentação de Uso

### Autenticação

```http
POST /auth/login
Content-Type: application/json

{
  "username": "usuario",
  "password": "senha123"
}
```

Resposta:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "bearer"
}
```

### Criar Post

```http
POST /posts/
Authorization: Bearer <seu_token>
Content-Type: application/json

{
  "description": "Meu primeiro post!",
  "image_url": "https://exemplo.com/imagem.jpg"
}
```

### Seguir Usuário

```http
POST /users/123/follow
Authorization: Bearer <seu_token>
```

### Endpoints Principais

| Método | Endpoint                    | Descrição                      |
|--------|-----------------------------|--------------------------------|
| POST   | /users/                    | Criar novo usuário             |
| GET    | /users/{user_id}           | Obter perfil do usuário        |
| POST   | /users/{user_id}/follow    | Seguir usuário                 |
| DELETE | /users/{user_id}/follow    | Deixar de seguir usuário       |
| GET    | /users/{user_id}/posts     | Posts do usuário               |
| GET    | /users/{user_id}/followers | Seguidores do usuário          |
| GET    | /users/{user_id}/following | Usuários que ele segue         |
| POST   | /posts/                    | Criar novo post                |
| GET    | /posts/                    | Feed de posts                  |
| GET    | /posts/{post_id}           | Detalhes do post               |
| PUT    | /posts/{post_id}           | Atualizar post                 |
| DELETE | /posts/{post_id}           | Deletar post                   |
| POST   | /posts/{post_id}/likes     | Curtir post                    |
| DELETE | /posts/{post_id}/likes     | Descurtir post                 |
| POST   | /posts/{post_id}/comments  | Comentar no post               |
| GET    | /posts/{post_id}/comments  | Listar comentários             |
| GET    | /comments/{comment_id}     | Detalhes do comentário         |
| PUT    | /comments/{comment_id}     | Editar comentário              |
| DELETE | /comments/{comment_id}     | Deletar comentário             |

### Configurações Disponíveis (.env)

```env
DATABASE_URL=postgresql+asyncpg://user:password@localhost/social_db
SECRET_KEY=sua-chave-secreta-jwt-muito-segura
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
```

### Comandos Úteis

```bash
# Rodar servidor de desenvolvimento
uvicorn app.main:app --reload

# Criar nova migração
alembic revision --autogenerate -m "Descrição da mudança"

# Aplicar migrações
alembic upgrade head

# Rodar com Docker
docker-compose up -d

# Ver logs
docker-compose logs -f api
```

---

## Desenvolvimento

### Ambiente de Desenvolvimento

```bash
# Clone e configure
git clone https://github.com/seu-usuario/social-media-api.git
cd social-media-api

# Ambiente virtual
python -m venv venv
source venv/bin/activate

# Dependências
pip install -r requirements.txt

# Configuração
cp .env.example .env
# Edite as variáveis de ambiente

# Banco de dados
alembic upgrade head

# Rodar API
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### Estrutura do Banco de Dados

```sql
-- Principais tabelas
users (id, username, email, password_hash, full_name, bio, link, created_at)
posts (id, user_id, description, image_url, created_at, updated_at)
comments (id, user_id, post_id, comment, created_at)
likes (id, user_id, post_id, created_at)
follows (id, follower_id, followed_id, created_at)
```

### Como Contribuir

1. Fork este repositório
2. Crie sua branch (`git checkout -b feat/nova-funcionalidade`)
3. Faça suas mudanças e testes
4. Commit suas mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
5. Push para a branch (`git push origin feat/nova-funcionalidade`)
6. Abra um Pull Request


---
  
## Troubleshooting

**Erro de conexão com banco:**
- Verifique se PostgreSQL está rodando
- Confirme credenciais no arquivo `.env`
- Teste conexão: `psql -h localhost -U usuario -d database`

**Token JWT inválido:**
- Verifique se `SECRET_KEY` está configurada
- Token pode ter expirado (padrão: 30min)
- Certifique-se de enviar no header: `Authorization: Bearer <token>`

**Erro 422 (Validation Error):**
- Verifique se JSON está bem formatado
- Confirme se todos campos obrigatórios estão presentes
- Consulte docs automáticas em `/docs`

---

## Documentação Interativa

Após iniciar a API, acesse:

- **Swagger UI:** http://localhost:8000/docs
- **ReDoc:** http://localhost:8000/redoc
- **OpenAPI Schema:** http://localhost:8000/openapi.json

---

## Licença

Distribuído sob licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## Contribuidores

<a href="https://github.com/seu-usuario">
  <img src="https://avatars.githubusercontent.com/u/seu-id?v=4" width="50px;" alt="Seu Nome"/>
</a>

## Suporte

- **Bugs:** Abra uma [issue](https://github.com/seu-usuario/social-media-api/issues)
- **Ideias:** Use [Discussions](https://github.com/seu-usuario/social-media-api/discussions)
- **Contato:** mouradanilo061@gmail.com
