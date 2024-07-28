## ДЗ №18 Логирование приложений.

1. Скачаем новые версии приложений reddit
2. Создаем ВМ в Yandex.Cloud
    ```shell
    yc compute instance create \
       --name logging \
       --zone ru-central1-a --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
       --create-boot-disk type=network-ssd,image-folder-id=standard-images,image-family=ubuntu-2004-lts,size=50 \
       --memory 4GB \
       --cores 2 \
       --ssh-key ~/.ssh/id_rsa.pub
    ```
3. Устанавливаем Docker при помощи docker-machine
    ```shell
    docker-machine create \
    --driver generic \
    --generic-ip-address=89.169.128.196 \
    --generic-ssh-user yc-user \
    --generic-ssh-key ~/.ssh/id_rsa \
    logging
    ```
   
4. Создаем logging/docker-compose.yml для запуска EFK стека
5. Создаем кастомный образ fluentd logging/fluentd
6. Запускаем приложения и проверяем пишутся ли логи

    ```shell
    docker-compose logs -f post
    
    {"event": "post_create", "level": "info", "message": "Successfully created a new post", "params": {"link": "http://hello.com", "title": "new post"}, "request_id": "defce9e8-dbda-4d8b-b6f6-7dc4ec78d74c", "service": "post", "timestamp": "2024-07-28 15:39:10"}
    {"addr": "172.19.0.2", "event": "request", "level": "info", "method": "POST", "path": "/add_post?", "request_id": "defce9e8-dbda-4d8b-b6f6-7dc4ec78d74c", "response_status": 200, "service": "post", "timestamp": "2024-07-28 15:39:10"}
    {"event": "find_all_posts", "level": "info", "message": "Successfully retrieved all posts from the database", "params": {}, "request_id": "10ca5aaf-37d1-4e6e-9eec-aca61985fec1", "service": "post", "timestamp": "2024-07-28 15:39:10"}
    {"addr": "172.19.0.2", "event": "request", "level": "info", "method": "GET", "path": "/posts?", "request_id": "10ca5aaf-37d1-4e6e-9eec-aca61985fec1", "response_status": 200, "service": "post", "timestamp": "2024-07-28 15:39:10"}
    ```
   
7. Запускаем приложения EFK
8. Создаем индекс fluentd-*

    ![img1.png](img%2Fimg1.png)

9. Просмотр логов в Kibana
    
    ![img2.png](img%2Fimg2.png)

10. Добавление фильтров

   В конфиг файл fluentd.conf добавим фильтр json:
   ```
   <filter service.post>
   @type parser
   key_name log
   <parse>
   @type json
   </parse>
   </filter>
   ```
   
   Проверяем

   ![img3.png](img%2Fimg3.png)

11. Неструктурированные логи. Добавим логирование для сервиса ui

   Видно, что общая структура у поля log в сообщениях от этого сервиса отсутствует
   ![img4.png](img%2Fimg4.png)

12. Добавление фильтра во Fluentd для сервиса ui

   ```
      <filter service.ui>
        @type parser
        key_name log
         <parse>
             @type regexp
             expression /\[(?<time>[^\]]*)\]  (?<level>\S+) (?<user>\S+)[\W]*service=(?<service>\S+)[\W]*event=(?<event>\S+)[\W]*(?:path=(?<path>\S+)[\W]*)?request_id=(?<request_id>\S+)[\W]*(?:remote_addr=(?<remote_addr>\S+)[\W]*)?(?:method= (?<method>\S+)[\W]*)?(?:response_status=(?<response_status>\S+)[\W]*)?(?:message='(?<message>[^\']*)[\W]*)?/
       </parse>
      </filter>
   ```
   
   Перезапускаем контейнер fluentd и проверяем

   ![img5.png](img%2Fimg5.png)

13. Пробуем grok шаблоны

   Добавим фильтр
   ```
   <filter service.ui>
     @type parser
     <parse>
       @type grok
       grok_pattern %{RUBY_LOGGER}
     </parse>
     key_name log
   </filter>
   <filter service.ui>
   @type parser
   <parse>
   @type grok
   grok_pattern service=%{WORD:service} \| event=%{WORD:event} \| request_id=%{GREEDYDATA:request_id} \| message='%{GREEDYDATA:message}'
   </parse>
   key_name message
   reserve_data true
   </filter>
   ```
   
14. Включение трассировки

   Включаем Zipkin и настраиваем наши приложения на отправку трейсов

   ![img6.png](img%2Fimg6.png)
   
