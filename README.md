# Репозиторий для выполнения ДЗ №2 
1. Клонируем репозиторий kubernetes-intro в новую ветку kubernetes-controllers
   
2. Пулим новую ветку на сервер:

   `git pull https://github.com/Kuber-2024-04OTUS/hyperique_repo.git kubernetes-controllers`

   
3. Подготавливаем инфраструктуру к развертыванию deployment
   
   - Добавляем label homework=true к ноде кластера minikube


   `kubectl label nodes minikube homework=true`

     
   - удаляем пространство имен "homework"

   
   `kubectl delete ns homework`

 
   - для выполнения задания используем манифест namespace.yaml из предыдущего задания.

     
   `kubectl apply -f kubernetes-controllers/namespace.yaml`


   - так же применяем ConfigMap для конфига nginx


   `kubectl apply -f kubernetes-intro/configmap.yaml`


5. Разворачиваем deployment и убеждаемся в работоспособности подов.


   `kubectl apply -f kubernetes-controllers/deployment.yaml`

   
  ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/9a0a51f3-ff98-49c6-b43b-daf464947f19)

5. Поднимаем nodeport для публикации приложения, используя манифест из предыдущего задания:


   `kubectl apply -f kubernetes-intro/nodeport.yaml`


 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/4ff3d0aa-7854-4de3-aba2-6cf8c40bb4db)

6. Применяем манифест ingress для нашего приложения:


   `kubectl apply -f kubernetes-intro/ingress/testpageingress.yaml`


7. Проверяем работоспособность приложения:

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/4c4275c4-916f-4f1c-b9b8-8d3b37f01a89)

8. Дополнительно проверим работоспособность readinessProbe, для этого удалим Index.html в одном из подов и посмотрим результат.

`kubectl exec -it mywebsrv-deployment-6fcb44d67-dxbbz -n homework /bin/bash`

 `rm /homework/index.html`
 
 `exit`
 
 В результате один из подов выключается из ротации:

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e83bd217-e9aa-4870-bb07-2548fe325662)

9. Пересоберем приложение с новой версией.
   Как мы видим поды переподнимаются, исключаясь из ротации по одному, в первую очередь удаляя тот, который уже выбыл из ротации.
   
 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/3b19dd60-b4f3-42c5-ad60-955488a87cdb)
 
   На выходе имеем новую версию нашего приложения:
   
 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/352bc9be-b77c-4f84-a267-f6a380003d5d)

