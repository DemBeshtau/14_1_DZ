# Настройка мониторинга Prometheus-Grafana
1. На системе Prometheus-Grafana настройть 4 дашборда:
   - процессор;<br/>
   - память;<br/>
   - сеть;<br/>
   - диск.<br/>
### Исходные данные  ###
&ensp;&ensp;ПК на Linux c 8 ГБ ОЗУ или виртуальная машина (ВМ) с включенной Nested Virtualization.<br/>
&ensp;&ensp;Предварительно установленное и настроенное ПО:<br/>
&ensp;&ensp;&ensp;Hashicorp Vagrant (https://www.vagrantup.com/downloads);<br/>
&ensp;&ensp;&ensp;Oracle VirtualBox (https://www.virtualbox.org/wiki/Linux_Downloads).<br/>
&ensp;&ensp;&ensp;Все действия проводились с использованием Vagrant 2.4.0, VirtualBox 7.0.14 и образа<br/> 
&ensp;&ensp;ubuntu/jammy64 версии 20240301.0.0.<br/> 
### Ход решения ###
1. Установка на ВМ Prometheus:<br/>
```shell
root@monitoring:~# apt update
...

root@monitoring:~# apt install -y prometheus
...

root@monitoring:~# apt list installed prometheus
Listing... Done
prometheus/jammy-updates,jammy-security,now 2.31.2+ds1-1ubuntu1.22.04.2 amd64 [installed]
N: There is 1 additional version. Please use the '-a' switch to see it

root@monitoring:~# systemctl status prometheus
● prometheus.service - Monitoring system and time series database
     Loaded: loaded (/lib/systemd/system/prometheus.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-06-18 14:46:40 UTC; 4min 42s ago
       Docs: https://prometheus.io/docs/introduction/overview/
             man:prometheus(1)
   Main PID: 612 (prometheus)
      Tasks: 8 (limit: 2309)
     Memory: 65.5M
        CPU: 605ms
     CGroup: /system.slice/prometheus.service
             └─612 /usr/bin/prometheus
...
```
2. Установка на ВМ агента (экспортера) Prometheus:<br/>
```shell
root@monitoring:~# apt install -y prometheus-node-exporter
...

root@monitoring:~# apt list installed prometheus-node-exporter
Listing... Done
prometheus-node-exporter/jammy-updates,jammy-security,now 1.3.1-1ubuntu0.22.04.2 amd64 [installed,automatic]
N: There is 1 additional version. Please use the '-a' switch to see it

root@monitoring:~# systemctl status prometheus-node-exporter
● prometheus-node-exporter.service - Prometheus exporter for machine metrics
     Loaded: loaded (/lib/systemd/system/prometheus-node-exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-06-18 14:46:40 UTC; 6min ago
       Docs: https://github.com/prometheus/node_exporter
   Main PID: 611 (prometheus-node)
      Tasks: 7 (limit: 2309)
     Memory: 19.9M
        CPU: 827ms
     CGroup: /system.slice/prometheus-node-exporter.service
             └─611 /usr/bin/prometheus-node-exporter
...
```
3. Установка на ВМ пакета Grafana:<br/>
```shell
root@monitoring:~# dpkg -i grafana_10.2.2_amd64.deb
...
root@monitoring:~# systemctl enable --now grafana-server

root@monitoring:~# apt list installed grafana
Listing... Done
grafana/now 10.2.2 amd64 [installed,local]

root@monitoring:~# systemctl status grafana-server 
● grafana-server.service - Grafana instance
     Loaded: loaded (/lib/systemd/system/grafana-server.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-06-18 14:46:40 UTC; 19min ago
       Docs: http://docs.grafana.org
   Main PID: 603 (grafana)
      Tasks: 12 (limit: 2309)
     Memory: 186.9M
        CPU: 2.925s
     CGroup: /system.slice/grafana-server.service
...
```
4. Проверка портов, прослушиваемых установленными сервисами:<br/>
```shell
root@monitoring:/# ss -ntlp
State        Recv-Q       Send-Q              Local Address:Port               Peer Address:Port       Process                                          
LISTEN       0            4096                127.0.0.53%lo:53                      0.0.0.0:*           users:(("systemd-resolve",pid=563,fd=14))       
LISTEN       0            128                       0.0.0.0:22                      0.0.0.0:*           users:(("sshd",pid=683,fd=3))                   
LISTEN       0            4096                            *:9090                          *:*           users:(("prometheus",pid=612,fd=8))             
LISTEN       0            4096                            *:9100                          *:*           users:(("prometheus-node",pid=611,fd=3))        
LISTEN       0            128                          [::]:22                         [::]:*           users:(("sshd",pid=683,fd=4))                   
LISTEN       0            4096                            *:3000                          *:*           users:(("grafana",pid=603,fd=13))         
```
5. Демонстрация работы дашбордов в Grafana после настройки данного сервиса:<br/>
![изображение](https://github.com/DemBeshtau/14_1_DZ/assets/149678567/18b461eb-ade7-4871-89e2-3328a7a7a0cb)
