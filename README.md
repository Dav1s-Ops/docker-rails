# Dockerized Rails Development

This README provides info on the files needed for setting up and running a Rails app using Docker and PostgreSQL in development. It's a pretty simple setup and allows you to keep development containerized!

## Prerequisites
- You need to have [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running

## Structure Overview
- `Dockerfile.dev`: Defines the image for the Rails web server in `development`.

- `docker-compose.yml`: Manages the services being the Rails app (`web`) and the PostgreSQL database (`db`).

- `config/database.yml`: Configuration for PostgreSQL database connections in different environments.

## Getting started
### Setup and/or Installation
Whether you'd like to clone the repo or just create the files in your project is up to you! If you're cloning, you can start with the steps below:
### 1. Build and Start Containers
  Build the Docker images and start the containers
  ```bash
  docker compose up --build
  ```
  This command will set up two services:
  - `db`: The PostgreSQL database instance.
  - `web`: The Rails app, linked to the database.
### 2. Database Setup
  Once the containers are set up, you can jump into the Docker Rails shell in another tab 
  ```bash
  docker compose exec web bash
  ```
  Then run the migration command to create the `schema.rb`
  ```bash
  rails db:migrate
  ```
### 3. Testing the Database Connection
  Don't close out of the Docker Rails shell session because now you can check the `db` container connection to the Rails app (`web`) from the Rails Console!
  ```bash
  rails c
  ```
  Once in the console, you can check the connection via `ActiveRecord`
  ```ruby
  ActiveRecord::Base.connection.execute("SELECT version()").first
  ```
  ```ruby
  ActiveRecord::Base.connection.active?
  ```
  #### Example:
  <div style="display: flex; justify-content: center; align-items: center;">
    <img src="https://github.com/user-attachments/assets/d9990aed-dc52-458e-a8a6-38c72473d03a" alt="Screenshot">
  </div>

## Docker Development Files Breakdown

### Dockerfile (`Dockerfile.dev`)
- **Base Image**: Uses a slim Ruby version defined with `ARG RUBY_VERSION=3.2.2`.
- **Working Directory**: The app runs inside `/rails`.
- **Dependencies**: Installs required packages and dependencies such as `libpq-dev` and `nodejs` for JavaScript runtime support.
- **Startup Command**: Rails server starts listening at `0.0.0.0:3000` for external access.

### Docker Compose (`docker-compose.yml`)
- **Database Service (`db`)**: Uses PostgreSQL 17 with a volume (`db_data`) to persist data between container instances.
- **Web Service (`web`)**: Builds from the Dockerfile and maps the local directory to `/rails` for live reloading.
- **Environment Variables**: Defines database credentials for development.

### Database Configuration (`config/database.yml`)
The configuration file defines the settings for different environments (`development`, `test`, `production`) with credentials sourced from environment variables. The database uses:
- Hostname (`DATABASE_HOST`) defined as `db` for container communication.
- Username and password for PostgreSQL authentication.

## Troubleshooting
### Database Connection Issues 
Ensure that the `db` service is running and that the credentials match between `docker-compose.yml` and `config/database.yml`.
### Port Conflicts 
Check that port `3000` is not being used by another process on your local machine.
