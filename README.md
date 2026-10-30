# News App

## Description

The News App is a Django web application that allows users to read, write, review and manage news articles. It supports three user roles: Readers, Editors and Journalists. Readers can subscribe to publishers and journalists to receive relevant articles.

## Features

- User registration and login.
- Role-based access for Readers, Editors and Journalists.
- Journalists can create articles and newsletters.
- Editors can review, approve, update and delete articles.
- Editors can manage newsletters.
- Readers can browse articles and subscribe to publishers and journalists.
- REST API for accessing and managing articles.
- Token authentication for protected API endpoints.
- Email notifications when articles are approved.

## Requirements

### Running locally with a virtual environment

- Python version compatible with the project's dependencies.
- MariaDB or MySQL server.
- Git, if cloning the repository.

### Running with Docker

- Docker Desktop or another Docker installation with Docker Compose support.
- An internet connection to download the required images and packages.

## Installation and Setup: Virtual Environment

Run the following commands from the project directory containing `manage.py`.

### 1. Clone the repository

Clone the project from its GitHub repository, then navigate into the application directory.

```bash
git clone <YOUR_REPOSITORY_URL>
cd News_App
```

Replace `<YOUR_REPOSITORY_URL>` with the repository URL provided by the project owner.

### 2. Create and activate a virtual environment

Create the environment:

```bash
python -m venv myenv
```

On Windows PowerShell:

```powershell
.\myenv\Scripts\Activate.ps1
```

On macOS or Linux:

```bash
source myenv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure environment variables

The application needs a Django secret key and database credentials. Obtain your own credentials from your local Django/MariaDB configuration or your database administrator. Never copy private production credentials from another environment.

Set the following variables in your terminal before starting Django.

**Windows PowerShell:**

```powershell
$env:DJANGO_SECRET_KEY = "your-own-secret-key"
$env:DJANGO_DEBUG = "True"
$env:DJANGO_ALLOWED_HOSTS = "localhost,127.0.0.1"
$env:DB_NAME = "News_App"
$env:DB_USER = "your_database_user"
$env:DB_PASSWORD = "your_database_password"
$env:DB_HOST = "localhost"
$env:DB_PORT = "3306"
```

**macOS or Linux:**

```bash
export DJANGO_SECRET_KEY="your-own-secret-key"
export DJANGO_DEBUG="True"
export DJANGO_ALLOWED_HOSTS="localhost,127.0.0.1"
export DB_NAME="News_App"
export DB_USER="your_database_user"
export DB_PASSWORD="your_database_password"
export DB_HOST="localhost"
export DB_PORT="3306"
```

Replace the example values with your own settings. Generate a strong, random Django secret key and keep it private. These example commands configure a local development environment; do not use `DEBUG=True` in production.

These variables must match the environment-variable names read by `News_App/settings.py`. If your settings file does not read these variables, update it before relying on this configuration.

### 5. Create the database

Start your local MariaDB or MySQL server and create a database named `News_App`.

Log in using your own database account:

```bash
mariadb -u your_database_user -p
```

Then execute:

```sql
CREATE DATABASE News_App;
EXIT;
```

If the `mariadb` command is unavailable, use your installed MariaDB/MySQL client.

Ensure the database name, username, password, host and port match your environment variables.

### 6. Apply migrations

From the directory containing `manage.py`, run:

```bash
python manage.py makemigrations
python manage.py migrate
```

If the project already contains all required migration files, running `python manage.py migrate` is normally sufficient.

### 7. Create an administrator account

```bash
python manage.py createsuperuser
```

Follow the prompts to create your administrator account.

### 8. Run the application

```bash
python manage.py runserver
```

Open the following addresses in your browser:

- Application: http://127.0.0.1:8000/
- Django admin: http://127.0.0.1:8000/admin/

Keep the terminal running while using the application.

## Running with Docker

Docker provides a containerised environment for the web application and database. The repository should contain a `Dockerfile` and a `docker-compose.yml` file in the directory from which you run Docker Compose.

### 1. Install and start Docker

Install Docker Desktop or an equivalent Docker environment and ensure that its engine is running.

### 2. Configure Docker environment variables

Before starting the containers, supply your own database credentials and Django secret key through a local, untracked `.env` file, if your `docker-compose.yml` uses Compose variable substitution.

For example, a local `.env` file may contain:

```dotenv
DJANGO_SECRET_KEY=replace_with_your_own_random_secret
DJANGO_DEBUG=True
DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1
DB_NAME=News_App
DB_USER=your_database_user
DB_PASSWORD=your_own_database_password
DB_HOST=db
DB_PORT=3306
MARIADB_DATABASE=News_App
MARIADB_USER=your_database_user
MARIADB_PASSWORD=your_own_database_password
MARIADB_ROOT_PASSWORD=your_own_root_password
```

Use the same database name, username and password for the application and database service. The database host should match the database service name in `docker-compose.yml` (for example, `db`).

**Important:** Docker Compose does not automatically pass every variable in a `.env` file into a container. The Compose file must explicitly reference the variables under the relevant service's `environment` section. Configure it to use variable substitution instead of hardcoded passwords.

Add `.env` to `.gitignore` and never commit it. A `.env.example` file containing placeholders is safe to share.

### 3. Build and start the containers

From the directory containing `docker-compose.yml`, run:

```bash
docker compose up --build -d
```

Check the running containers:

```bash
docker compose ps
```

View application logs if needed:

```bash
docker compose logs -f web
```

### 4. Apply database migrations

Once the database and web containers are running, execute:

```bash
docker compose exec web python manage.py migrate
```

If the project has new model changes without migration files, create them first:

```bash
docker compose exec web python manage.py makemigrations
docker compose exec web python manage.py migrate
```

### 5. Create an administrator account

```bash
docker compose exec web python manage.py createsuperuser
```

### 6. Access the application

Open http://127.0.0.1:8000/ in your browser.

The admin page is available at http://127.0.0.1:8000/admin/.

### 7. Stop the containers

To stop the application while preserving database data:

```bash
docker compose down
```

To start it again:

```bash
docker compose up -d
```

**Warning:** `docker compose down -v` also deletes the Compose-managed database volume and its stored data. Do not use it unless you intentionally want to remove that data.

## API Authentication

The News App uses Django REST Framework token authentication for protected API endpoints.

### 1. Obtain a token

Send a `POST` request to:

`http://127.0.0.1:8000/api/token/`

Include valid user credentials in the JSON request body:

```json
{
    "username": "your_username",
    "password": "your_password"
}
```

For example, using `curl`:

```bash
curl -X POST http://127.0.0.1:8000/api/token/ \
  -H "Content-Type: application/json" \
  -d "{\"username\":\"your_username\",\"password\":\"your_password\"}"
```

A successful response contains a token:

```json
{
    "token": "your_authentication_token"
}
```

Use the token returned by your own request; do not share it publicly.

### 2. Access protected endpoints

Include the token in the HTTP `Authorization` header:

```http
Authorization: Token your_authentication_token
```

The token endpoint is configured using Django REST Framework's `obtain_auth_token` view. Requests must use the correct HTTP method and valid credentials.

## Running Tests

Activate your virtual environment and run:

```bash
python manage.py test news.tests
```

To run the tests inside the Docker web container:

```bash
docker compose exec web python manage.py test news.tests
```

Ensure the test database and application dependencies are configured correctly before running the tests.

## Email Notifications

The application sends email notifications when articles are approved. Email delivery depends on the email backend and settings configured in `News_App/settings.py`.

For local development, Django's console email backend can display messages in the terminal instead of sending real emails. To send real emails, obtain SMTP settings and credentials from your email provider and configure them using environment variables. Do not commit SMTP passwords or access tokens.

## Security and Troubleshooting

- Never commit `.env` files, database passwords, Django secret keys, SMTP credentials or API tokens.
- Keep `.env` in `.gitignore`. Share only a `.env.example` file with placeholder values.
- If a credential has already been committed or exposed, rotate it; deleting it from the latest version of a file is not sufficient.
- Keep `DEBUG=False` in production and configure `ALLOWED_HOSTS` for the actual deployment domain.
- Ensure MariaDB/MySQL is running before starting the local application.
- If a database table is missing, apply the required migrations.
- If Docker cannot connect to the database, check the service name, credentials, port and container health.
- Back up database data before performing destructive Docker or database operations.

## Project Structure

The repository contains the Django project configuration, the `news` application, database migrations, templates, tests and supporting documentation. Docker configuration files are used to build and run the application in containers.
