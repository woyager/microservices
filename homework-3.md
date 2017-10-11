# Вопросы по ДЗ17 (docker 3)

1. ifconfig для host драйвера покажет одинаковые интерфейсы для хост-машины и контейнера.
2. Работать будет первый контейнер, nginx которого займет 80-й порт. С большой вероятностью это будет первый запущенный контейнер ) Для остальных docker ps -a покажет (без -a мы увидим только один контейнер)
```
a506765f8706        nginx                   "nginx -g 'daemon ..."   4 seconds ago       Exited (1) Less than a second ago                       kind_hodgkin
c2ecdbc2e1f5        nginx                   "nginx -g 'daemon ..."   5 seconds ago       Exited (1) 2 seconds ago                                hopeful_swartz
e013adaa5d3f        nginx                   "nginx -g 'daemon ..."   7 seconds ago       Exited (1) 3 seconds ago                                brave_hugle
49c41841a4e8        nginx                   "nginx -g 'daemon ..."   2 minutes ago       Up 2 minutes                                            silly_visvesvar
```
3. При запуске с host драйвером список namespace не меняется. Для none мы увидим новый namespace, если выполнить в нем ifconfig - один loopback интерфейс.

## Bridge network driver

Тут же только copy-paste требуется?)

```
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
cb7e96ed27fa        back_net            bridge              local
c07584d579bf        front_net           bridge              local

root@docker-host:~# ifconfig | grep br
br-c07584d579bf Link encap:Ethernet  HWaddr 02:42:e7:22:fa:f6
br-cb7e96ed27fa Link encap:Ethernet  HWaddr 02:42:56:f8:75:23

root@docker-host:~# ifconfig br-cb7e96ed27fa
br-cb7e96ed27fa Link encap:Ethernet  HWaddr 02:42:56:f8:75:23
          inet addr:10.0.2.1  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::42:56ff:fef8:7523/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:95 errors:0 dropped:0 overruns:0 frame:0
          TX packets:105 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:6260 (6.2 KB)  TX bytes:15826 (15.8 KB)

root@docker-host:~# ifconfig br-c07584d579bf

br-c07584d579bf Link encap:Ethernet  HWaddr 02:42:e7:22:fa:f6
          inet addr:10.0.1.1  Bcast:0.0.0.0  Mask:255.255.255.0
          inet6 addr: fe80::42:e7ff:fe22:faf6/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:21 errors:0 dropped:0 overruns:0 frame:0
          TX packets:34 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:5336 (5.3 KB)  TX bytes:4150 (4.1 KB)

root@docker-host:~# brctl show br-c07584d579bf
bridge name     bridge id               STP enabled     interfaces
br-c07584d579bf         8000.0242e722faf6       no              veth5547d40
                                                        vethdf740ae
                                                        vethe956d97
root@docker-host:~# brctl show br-cb7e96ed27fa
bridge name     bridge id               STP enabled     interfaces
br-cb7e96ed27fa         8000.024256f87523       no              veth426a386
                                                        veth71b4fa3
                                                        veth9d32fc6

root@docker-host:~# ps ax | grep docker-proxy
 9241 ?        Sl     0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 9292 -container-ip 10.0.1.2 -container-port 9292

```
