## Задание 1
### На лекции вы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter: поместите его в автозагрузку; предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron); удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
![image](https://user-images.githubusercontent.com/126553776/229810623-cf213f21-53be-45e2-bea0-1a5817bd3d9b.png)
sudo systemctl status node_exporter
node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2023-04-04 13:28:08 UTC; 1h ago
   Main PID: 3936 (node_exporter)
      Tasks: 5 (limit: 1122)
     Memory: 2.2M
     CGroup: /system.slice/node_exporter.service
             └─1341 /home/vagrant/node_exporter-1.3.0.linux-amd64/node_exporter
