# 📚 Library Management API

Uma API RESTful robusta para gerenciamento de bibliotecas, desenvolvida com Laravel e PostgreSQL.

## 🚀 Tecnologias

- **Laravel 10+** - Framework PHP
- **PostgreSQL** - Banco de dados relacional
- **PHP 8.1+** - Linguagem de programação
- **Composer** - Gerenciador de dependências PHP

## 📋 Pré-requisitos

Antes de começar, certifique-se de ter instalado:

- [PHP](https://www.php.net/) (versão 8.1 ou superior)
- [Composer](https://getcomposer.org/)
- [PostgreSQL](https://www.postgresql.org/) (versão 12 ou superior)
- [Laravel Herd](https://herd.laravel.com/) (recomendado) ou outro servidor local

## ⚙️ Instalação

1. **Clone o repositório**
```bash
git clone https://github.com/helenilsoon/library-management-api.git
cd library-management-api
```

2. **Instale as dependências**
```bash
composer install
```

3. **Configure as variáveis de ambiente**

Copie o arquivo `.env.example` para `.env`:
```bash
cp .env.example .env
```

Edite o arquivo `.env` com suas configurações:

```env
APP_NAME="Library Management API"
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

# Database
#DB_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=


```

4. **Gere a chave da aplicação**
```bash
php artisan key:generate
```

5. **Execute as migrações**
```bash
php artisan migrate
```

6. **Popule o banco com dados de exemplo (opcional)**
```bash
php artisan db:seed
```

## 🏃‍♂️ Executando o Projeto

### Usando Laravel Herd
Se você está usando o Herd, basta acessar:
```
http://library-management-api.test
```

### Usando Artisan Serve
```bash
php artisan serve
```
A API estará disponível em: `http://localhost`

### Usando Nginx/Apache
Configure o virtual host apontando para a pasta `public/` do projeto.

## 📚 Endpoints da API

### Livros

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/books` | Lista todos os livros |
| GET | `/api/books/{id}` | Busca um livro por ID |
| POST | `/api/books` | Cria um novo livro |
| PUT | `/api/books/{id}` | Atualiza um livro |
| DELETE | `/api/books/{id}` | Remove um livro |
| GET | `/api/books/search` | Busca livros por título/autor |

### Usuários/Membros

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/members` | Lista todos os membros |
| GET | `/api/members/{id}` | Busca um membro por ID |
| POST | `/api/members` | Cria um novo membro |
| PUT | `/api/members/{id}` | Atualiza um membro |
| DELETE | `/api/members/{id}` | Remove um membro |

### Empréstimos

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/api/loans` | Lista todos os empréstimos |
| GET | `/api/loans/{id}` | Busca um empréstimo por ID |
| POST | `/api/loans` | Registra um novo empréstimo |
| PUT | `/api/loans/{id}` | Atualiza um empréstimo |
| PATCH | `/api/loans/{id}/return` | Marca livro como devolvido |
| GET | `/api/loans/overdue` | Lista empréstimos atrasados |

## 📝 Exemplos de Uso

### Criar um novo livro
```bash
POST /api/books
Content-Type: application/json
Accept: application/json

{
  "title": "1984",
  "author": "George Orwell",
  "isbn": "978-0451524935",
  "publisher": "Signet Classic",
  "published_year": 1949,
  "pages": 328,
  "category": "Fiction",
  "quantity": 5
}
```

**Resposta:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "1984",
    "author": "George Orwell",
    "isbn": "978-0451524935",
    "available": 5,
    "created_at": "2025-10-06T01:43:26.000000Z"
  },
  "message": "Livro criado com sucesso"
}
```

### Registrar um empréstimo
```bash
POST /api/loans
Content-Type: application/json

{
  "book_id": 1,
  "member_id": 1,
  "loan_date": "2025-10-06",
  "due_date": "2025-10-20"
}
```

### Marcar livro como devolvido
```bash
PATCH /api/loans/1/return
Content-Type: application/json

{
  "return_date": "2025-10-15"
}
```

## 🗄️ Estrutura do Banco de Dados

### Migration: Books
```php
Schema::create('books', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->string('author');
    $table->string('isbn')->unique();
    $table->string('publisher')->nullable();
    $table->integer('published_year')->nullable();
    $table->integer('pages')->nullable();
    $table->string('category')->nullable();
    $table->integer('quantity')->default(1);
    $table->timestamps();
    $table->softDeletes();
});
```

### Migration: Members
```php
Schema::create('members', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->string('phone')->nullable();
    $table->text('address')->nullable();
    $table->date('membership_date')->default(now());
    $table->timestamps();
    $table->softDeletes();
});
```

### Migration: Loans
```php
Schema::create('loans', function (Blueprint $table) {
    $table->id();
    $table->foreignId('book_id')->constrained()->onDelete('cascade');
    $table->foreignId('member_id')->constrained()->onDelete('cascade');
    $table->date('loan_date');
    $table->date('due_date');
    $table->date('return_date')->nullable();
    $table->enum('status', ['active', 'returned', 'overdue'])->default('active');
    $table->timestamps();
});
```

## 🛠️ Comandos Artisan Úteis

```bash
# Criar um novo controller
php artisan make:controller BookController --api --model=Book

# Criar um novo model com migration
php artisan make:model Book -m

# Criar um seeder
php artisan make:seeder BooksTableSeeder

# Criar um request de validação
php artisan make:request StoreBookRequest

# Limpar cache
php artisan cache:clear
php artisan config:clear
php artisan route:clear

# Ver todas as rotas
php artisan route:list

# Executar testes
php artisan test
```

## 📦 Estrutura de Pastas

```
library-management-api/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── BookController.php
│   │   │   ├── MemberController.php
│   │   │   └── LoanController.php
│   │   └── Requests/
│   ├── Models/
│   │   ├── Book.php
│   │   ├── Member.php
│   │   └── Loan.php
│   └── Services/
├── database/
│   ├── migrations/
│   ├── seeders/
│   └── factories/
├── routes/
│   ├── api.php
│   └── web.php
├── tests/
│   ├── Feature/
│   └── Unit/
└── .env
```

## 🔒 Segurança

- Validação de dados com Form Requests
- Proteção CSRF (desabilitada para API)
- Rate limiting configurado
- Sanitização de inputs
- Autenticação com Laravel Sanctum (opcional)

## 📄 Dependências Principais

```json
{
  "php": "^8.1",
  "laravel/framework": "^10.0",
  "guzzlehttp/guzzle": "^7.2"
}
```

## 🧪 Testes

Execute os testes com:

```bash
# Todos os testes
php artisan test

# Testes com coverage
php artisan test --coverage

# Teste específico
php artisan test --filter BookControllerTest
```

## 🤝 Contribuindo

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/nova-feature`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova feature'`)
4. Push para a branch (`git push origin feature/nova-feature`)
5. Abra um Pull Request

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## 👤 Autor

**helenilson**
- GitHub: [@helenilsoon](https://github.com/helenilsoon)
- Localização: Manaus, Amazonas, BR

## 📞 Suporte

Se você tiver alguma dúvida ou problema, abra uma [issue](https://github.com/helenilsoon/library-management-api/issues) no GitHub.

---

⭐ Desenvolvido com Laravel + PostgreSQL