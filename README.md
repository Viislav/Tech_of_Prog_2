# Начало
Итак, для начала стоит уточнить, что работаю я на Кали Линуксе, потому все работы будут проведены в zsh терминале и папки так же будут отличаться от Windows.

Для начала, мы переносим мледующую команду

    127.0.0.1 api.localhost traefik.localhost adminer.localhost
В папку /etc/hosts, где стоят настройки для локального хоста по IPv4. По факту, всё уже приготовлено, так как с 1 лабораторной все у нас уже стоит. Теперь мы будем опять поднимать сервисы для нашей работы(не опять, а снова).
# Docker
У нас есть пробный файл .etc.example. Именно в нём идут все настройки, по которым сервисы поднимаются. Там в строке вы должны поменять пароль, а потом поднимать сервисы. Ну и название файла с .env.example на .env не забудьте поменять. Выглядит всё это следующим образом:

    # Fill me and copy to .env
    POSTGRES_PASSWORD=*******
    # Важно: Compose не подставляет переменные внутри значений в файле .env
    POSTGRES_DSN=postgres://app:*******@postgres:5432/appdb
    REDIS_HOST=redis
    CELERY_BROKER_URL=amqp://guest:guest@rabbitmq:5672//
    CELERY_RESULT_BACKEND=redis://redis:6379/0
    # Optional: enable Traefik basic auth (used in docker-compose.security.yml)
    # Generate via: docker run --rm httpd:2.4-alpine htpasswd -nbB admin 'yourpass'
    # Example:
    # BASIC_AUTH_USERS=admin:$2y$05$abcdefghijklmnopqrstuvABCDEFGHIJKLMNOPQRSTUV/abcdefghijklmnopqr
----
    mv ./.env.example ./.env 
----
       docker compose up -d --build
    [+] Building 0.1s (18/22)                                                                                 docker:default
     => [api internal] load build definition from Dockerfile                                                            0.0s
     => => transferring dockerfile: 640B                                                                                0.0s 
     => [worker internal] load build definition from Dockerfile                                                         0.0s 
     => => transferring dockerfile: 640B                                                                                0.0s 
     => [worker internal] load metadata for docker.io/library/python:3.11-slim                                          0.0s 
     => [api internal] load .dockerignore                                                                               0.0s 
     => => transferring context: 2B                                                                                     0.0s 
     => [worker internal] load .dockerignore                                                                            0.0s 
     => => transferring context: 2B                                                                                     0.0s 
     => [api internal] load build context                                                                               0.0s 
     => => transferring context: 128B                                                                                   0.0s 
     => [api builder 1/4] FROM docker.io/library/python:3.11-slim                                                       0.0s 
     => [worker internal] load build context                                                                            0.0s 
     => => transferring context: 128B                                                                                   0.0s 
     => CACHED [api builder 2/4] WORKDIR /app                                                                           0.0s 
     => CACHED [api stage-1 3/5] RUN useradd -m appuser && chown -R appuser /app                                        0.0s 
     => CACHED [api builder 3/4] COPY requirements.txt .                                                                0.0s 
     => CACHED [api builder 4/4] RUN pip install --user --no-cache-dir -r requirements.txt                              0.0s 
     => CACHED [api stage-1 4/5] COPY --from=builder /root/.local /home/appuser/.local                                  0.0s 
     => CACHED [worker stage-1 5/5] COPY app/ app/                                                                      0.0s 
     => [api] exporting to image                                                                                        0.0s 
     => => exporting layers                                                                                             0.0s 
     => => writing image sha256:ffa273fb39dae5a8df9e3c46d8a1fdee4b4eef88bc4372f0feee3e13d6f24018                        0.0s 
     => => naming to docker.io/library/elite-stack-api                                                                  0.0s 
     => [worker] exporting to image                                                                                     0.0s 
     => => exporting layers                                                                                             0.0s 
     => => writing image sha256:6690c1967e50a5b880cd45bce08351dd809b285d8df3b1bcee1d6f39b51e1ca8                        0.0s 
     => => naming to docker.io/library/elite-stack-worker                                                               0.0s 
     => [api] resolving provenance for metadata file                                                                    0.0s 
     => [worker] resolving provenance for metadata file                                                                 0.0s 
    [+] Running 9/9                                                                                                          
     ✔ api                               Built                                                                          0.0s 
     ✔ worker                            Built                                                                          0.0s 
     ✔ Container elite-stack-adminer-1   Running                                                                        0.0s 
     ✔ Container elite-stack-redis-1     Running                                                                        0.0s 
     ✔ Container elite-stack-rabbitmq-1  Healthy                                                                        0.5s 
     ✔ Container elite-stack-traefik-1   Running                                                                        0.0s 
     ✔ Container elite-stack-worker-1    Running                                                                        0.0s 
     ✔ Container elite-stack-postgres-1  Healthy                                                                        0.5s 
     ✔ Container elite-stack-api-1       Running                                                                        0.0s 
Только единственное: удалите version в docker-compose.yaml. Оно у меня ругалось на поставленную актуальную версию. Docker поднимает по своей версии, так что ничего не будет. Теперь будет Запуск и небольшая проверка.
# Запуск
