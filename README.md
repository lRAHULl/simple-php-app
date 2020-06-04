## Docker Assignment 2 

## Understanding Docker - Volumes, Networks, Secrets, Writing Compose file.

### Running the application.

```bash
docker compose up -d
```

Let the images build and run, after a while go [here]('http://localhost:8085/index.php')

### Stopping the application.

```bash
docker-compose stop
```

### Removing the application.

```bash
docker-compose rm -f
```

### Remove the volumes created by the app.

```bash
docker-compose rm -v
```

### Remove the database data created by the app in the host system.
```bash
sudo rm -r mysql-data
```
