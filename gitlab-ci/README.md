## ДЗ №16 Gitlab CI. Построение процесса непрерывной интеграции

1. Создаем виртуальную машину в Yandex.Cloud
   ```bash
   yc compute instance create \
   --name gitlab-docker-host \
   --zone ru-central1-a --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
   --create-boot-disk type=network-ssd,image-folder-id=standard-images,image-family=ubuntu-2004-lts,size=50 \
   --memory 4GB \
   --cores 2 \
   --ssh-key ~/.ssh/id_rsa.pub
   ```
2. На ВМ устанавливаем Docker с помощью docker-machine
   ```bash
       docker-machine create \
       --driver generic \
       --generic-ip-address=51.250.71.251 \
       --generic-ssh-user yc-user \
       --generic-ssh-key ~/.ssh/id_rsa \
       docker-host
   ```
3. Создаем директории по data volumes:
    ```bash
    docker-machine ssh docker-host
    Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-187-generic x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/pro
    Last login: Thu Jul 25 17:14:26 2024 from 5.129.197.143
    yc-user@docker-host:~$ sudo mkdir -p /srv/gitlab/config /srv/gitlab/data /srv/gitlab/logs
    
    ```
4. Запускаем GitLab
   Создаем docker-compose.yaml и запускаем:
    ```bash
    docker-compose up -d
    
    [+] Running 10/10
     ✔ web Pulled                                                                                                                                                                                                                                281.0s 
       ✔ 3713021b0277 Already exists                                                                                                                                                                                                               0.0s 
       ✔ 18fb9b360a6c Pull complete                                                                                                                                                                                                                0.5s 
       ✔ c7e4f5c8d5dd Pull complete                                                                                                                                                                                                                2.8s 
       ✔ 1ae68faf8cbf Pull complete                                                                                                                                                                                                                3.0s 
       ✔ 40c87fb60e9d Pull complete                                                                                                                                                                                                                3.1s 
       ✔ 1329d3b69d77 Pull complete                                                                                                                                                                                                                3.2s 
       ✔ 58a911bce43c Pull complete                                                                                                                                                                                                                3.3s 
       ✔ 1a7a4bf6f4d7 Pull complete                                                                                                                                                                                                                3.4s 
       ✔ 97f870522a99 Pull complete                                                                                                                                                                                                              269.2s 
    [+] Running 2/2
     ✔ Network gitlab-ci_default  Created     
    ```
    Узнаем пароль для root
   ```bash
   docker exec -it gitlab-ci-web-1 grep 'Password:' /etc/gitlab/initial_root_password
   ```
    
5. Заходим в GitLab и создаем группу проектов homework
6. Создаем проект example
7. Добавляем remote к репозиторию infra и пушим проект в gitlab
8. Запускаем runner
   ```bash
   docker run -d --name gitlab-runner --restart always -v /srv/gitlabrunner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:latest
   ```
9. Регистрируем runner
   ```bash
   docker exec -it gitlab-runner gitlab-runner register \
    --url https://gitlab.bz-otus-project.fun \
    --non-interactive \
    --locked=false \
    --name DockerRunner \
    --executor docker \
    --docker-image alpine:latest \
    --registration-token <your-token> \
    --tag-list "linux,xenial,ubuntu,docker" \
    --run-untagged 
   ```
   
10. Pipeline запустился
   ```
   Running with gitlab-runner 17.2.1 (9882d9c7)
     on DockerRunner Uv_yPgkW, system ID: r_qiWmk17llKRj
   Preparing the "docker" executor
   00:41
   Using Docker executor with image alpine:latest ...
   Pulling docker image alpine:latest ...
   Using docker image sha256:324bc02ae1231fd9255658c128086395d3fa0aedd5a41ab6b034fd649d1a9260 for alpine:latest with digest alpine@sha256:0a4eaa0eecf5f8c050e5bba433f58c052be7587ee8af3e8b3910ef9ab5fbe9f5 ...
   Preparing environment
   00:18
   Running on runner-uvypgkw-project-1-concurrent-0 via c2dc8d9f3ea3...
   Getting source from Git repository
   00:31
   Fetching changes with git depth set to 20...
   Initialized empty Git repository in /builds/homework/example/.git/
   Created fresh repository.
   Checking out e73a0f45 as detached HEAD (ref is gitlab-ci-1)...
   Skipping Git submodules setup
   Executing "step_script" stage of the job script
   00:14
   Using docker image sha256:324bc02ae1231fd9255658c128086395d3fa0aedd5a41ab6b034fd649d1a9260 for alpine:latest with digest alpine@sha256:0a4eaa0eecf5f8c050e5bba433f58c052be7587ee8af3e8b3910ef9ab5fbe9f5 ...
   $ echo 'Building'
   Building
   Cleaning up project directory and file based variables
   00:24
   Job succeeded
   ```

11. Настройка тестов и запуск пайплайна

   ![screen1.png](img%2Fscreen1.png)

12. Добавление окружения dev

   ![screen2.png](img%2Fscreen2.png)

13. Добавление окружений stage и production

   ![screen3.png](img%2Fscreen3.png)

14. Добавление переменных окружений

   ![screen4.png](img%2Fscreen4.png)