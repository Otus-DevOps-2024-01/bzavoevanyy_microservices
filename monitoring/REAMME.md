## ДЗ №17 Создание и запуск системы мониторинга Prometheus.

1. Создаем ВМ в Yandex.Cloud
    ```shell
    yc compute instance create \
       --name prometheus-docker-host \
       --zone ru-central1-a --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
       --create-boot-disk type=network-ssd,image-folder-id=standard-images,image-family=ubuntu-2004-lts,size=50 \
       --memory 4GB \
       --cores 2 \
       --ssh-key ~/.ssh/id_rsa.pub
    ```
   
2. Устанавливаем Docker при помощи docker-machine
    ```shell
           docker-machine create \
           --driver generic \
           --generic-ip-address=84.201.129.173 \
           --generic-ssh-user yc-user \
           --generic-ssh-key ~/.ssh/id_rsa \
           docker-host
    ```
3. Добавляем Dockerfile и файл конфигурации prometheus и собираем образ

    ```shell
    cd monitoring/prometheus
    docker buildx build -t bzavoevanyy/prometheus .
    ```
   
4. Переносим docker-compose.yml в директорию docker и добавляем сервис prometheus, конфигурируем сети
5. Запускаем
   ```shell
   docker-compose up -d 
   
   docker ps
   CONTAINER ID   IMAGE                     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
   df3420ae5511   bzavoevanyy/comment:1.0   "puma"                   9 seconds ago   Up 6 seconds                                               reddit_app-comment-1
   9993692bb9c8   bzavoevanyy/prometheus    "/bin/prometheus --c…"   9 seconds ago   Up 6 seconds   0.0.0.0:9090->9090/tcp, :::9090->9090/tcp   reddit_app-prometheus-1
   3117a082aff8   bzavoevanyy/ui:1.0        "puma"                   9 seconds ago   Up 6 seconds   0.0.0.0:9292->9292/tcp, :::9292->9292/tcp   reddit_app-ui-1
   ee41994c52f3   bzavoevanyy/post:1.0      "python3 post_app.py"    9 seconds ago   Up 6 seconds                                               reddit_app-post-1
   4f27919bd901   mongo:4.2                 "docker-entrypoint.s…"   9 seconds ago   Up 6 seconds   27017/tcp                                   reddit_app-post_db-1
   ```
   ![scr1.png](img%2Fscr1.png)

   ![scr2.png](img%2Fscr2.png)

6.  Проверим healthcheck UI сервиса
   
   ![scr3.png](img%2Fscr3.png)

7. Остановим сервис post и снова проверим healthcheck

   ```shell
   docker stop reddit_app-post-1
   ```
   ui сервис сигнализирует об ошибке

   ![scr4.png](img%2Fscr4.png)

8. Определим причину по метрике ui_health_post_availability и восстановим работу сервиса

   ![scr5.png](img%2Fscr5.png)

   ```shell
   docker start reddit_app-post-1
   ```
   
9. Сбор метрик хоста

   Добавим в docker-compose файл сервис node-exporter, обновим конфигурацию prometheus и перезапустим сервисы

   Убедимся, что prometheus собирает метрики хоста

   ![scr6.png](img%2Fscr6.png)

10. Добавим нагрузки и проверим метрики

   ```shell
   docker-machine ssh docker-host
   yes > /dev/null
   ```
   
   Нагрузка возросла

   ![scr7.png](img%2Fscr7.png)