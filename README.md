# Lab Assignment 6 – Docker & MySQL

**Name:** Shirabthinath Shivaji
**Docker Hub:** [shirabthi](https://hub.docker.com/u/shirabthi)

This lab covers installing Docker Desktop on Windows, building and running a containerized todo app from a `Dockerfile`, pushing the image to Docker Hub, and running a MySQL database server in Docker that is managed through MySQL Workbench.

## Deliverables

| Deliverable | Location |
|---|---|
| Dockerfile | [`Dockerfile`](./Dockerfile) |
| Docker Desktop with a running container | [`screenshots/Docker_SS.png`](./screenshots/Docker_SS.png) |
| MySQL Workbench connected to the MySQL server | [`screenshots/SQL_DATABASE_SS.png`](./screenshots/SQL_DATABASE_SS.png) |
| Image push and MySQL container commands | [`screenshots/Git_SS.png`](./screenshots/Git_SS.png) |
| Todo app running in the browser | [`screenshots/To_Do_SS.png`](./screenshots/To_Do_SS.png) |

## Tools Used

- Docker Desktop for Windows (WSL 2 backend)
- Docker Hub
- MySQL (official `mysql` Docker image)
- MySQL Workbench
- MSYS2 / Git Bash terminal

## Part 1 – Build and Run the Todo App

The `Dockerfile` in this repository builds the getting-started todo app (a Node.js backend with a web front end) into a Docker image. The app listens on port 3000 inside the container.

**Build the image:**

```bash
docker build --tag shirabthi/getting-started-todo-app .
```

**Run the container**, mapping port 8080 on the host to port 3000 in the container:

```bash
docker run --detach --name todo --publish 8080:3000 shirabthi/getting-started-todo-app
```

The app is then available at <http://localhost:8080>.

![Todo app running in the browser](./screenshots/To_Do_SS.png)

## Part 2 – Push the Image to Docker Hub

```bash
docker login
docker push shirabthi/getting-started-todo-app
```

The image is published at [hub.docker.com/r/shirabthi/getting-started-todo-app](https://hub.docker.com/r/shirabthi/getting-started-todo-app).

**Cleanup** of the todo app container and the Compose stack:

```bash
docker rm --force todo
docker compose down --volumes
```

## Part 3 – Run MySQL Server in Docker

```bash
docker run --detach --name mysql-db \
  --env MYSQL_ROOT_PASSWORD=<your_password> \
  --publish 3306:3306 \
  --volume mysql-data:/var/lib/mysql \
  mysql
```

| Option | Purpose |
|---|---|
| `--name mysql-db` | Names the container |
| `--env MYSQL_ROOT_PASSWORD` | Sets the MySQL root password |
| `--publish 3306:3306` | Exposes MySQL on the host's port 3306 |
| `--volume mysql-data:/var/lib/mysql` | Persists database files in a named volume |

**Verify it is running:**

```bash
docker ps
```

![Terminal output: image push, cleanup, and MySQL container](./screenshots/Git_SS.png)

![Docker Desktop showing the running mysql-db container](./screenshots/Docker_SS.png)

## Part 4 – Connect with MySQL Workbench

A new connection was created in MySQL Workbench with these settings:

| Setting | Value |
|---|---|
| Connection Method | Standard TCP/IP (mysql) |
| Host | `localhost` |
| Port | `3306` |
| User Name | `root` |
| Password | The value of `MYSQL_ROOT_PASSWORD` |

After connecting, `SHOW DATABASES;` confirms the server is reachable and returns the default system databases.

![MySQL Workbench connected to the Docker MySQL server](./screenshots/SQL_DATABASE_SS.png)

## Troubleshooting Notes

- **"Virtual Machine Platform" not enabled during install:** Re-run the installer and choose *All-users installation*, or run `wsl --install` in an administrator PowerShell and restart.
- **Pasting in the MSYS2 / Git Bash terminal:** Use `Shift + Insert` instead of `Ctrl + V`.
- **Workbench shows "offline":** Make sure the `mysql-db` container is running (`docker ps`), wait for `ready for connections` in `docker logs mysql-db`, then reopen the connection. Using `127.0.0.1` instead of `localhost` can also help.
