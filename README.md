# Репозиторий для выполнения ДЗ №4
1. Клонируем репозиторий kubernetes-networks в новую ветку kubernetes-volumes

2. Модифицируем deployment.yaml, создаем cm.yaml, pvc.yaml

   
3. Пулим новую ветку на сервер:

   `git pull https://github.com/Kuber-2024-04OTUS/hyperique_repo.git kubernetes-volumes`


4. Применяем манифест pvc.yaml и убеждаемся в успешности binding:

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/d96a9a88-08af-46c1-9891-8fcedfa54dbb)


5. Применяем манифест cm.yaml, переподнимаем deployment и проверяем доступность нашего файла, примапленного как configmap
 
  
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/43384947-7d16-4c09-9f06-1bdbe413d849)


   
6. Создаем PersistentVolume pv.yaml c политикой retain и storageclassname: sc-for-deployment, убеждаемся в создании.


   `kubectl apply -f pv.yaml`

   
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/10635209-70fa-4781-a2ef-25baaf9f9825)

   

7. Создаем pvc-pb.yaml для PersistentVolumeClaim с использованием политик, запускаем, проверяем бинд.

 `kubectl apply -f pvc-pb.yaml`

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/05277e8d-0c10-4bbc-89fa-7ca8075f5693)

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/db965551-4b47-4b0a-85e8-8980b4d1e3e2)



8. Запускаем deployment уже с новой PersistentVolumeClaim, смотрим на работоспособность
   
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/6c3a2e81-3c60-4281-a256-eb66475d4645)

9. Посмотрим в наш примапленый каталог в ноде миникуба:

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/ba196b70-664a-4dd2-af15-6be56b95d633)


