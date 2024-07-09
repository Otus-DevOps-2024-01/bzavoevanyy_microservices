# Micorservices

1. Создал Dockerfile для всех компонентов приложения
2. Сборка приложения (все выполняется на удаленной docker-host):
    ```bash
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
    docker pull mongo:latest
    latest: Pulling from library/mongo
    3713021b0277: Pull complete 
    39bdcacccd97: Pull complete 
    d6b691142508: Pull complete 
    bcc1924dee6d: Pull complete 
    091a7990873d: Pull complete 
    77e5254f6ae8: Pull complete 
    403f753f5920: Pull complete 
    88cd53ea307c: Pull complete 
    Digest: sha256:1cd3951000020c1cb1757868e6cfd82667f57d80bb31fed8b585e26a8a1d960f
    Status: Downloaded newer image for mongo:latest
    docker.io/library/mongo:latest
    
    What's next:
        View a summary of image vulnerabilities and recommendations → docker scout quickview mongo:latest
        
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
    docker build -t bzavoevanyy/post:1.0 ./post-py
    
    [+] Building 26.5s (9/9) FINISHED                                                                                                                                                                                                    docker:default
     => [internal] load .dockerignore                                                                                                                                                                                                              0.2s
     => => transferring context: 2B                                                                                                                                                                                                                0.1s
     => [internal] load build definition from Dockerfile                                                                                                                                                                                           0.2s
     => => transferring dockerfile: 394B                                                                                                                                                                                                           0.2s
     => [internal] load metadata for docker.io/library/python:3.6.0-alpine                                                                                                                                                                         0.7s
     => [1/4] FROM docker.io/library/python:3.6.0-alpine@sha256:142fc3f338b10569d08c3f3855c492c2a176b0c45af099f9ebe87f0fededb210                                                                                                                   0.0s
     => [internal] load build context                                                                                                                                                                                                              0.2s
     => => transferring context: 889B                                                                                                                                                                                                              0.1s
     => CACHED [2/4] WORKDIR /app                                                                                                                                                                                                                  0.0s
     => [3/4] ADD . /app                                                                                                                                                                                                                           0.1s
     => [4/4] RUN apk --no-cache --update add build-base &&     pip install --trusted-host pypi.org --trusted-host pypi.python.org --trusted-host files.pythonhosted.org -r /app/requirements.txt &&     apk del build-base                       24.8s
     => exporting to image                                                                                                                                                                                                                         0.3s 
     => => exporting layers                                                                                                                                                                                                                        0.3s 
     => => writing image sha256:3c7e434e5a3cfffdc2fdd2f12c9c9df4f32ecd466af5ca7eeaf34714f20dc0f3                                                                                                                                                   0.0s 
     => => naming to docker.io/bzavoevanyy/post:1.0                                                                                                                                                                                                0.0s 
                                                                                                                                                                                                                                                        
    What's next:                                                                                                                                                                                                                                        
        View a summary of image vulnerabilities and recommendations → docker scout quickview 
        
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
    docker build -t bzavoevanyy/comment:1.0 ./comment
    
    [+] Building 154.1s (13/13) FINISHED                                                                                                                                                                                                 docker:default
     => [internal] load .dockerignore                                                                                                                                                                                                              0.2s
     => => transferring context: 2B                                                                                                                                                                                                                0.1s
     => [internal] load build definition from Dockerfile                                                                                                                                                                                           0.2s
     => => transferring dockerfile: 717B                                                                                                                                                                                                           0.2s
     => [internal] load metadata for docker.io/library/ruby:2.2                                                                                                                                                                                    0.6s
     => CACHED [1/8] FROM docker.io/library/ruby:2.2@sha256:4987b5e2f03b7086c493208ef616b711fe73228391a80faf451975f9e0195236                                                                                                                       0.0s
     => [internal] load build context                                                                                                                                                                                                              0.1s
     => => transferring context: 1.24kB                                                                                                                                                                                                            0.1s
     => [2/8] RUN echo "deb [trusted=yes] http://archive.debian.org/debian stretch main non-free contrib" > /etc/apt/sources.list &&     echo 'deb-src [trusted=yes] http://archive.debian.org/debian/ stretch main non-free contrib'  >> /etc/ap  0.4s
     => [3/8] RUN apt-get update -qq && apt-get install -y build-essential && apt-get clean                                                                                                                                                       99.4s
     => [4/8] RUN mkdir "/app"                                                                                                                                                                                                                     0.6s 
     => [5/8] WORKDIR /app                                                                                                                                                                                                                         0.2s 
     => [6/8] ADD Gemfile* /app/                                                                                                                                                                                                                   0.2s 
     => [7/8] RUN bundle install                                                                                                                                                                                                                  13.8s 
     => [8/8] ADD . /app                                                                                                                                                                                                                           0.3s 
     => exporting to image                                                                                                                                                                                                                        38.3s 
     => => exporting layers                                                                                                                                                                                                                       38.3s 
     => => writing image sha256:d7ca57180f3cf14f55a8b3294f86dba0b29d365d7c4c55034a30cfaf0de38a15                                                                                                                                                   0.0s 
     => => naming to docker.io/bzavoevanyy/comment:1.0                                                                                                                                                                                             0.0s 
                                                                                                                                                                                                                                                        
    What's next:
        View a summary of image vulnerabilities and recommendations → docker scout quickview 
        
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
    docker build -t bzavoevanyy/ui:1.0 ./ui
    
    [+] Building 18.5s (13/13) FINISHED                                                                                                                                                                                                  docker:default
     => [internal] load .dockerignore                                                                                                                                                                                                              0.2s
     => => transferring context: 2B                                                                                                                                                                                                                0.1s
     => [internal] load build definition from Dockerfile                                                                                                                                                                                           0.2s
     => => transferring dockerfile: 767B                                                                                                                                                                                                           0.2s
     => [internal] load metadata for docker.io/library/ruby:2.2                                                                                                                                                                                    0.6s
     => [1/8] FROM docker.io/library/ruby:2.2@sha256:4987b5e2f03b7086c493208ef616b711fe73228391a80faf451975f9e0195236                                                                                                                              0.0s
     => [internal] load build context                                                                                                                                                                                                              0.1s
     => => transferring context: 1.75kB                                                                                                                                                                                                            0.1s
     => CACHED [2/8] RUN echo "deb [trusted=yes] http://archive.debian.org/debian stretch main non-free contrib" > /etc/apt/sources.list &&     echo 'deb-src [trusted=yes] http://archive.debian.org/debian/ stretch main non-free contrib'  >>   0.0s
     => CACHED [3/8] RUN apt-get update -qq && apt-get install -y build-essential && apt-get clean                                                                                                                                                 0.0s
     => CACHED [4/8] RUN mkdir "/app"                                                                                                                                                                                                              0.0s
     => CACHED [5/8] WORKDIR /app                                                                                                                                                                                                                  0.0s
     => [6/8] ADD Gemfile* /app/                                                                                                                                                                                                                   0.1s
     => [7/8] RUN bundle install                                                                                                                                                                                                                  16.4s
     => [8/8] ADD . /app                                                                                                                                                                                                                           0.3s 
     => exporting to image                                                                                                                                                                                                                         0.6s 
     => => exporting layers                                                                                                                                                                                                                        0.6s 
     => => writing image sha256:9255000fe5d201d9c5c807061e1451f1e89177c2855c6b18bdabebe4b0d2ab40                                                                                                                                                   0.0s 
     => => naming to docker.io/bzavoevanyy/ui:1.0                                                                                                                                                                                                  0.0s 
                                                                                                                                                                                                                                                        
    What's next:
        View a summary of image vulnerabilities and recommendations → docker scout quickview 
    ```

3. Запуск приложения:
   ### Пришлось танцевать с бубном, чтобы запустить сервис post
      ```bash
      ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
      docker network create reddit
      c5e4a5b4bcbd0b76a39d9c3ca05c20e949213820fe5a6c5ed88471aae62aee4d
      
      ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
      docker run -d --network=reddit \
       --network-alias=post_db --network-alias=comment_db mongo:latest
      f17153231d83f3250d969f12529448de7bf9c4e7d01794a9ed25381bb53d17ba
      
      ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
      docker run -d --network=reddit \
       --network-alias=post bzavoevanyy/post:1.0
      03ac61fcfc4dc203d51581fd61c41cdf0407eb0903ca2cf8744b7153797faec7
      
      ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
      docker run -d --network=reddit \
       --network-alias=comment bzavoevanyy/comment:1.0
      6a30bb7acb2f403e381b0918fa3c42dc0e882ee086a060aeaa2ffa99aa07b02e
      
      ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-3]
      docker run -d --network=reddit \
       -p 9292:9292 bzavoevanyy/ui:1.0
      cdcf6f6ff5254ad8d10d38ef5f4e9363c50baca9e041b8d7f63af49036f45889
      ```

4. Проверил работу приложения
5. Улучшаем образы
   ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
   docker images
   REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
   bzavoevanyy/post      1.0       d706451147c7   6 minutes ago   705MB
   bzavoevanyy/ui        1.0       9255000fe5d2   2 hours ago     1.01GB
   bzavoevanyy/comment   1.0       d7ca57180f3c   2 hours ago     1.01GB
   mongo                 latest    a31b196b207d   7 days ago      796MB
   ```
   
   Исправим образ ui

   ```bash 
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
   docker images
   REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
   bzavoevanyy/ui        1.0       4dd8d07b2869   19 seconds ago   486MB
   bzavoevanyy/post      1.0       d706451147c7   18 minutes ago   705MB
   bzavoevanyy/comment   1.0       d7ca57180f3c   2 hours ago      1.01GB
   mongo                 latest    a31b196b207d   7 days ago       796MB
   ```

6. Создадим Docker volume и подключим его к контейнеру с MongoDB

   ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
   docker volume create reddit_db
   reddit_db
   ```
   
7. Запускаем приложение и проверяем работу

```bash
~/OTUS_HOMEWORK/bzavoevanyy_microservices/src git:[docker-3]
docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED              STATUS              PORTS                                       NAMES
b6ccf117b324   bzavoevanyy/ui:1.0        "puma"                   10 seconds ago       Up 8 seconds        0.0.0.0:9292->9292/tcp, :::9292->9292/tcp   exciting_hellman
0256f9b77f8b   bzavoevanyy/comment:1.0   "puma"                   16 seconds ago       Up 15 seconds                                                   elegant_bouman
21b1c9f7b268   bzavoevanyy/post:1.0      "python3 post_app.py"    24 seconds ago       Up 22 seconds                                                   dreamy_jennings
e6a0189c9cd2   mongo:latest              "docker-entrypoint.s…"   About a minute ago   Up About a minute   27017/tcp                                   stoic_mirzakhani
```