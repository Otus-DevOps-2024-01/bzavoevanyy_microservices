### ДЗ Технология контейнеризации. Введение в Docker

1. Рассмотрел основные возможности взаимодействия с docker согласно методическим указаниям.
2. Создан image из контейнера:
    ```bash
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-2]
    docker ps
    CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS              PORTS                    NAMES
    b9bd219c9cff   ubuntu:18.04   "/bin/bash"              8 minutes ago   Up About a minute                            affectionate_hopper
    fa289e9eee6a   redis          "docker-entrypoint.s…"   4 weeks ago     Up 12 minutes       0.0.0.0:6379->6379/tcp   localredis
    
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    docker images | grep ubuntu-tmp-file > docker-1.log
    ```

3. Создал Docker хост в Yandex Cloud и настроим локальное окружение на работу с ним
    ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    yc compute instance create \
    --name docker-host \
    --zone ru-central1-a \
    --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
    --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
    --ssh-key ~/.ssh/id_rsa.pub
    done (33s)
    id: fhm9ic14mr7hf1s4e7np
    folder_id: b1grqb9qi5s92olhm5sj
    created_at: "2024-07-06T08:32:26Z"
    name: docker-host
    zone_id: ru-central1-a
    platform_id: standard-v2
    resources:
    memory: "2147483648"
    cores: "2"
    core_fraction: "100"
    status: RUNNING
    boot_disk:
    mode: READ_WRITE
    device_name: fhm2h27ug7aiqh2migep
    auto_delete: true
    disk_id: fhm2h27ug7aiqh2migep
    network_interfaces:
    - index: "0"
      mac_address: d0:0d:99:30:24:b6
      subnet_id: e9b9a6g10ev0etmqa25a
      primary_v4_address:
      address: 10.128.0.13
      one_to_one_nat:
      address: 51.250.10.85
      ip_version: IPV4
      fqdn: fhm9ic14mr7hf1s4e7np.auto.internal
      scheduling_policy: {}
      network_settings:
      type: STANDARD
      placement_policy: {}
    
    There is a new yc version '0.128.0' available. Current version: '0.89.0'.
    See release notes at https://cloud.yandex.ru/docs/cli/release-notes
    You can install it by running the following command in your shell:
    $ yc components update

    ```
4. Инициализировал окружение docker:
    ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    docker-machine create \
    --driver generic \
    --generic-ip-address=51.250.10.85 \
    --generic-ssh-user yc-user \
    --generic-ssh-key ~/.ssh/id_rsa \
    docker-host
    Creating CA: /Users/bogdanzavoevanyy/.docker/machine/certs/ca.pem
    Creating client certificate: /Users/bogdanzavoevanyy/.docker/machine/certs/cert.pem
    Running pre-create checks...
    Creating machine...
    (docker-host) Importing SSH key...
    Waiting for machine to be running, this may take a few minutes...
    Detecting operating system of created instance...
    Waiting for SSH to be available...
    Detecting the provisioner...
    Provisioning with ubuntu(systemd)...
    Installing Docker...
    Copying certs to the local machine directory...
    Copying certs to the remote machine...
    Setting Docker configuration on the remote daemon...
    Checking connection to Docker...
    Docker is up and running!
    To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env docker-host
    ```
    ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    docker-machine env docker-host
    export DOCKER_TLS_VERIFY="1"
    export DOCKER_HOST="tcp://51.250.10.85:2376"
    export DOCKER_CERT_PATH="/Users/bogdanzavoevanyy/.docker/machine/machines/docker-host"
    export DOCKER_MACHINE_NAME="docker-host"
    # Run this command to configure your shell:
    # eval $(docker-machine env docker-host)
    ```
    ```bash
    ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    eval $(docker-machine env docker-host)
    ```

5. Подготовил Dockerfile для приложения redit
6. Собрал docker образ приложения:
    ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
    docker build -t reddit:latest .
    
    [+] Building 223.9s (15/15) FINISHED                                                                                                                                                                                                 docker:default
    => [internal] load build definition from Dockerfile                                                                                                                                                                                           0.5s
    => => transferring dockerfile: 480B                                                                                                                                                                                                           0.3s
    => [internal] load .dockerignore                                                                                                                                                                                                              0.5s
    => => transferring context: 2B                                                                                                                                                                                                                0.3s
    => [internal] load metadata for docker.io/library/ubuntu:18.04                                                                                                                                                                                0.8s
    => [ 1/10] FROM docker.io/library/ubuntu:18.04@sha256:152dc042452c496007f07ca9127571cb9c29697f42acbfad72324b2bb2e43c98                                                                                                                        0.0s
    => [internal] load build context                                                                                                                                                                                                              0.2s
    => => transferring context: 89B                                                                                                                                                                                                               0.2s
    => CACHED [ 2/10] RUN apt-get update      && apt-get install -y mongodb-server ruby-full ruby-dev build-essential git     && apt-get clean cache                                                                                              0.0s
    => [ 3/10] RUN gem install bundler -v 2.3.27                                                                                                                                                                                                 17.5s
    => [ 4/10] RUN git clone -b monolith https://github.com/express42/reddit.git                                                                                                                                                                  2.4s
    => [ 5/10] COPY mongod.conf /etc/mongod.conf                                                                                                                                                                                                  0.2s
    => [ 6/10] COPY db_config /reddit/db_config                                                                                                                                                                                                   0.2s
    => [ 7/10] COPY start.sh /start.sh                                                                                                                                                                                                            0.2s
    => [ 8/10] WORKDIR /reddit                                                                                                                                                                                                                    0.2s
    => [ 9/10] RUN rm Gemfile.lock && bundle install                                                                                                                                                                                             23.9s
    => [10/10] RUN chmod 0777 /start.sh                                                                                                                                                                                                           0.6s
    => exporting to image                                                                                                                                                                                                                       176.9s
    => => exporting layers                                                                                                                                                                                                                      176.8s
    => => writing image sha256:96721525fc9fb8fda0fc7532f2f00a8a05a34f559156f6d66c24047088398407                                                                                                                                                   0.0s
    => => naming to docker.io/library/reddit:latest                                                                                                                                                                                               0.0s
    
    What's next:
    View a summary of image vulnerabilities and recommendations → docker scout quickview
   ```
   
7. Запускаем контейнер с приложением:
   ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-2]
   docker run --name reddit -d --network=host reddit:latest
   654c58f19e4b433563d3fae996b78dee4c052cd5e6e0dd409d55710d2a31336a
   
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-2]
   docker ps
   CONTAINER ID   IMAGE           COMMAND       CREATED              STATUS              PORTS     NAMES
   654c58f19e4b   reddit:latest   "/start.sh"   About a minute ago   Up About a minute             reddit
   ```
   
8. Запушил image в dockerhub:

   ```bash
   ~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
   docker push  bzavoevanyy/otus-reddit:1.0
   The push refers to repository [docker.io/bzavoevanyy/otus-reddit]
   e64019983390: Pushed
   6f2922a62d1e: Pushed
   5f70bf18a086: Mounted from conduktor/kafka
   7d6c438b80e3: Pushed
   945c4ef35785: Pushed
   099ce7b1b139: Pushed
   cad4e02586a8: Pushed
   96262f3526b0: Pushed
   0294df6e6256: Pushed
   c09969dbc5e8: Mounted from library/ubuntu
   1.0: digest: sha256:9510f6a767ee1af76cf6639a04caa5e52c7eb373bb02a76f74b81d1d4fd201fd size: 2408
   ```

9. Проверки:
```bash
~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-2]
docker logs reddit -f
about to fork child process, waiting until server is ready for connections.
forked process: 9
child process started successfully, parent exiting
Puma starting in single mode...
* Puma version: 6.4.2 (ruby 2.5.1-p57) ("The Eagle of Durango")
*  Min threads: 0
*  Max threads: 5
*  Environment: development
*          PID: 32
/reddit/helpers.rb:4: warning: redefining `object_id' may cause serious problems
* Listening on http://0.0.0.0:9292
Use Ctrl-C to stop

~/OTUS_HOMEWORK/bzavoevanyy_microservices git:[docker-2]
docker exec -it reddit bash
root@docker-host:/reddit# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1  18384  3000 ?        Ss   09:40   0:00 /bin/bash /start.sh
root         9  0.2  3.0 1020992 61692 ?       Sl   09:40   0:03 /usr/bin/mongod --fork --logpath /var/log/mongod.log --config /etc/mongodb.conf
root        32  0.0  1.8 593752 37212 ?        Sl   09:40   0:01 puma 6.4.2 (tcp://0.0.0.0:9292) [reddit]
root        60  0.2  0.1  18516  3400 pts/0    Ss   10:03   0:00 bash
root        75  0.0  0.1  34412  2908 pts/0    R+   10:03   0:00 ps aux
root@docker-host:/reddit# killall 5 1 
bash: killall: command not found
root@docker-host:/reddit# killall5 1
root@docker-host:/reddit# 
What's next:
    Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug reddit
    Learn more at https://docs.docker.com/go/debug-cli/
    
~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker inspect bzavoevanyy/otus-reddit:1.0
[
    {
        "Id": "sha256:dddda133c1008669dd6621535c0081252009c589df583e69aa2e1c3576368e91",
        "RepoTags": [
            "bzavoevanyy/otus-reddit:1.0",
            "reddit:latest"
        ],
        "RepoDigests": [
            "bzavoevanyy/otus-reddit@sha256:9510f6a767ee1af76cf6639a04caa5e52c7eb373bb02a76f74b81d1d4fd201fd"
        ],
        "Parent": "",
        "Comment": "buildkit.dockerfile.v0",
        "Created": "2024-07-06T09:50:57.372248139Z",
        "ContainerConfig": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": null,
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "DockerVersion": "",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/start.sh"
            ],
            "ArgsEscaped": true,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "/reddit",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "18.04"
            }
        },
        "Architecture": "arm64",
        "Variant": "v8",
        "Os": "linux",
        "Size": 626429167,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/zie9ttnoupdiioe6ru54zsau1/diff:/var/lib/docker/overlay2/q4b98bckrhtm1k8911picxi0t/diff:/var/lib/docker/overlay2/l0sr3tecn22jhk0x7x0blpfyg/diff:/var/lib/docker/overlay2/kusv2ekyo6r1xqfjfboissusc/diff:/var/lib/docker/overlay2/0axr9uglkc75s4pi2i8bi5719/diff:/var/lib/docker/overlay2/0z7lrfbovl7zqbuizirkbicxz/diff:/var/lib/docker/overlay2/yndi7qp7zwv526avpdtklp020/diff:/var/lib/docker/overlay2/xpm80yt85wr3wzder60lxwmpc/diff:/var/lib/docker/overlay2/0a6a07622c810f3ebb5d6c0de525d7757d0024d992647b06936a6411bf17144e/diff",
                "MergedDir": "/var/lib/docker/overlay2/5wpermq2y5a1w15nntszflaot/merged",
                "UpperDir": "/var/lib/docker/overlay2/5wpermq2y5a1w15nntszflaot/diff",
                "WorkDir": "/var/lib/docker/overlay2/5wpermq2y5a1w15nntszflaot/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:c09969dbc5e84ea45848232c61ee613e2283d20a03d72bb98bc819d2fbeb3218",
                "sha256:0294df6e62563724f318cb2a33e6187ddeeb6c51a13ee27e9270483b091c2a92",
                "sha256:96262f3526b0b2652e43f1a0fd993cef14c8cf30611c6f440c4aec90198b29c8",
                "sha256:cad4e02586a80517f37d076d0654bad38afaffc0c68c56e6e10c0c6a8cb13df2",
                "sha256:099ce7b1b139aab231c305e3c915cf24c722a1a4d4abd1005ba3f1e3e2e390c5",
                "sha256:945c4ef35785ef7a09cfd8b9a7c52d2bca00e7c71ecff5009c9e590b5b8bff23",
                "sha256:7d6c438b80e38bd9bc1d86ca1ecaacb61dad53e8786224b297cc09101d8ea486",
                "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
                "sha256:6f2922a62d1e80438fb4838ff2a81db0186df86c2c87823273043d028511aa5d",
                "sha256:e64019983390f35d2e21868d542daae4227ad37f41b04cb5886afa533f7d037d"
            ]
        },
        "Metadata": {
            "LastTagTime": "2024-07-06T09:52:24.552707346Z"
        },
        "Container": ""
    }
]

~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker inspect bzavoevanyy/otus-reddit:1.0 -f '{{.Config.Cmd}}'
[/start.sh]

~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker exec -it reddit bash
root@79cf152581f4:/reddit# mkdir /test1234
root@79cf152581f4:/reddit# touch /test1234/testfile 
root@79cf152581f4:/reddit# rmdir /opt
root@79cf152581f4:/reddit# exit
exit

What's next:
    Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug reddit
    Learn more at https://docs.docker.com/go/debug-cli/
    
~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker diff reddit
C /root
A /root/.bash_history
C /var
C /var/log
A /var/log/mongod.log
C /var/lib
C /var/lib/mongodb
A /var/lib/mongodb/index-5-5562542421096655515.wt
A /var/lib/mongodb/journal
A /var/lib/mongodb/journal/WiredTigerLog.0000000001
A /var/lib/mongodb/journal/WiredTigerPreplog.0000000001
A /var/lib/mongodb/journal/WiredTigerPreplog.0000000002
A /var/lib/mongodb/mongod.lock
A /var/lib/mongodb/WiredTiger.lock
A /var/lib/mongodb/WiredTigerLAS.wt
A /var/lib/mongodb/collection-0-5562542421096655515.wt
A /var/lib/mongodb/diagnostic.data
A /var/lib/mongodb/diagnostic.data/metrics.2024-07-06T09-59-46Z-00000
A /var/lib/mongodb/diagnostic.data/metrics.interim
A /var/lib/mongodb/index-3-5562542421096655515.wt
A /var/lib/mongodb/index-6-5562542421096655515.wt
A /var/lib/mongodb/WiredTiger.turtle
A /var/lib/mongodb/_mdb_catalog.wt
A /var/lib/mongodb/collection-4-5562542421096655515.wt
A /var/lib/mongodb/sizeStorer.wt
A /var/lib/mongodb/storage.bson
A /var/lib/mongodb/index-1-5562542421096655515.wt
A /var/lib/mongodb/WiredTiger
A /var/lib/mongodb/WiredTiger.wt
A /var/lib/mongodb/collection-2-5562542421096655515.wt
A /test1234
A /test1234/testfile
C /tmp
A /tmp/mongodb-27017.sock
D /opt

~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker stop reddit && docker rm reddit
reddit
reddit

~/OTUS_HOMEWORK/bzavoevanyy_microservices/docker-monolith git:[docker-2]
docker run --name reddit --rm -it bzavoevanyy/otus-reddit:1.0 bash 
root@d6b22aa0c416:/reddit# ls /
bin  boot  dev  etc  home  lib  media  mnt  opt  proc  reddit  root  run  sbin  srv  start.sh  sys  tmp  usr  var
root@d6b22aa0c416:/reddit# 
```