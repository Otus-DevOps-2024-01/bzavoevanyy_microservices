# bzavoevanyy_microservices

## ДЗ № 15 Docker: сети, docker-compose

1. None network driver
    - внутри контейнера из сетевых интерфейсов существует только loopback
        ```bash
            docker run -ti --rm --network none joffotron/docker-net-tools -c ifconfig
        Unable to find image 'joffotron/docker-net-tools:latest' locally
        latest: Pulling from joffotron/docker-net-tools
        3690ec4760f9: Pull complete
        0905b79e95dc: Pull complete
        Digest: sha256:5752abdc4351a75e9daec681c1a6babfec03b317b273fc56f953592e6218d5b5
        Status: Downloaded newer image for joffotron/docker-net-tools:latest
        lo        Link encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1%32555/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:0 errors:0 dropped:0 overruns:0 frame:0
        TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
       ```

2. Host network driver
    ```bash
    docker run -ti --rm --network host joffotron/docker-net-tools -c ifconfig
    br-31b03d9ff99c Link encap:Ethernet  HWaddr 02:42:A5:EE:2A:7F  
              inet addr:172.18.0.1  Bcast:172.18.255.255  Mask:255.255.0.0
              inet6 addr: fe80::42:a5ff:feee:2a7f%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:266603 errors:0 dropped:0 overruns:0 frame:0
              TX packets:311904 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:378837221 (361.2 MiB)  TX bytes:1741986517 (1.6 GiB)
    
    docker0   Link encap:Ethernet  HWaddr 02:42:FD:FD:B0:11  
              inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
              UP BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
    
    eth0      Link encap:Ethernet  HWaddr D0:0D:14:00:A6:5A  
              inet addr:10.128.0.4  Bcast:10.128.0.255  Mask:255.255.255.0
              inet6 addr: fe80::d20d:14ff:fe00:a65a%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:552101 errors:0 dropped:0 overruns:0 frame:0
              TX packets:470589 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:1765601505 (1.6 GiB)  TX bytes:409346382 (390.3 MiB)
    
    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1%32719/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:21050 errors:0 dropped:0 overruns:0 frame:0
              TX packets:21050 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:1609243 (1.5 MiB)  TX bytes:1609243 (1.5 MiB)
    
    veth0fa9c58 Link encap:Ethernet  HWaddr F2:D1:05:42:19:BD  
              inet6 addr: fe80::f0d1:5ff:fe42:19bd%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:13072923 errors:0 dropped:0 overruns:0 frame:0
              TX packets:8046821 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:1295055934 (1.2 GiB)  TX bytes:1889973859 (1.7 GiB)
    
    veth6dd53d0 Link encap:Ethernet  HWaddr 06:8D:FF:B0:E7:7D  
              inet6 addr: fe80::48d:ffff:feb0:e77d%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:61655 errors:0 dropped:0 overruns:0 frame:0
              TX packets:93747 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:167766557 (159.9 MiB)  TX bytes:835368929 (796.6 MiB)
    
    vethc057498 Link encap:Ethernet  HWaddr BE:65:D9:DD:60:17  
              inet6 addr: fe80::bc65:d9ff:fedd:6017%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:70364 errors:0 dropped:0 overruns:0 frame:0
              TX packets:114421 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:170268850 (162.3 MiB)  TX bytes:869404689 (829.1 MiB)
    
    vethc093fbc Link encap:Ethernet  HWaddr 12:EE:47:B5:34:4D  
              inet6 addr: fe80::10ee:47ff:feb5:344d%32719/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:7881381 errors:0 dropped:0 overruns:0 frame:0
              TX packets:12876880 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:1842140819 (1.7 GiB)  TX bytes:1239918427 (1.1 GiB)
    
    ```

   Результат идентичен с выводом команды docker-machine ssh docker-host ifconfig

   При попытке запустить несколько nginx в одной и той же сети и на одном порту запуститься только один контейнер,
   остальные упадут с ошибкой Address already in use
    ```bash
    docker run --network host -d nginx
    ```
   ```bash
   yc-user@docker-host:~$ sudo ip netns
    default
    254ab494031c (id: 3)
    462b98c76129 (id: 2)
    385ff67d5d1f (id: 1)
    46fa337e8327 (id: 0)
    ```
   ip netns exec <namespace> <command> - позволит выполнять команды в выбранном namespace

3. Bridge network driver
   Запускаем контейнеры с сетевыми алиасами

   ```bash
      docker network create reddit --driver bridge
      f061a498d3b16d510a126c088358fdf5b826f8464a11d8629d70e88393ea38c4
      docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
      fe041687ca8b2cc5f2870d3a26a8865249ff9a4c666dc3044497a9fd9d229811
      docker run -d --network=reddit --network-alias=post bzavoevanyy/post:1.0
      ...
      docker run -d --network=reddit --network-alias=comment bzavoevanyy/comment:1.0
      ...
      docker run -d --network=reddit -p 9292:9292 bzavoevanyy/ui:1.0
      ...
   
   ```

   Проверяем - все работает!

   Запустим наш проект в 2-х bridge сетях. Так , чтобы сервис ui не имел доступа к базе данных

   ```bash
      docker network create back_net --subnet=10.0.2.0/24
      35fd06328824bc6de1d6ed5f9bce7f90bc9a104eb53dffe165f1feadcb6a1ca9
      docker network create front_net --subnet=10.0.1.0/24
      4c54802565e1526c25b6d314819a70113831ce71dc4d781dbe628f16421d5a23
      docker run -d --network=front_net -p 9292:9292 --name ui bzavoevanyy/ui:1.0
      7b613e99221719b99e84ae49718680d01eb6ca1d043e116cd9adaff0c40b48c5
      docker run -d --network=back_net --name comment bzavoevanyy/comment:1.0
      9616a2fec57b1e2527e7ea913c5b549e6b31bd7bfde3d115fd4820148dc3837c
      docker run -d --network=back_net --name post bzavoevanyy/post:1.0 
      91bd5b7f64654c8df12198cd4d43eeb6b9f89b1cb6dc5ba3cad8034e09b89f57
      docker run -d --network=back_net --name mongo_db \
      --network-alias=post_db --network-alias=comment_db mongo:latest 
      8867eb666850b2fee198a0a14dbb9bcf164d727ed9346f9a89edb58b87d1cc8f
   ```

   Подключим контейнеры ко второй сети
   ```bash
   docker network connect front_net post
   docker network connect front_net comment
   ```
   
   Проверяем - все работает!

4. Рассмотрим, как выглядит сеть после выполненных операций

   ```bash
   yc-user@docker-host:~$ sudo docker network ls
   NETWORK ID     NAME               DRIVER    SCOPE
   35fd06328824   back_net           bridge    local
   2f9566c1ff9f   bridge             bridge    local
   4c54802565e1   front_net          bridge    local
   3fb02ed78365   host               host      local
   f5c9ae1fd484   none               null      local
   f061a498d3b1   reddit             bridge    local
   31b03d9ff99c   teamcity_default   bridge    local
   
   yc-user@docker-host:~$ ifconfig | grep br
   br-31b03d9ff99c: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 172.18.0.1  netmask 255.255.0.0  broadcast 172.18.255.255
   br-35fd06328824: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 10.0.2.1  netmask 255.255.255.0  broadcast 10.0.2.255
   br-4c54802565e1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 10.0.1.1  netmask 255.255.255.0  broadcast 10.0.1.255
   br-f061a498d3b1: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
           inet 172.19.0.1  netmask 255.255.0.0  broadcast 172.19.255.255
           inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
           inet 10.128.0.4  netmask 255.255.255.0  broadcast 10.128.0.255
           
   yc-user@docker-host:~$  brctl show br-35fd06328824
   bridge name     bridge id               STP enabled     interfaces
   br-35fd06328824         8000.02424805cfdb       no              veth00874a0
                                                           veth583366d
                                                           veth8d65d60
                                                           
   yc-user@docker-host:~$ sudo iptables -nL -t nat
   Chain PREROUTING (policy ACCEPT)
   target     prot opt source               destination         
   DOCKER     all  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
   
   Chain INPUT (policy ACCEPT)
   target     prot opt source               destination         
   
   Chain OUTPUT (policy ACCEPT)
   target     prot opt source               destination         
   DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL
   
   Chain POSTROUTING (policy ACCEPT)
   target     prot opt source               destination         
   MASQUERADE  all  --  10.0.1.0/24          0.0.0.0/0           
   MASQUERADE  all  --  10.0.2.0/24          0.0.0.0/0           
   MASQUERADE  all  --  172.19.0.0/16        0.0.0.0/0           
   MASQUERADE  all  --  172.17.0.0/16        0.0.0.0/0           
   MASQUERADE  all  --  172.18.0.0/16        0.0.0.0/0           
   MASQUERADE  tcp  --  172.18.0.2           172.18.0.2           tcp dpt:5432
   MASQUERADE  tcp  --  172.18.0.3           172.18.0.3           tcp dpt:8111
   MASQUERADE  tcp  --  10.0.1.2             10.0.1.2             tcp dpt:9292
   
   Chain DOCKER (2 references)
   target     prot opt source               destination         
   RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
   RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
   RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
   RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
   RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
   DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:5433 to:172.18.0.2:5432
   DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8112 to:172.18.0.3:8111
   DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:9292 to:10.0.1.2:9292    
   
   yc-user@docker-host:~$ ps ax | grep docker-proxy
       901 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 5433 -container-ip 172.18.0.2 -container-port 5432
       907 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 5433 -container-ip 172.18.0.2 -container-port 5432
      1451 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8112 -container-ip 172.18.0.3 -container-port 8111
      1457 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 8112 -container-ip 172.18.0.3 -container-port 8111
     92640 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 9292 -container-ip 10.0.1.2 -container-port 9292
     92647 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 9292 -container-ip 10.0.1.2 -container-port 9292
     97071 pts/0    S+     0:00 grep --color=auto docker-proxy                                                    
   ```

5. Docker compose

   - Добавил docker-compose.yaml со всеми компонентами приложения
   - Добавил переменные окружения в .env файл
   - Изменил базовое имя проекта, один из способов - добавить переменную окружения COMPOSE_PROJECT_NAME
   
