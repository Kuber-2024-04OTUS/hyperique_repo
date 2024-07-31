
# Репозиторий для выполнения ДЗ №13

1. Клонируем репозиторий kubernetes-csi в новую ветку kubernetes-debug

2. Создадим манифест для развертывания пода, подключим через configmap nginx.conf для развернутого логирования:

     `kubectl apply -f pod.yaml`

3. Создаем эфемерный контейнер для дебага пода:

     `kubectl debug -it nginx-distroless --image=busybox --target=nginx --share-processes`

4. Для того, что бы получить доступ к файловой системе пода, нам нужно обратиться к временной файловой системе proc, в которой и будет лежать содержимое пода
   
     `cd /proc/1/root/etc/nginx/`

     `ls -la`
   
   ![image](https://github.com/user-attachments/assets/4ff596ae-682a-4b44-bdb0-370523a877c7)

6. Запустим команду tcpdump:

   `tcpdump -nn -i any -e port 80`

 ![image](https://github.com/user-attachments/assets/90ed3dd8-62a8-4734-8ac0-b95564e52083)

 Как видим, утилита не найдена - значит будем использовать другой image для kubectl debug:

   `kubectl debug -it nginx-distroless -n homework --image=nicolaka/netshoot --target=nginx --share-processes`

   `tcpdump -nn -i any -e port 80`

 ![image](https://github.com/user-attachments/assets/1afaaa5d-00f0-456f-a9a8-aeb69fa8de69)

 Обратимся через curl к нашему поду:

 ![image](https://github.com/user-attachments/assets/1c42d01c-4073-48ee-825c-6ae458a6f90c)

    `curl 10.233.64.253`
   
Убеждаемся, что наши запросы отображаются: (файл tcpdump.log приложен к заданию)

 ![image](https://github.com/user-attachments/assets/43eee900-49b6-499a-a37d-56bca54175ab)

 
6. С помощью kubectl debug запустим отладочный под для ноды, на которой расположен наш pod:

   `kubectl debug node/node2-shb --image=busybox -ti`

  Файловая система ноды будет располагаться по пути /host. Найдем путь к логам пода nginx-distroless
  
   `cd /host/var/log/containers`

   ![image](https://github.com/user-attachments/assets/5348fb51-9e25-48a7-ad44-5775b24a726d)

  Откроем файл логов distroless пода:

   `cat /host/var/log/pods/homework_nginx-distroless_a9d89b59-a327-469c-9f46-31b693d7e905/nginx/0.log`

  Как видим в логе отображаются запросы, которые мы ранее делали к web серверу через curl: (файл nginx.log приложен к дз)

  ![image](https://github.com/user-attachments/assets/ef949379-ac70-486c-b7eb-5304d99b7203)

7. Задание со *

   Запустим эфемерный под:

    `kubectl debug -it nginx-distroless -n homework --image=nicolaka/netshoot --target=nginx --share-processes`

   Найдем корневой процесс nginx:

    `ps aux | grep nginx`

   ![image](https://github.com/user-attachments/assets/62e99520-8f33-4947-aff5-23f3746a2ef6)

   И попробуем выполнить strace:

    `strace -p 1`

   ![image](https://github.com/user-attachments/assets/675618ad-b169-4c3d-afd9-f0755a01607e)

   Как видим у нас нет прав для запуска этой команды. Это связано с securitycontext подов, даже эфемерный контейнер не имеет возможности интегрироваться в системные вызовы. Для того, что бы исправить эту ситуацию поду должен быть передан securitycontext SYS_PTRACE.
   Сделать это можно следующим образом:
   На github есть enhancement для kubectl из которого можно увидеть, что для kubectl debug существует система профилей, позволяющих менять securitycontext в зависимости от задач дебага (https://github.com/kubernetes/enhancements/tree/master/keps/sig-cli/1441-kubectl-debug#debugging-profiles), для дебага на уровне системных прерываний мы можем использовать профиль     general, который устанавливает переменную SYS_PTRACE:
   ![image](https://github.com/user-attachments/assets/10dab845-b321-4837-81d1-434cbeb4cfb3)


   Запустим и проверим:

    `kubectl debug -it nginx-distroless -n homework --image=nicolaka/netshoot --target=nginx --share-processes --profile=general`

   ![image](https://github.com/user-attachments/assets/9a2eb0e9-49b8-4f9d-a685-a23ae00b74f1)

   Как видим процесс приаттачился, задание выполнено.

   

 
  

  
