# Репозиторий для выполнения ДЗ №3
1. Клонируем репозиторий kubernetes-controllers в новую ветку kubernetes-networks

2. Модифицируем deployment.yaml, создаем service.yaml и hwingress.yaml

   
3. Пулим новую ветку на сервер:

   `git pull https://github.com/Kuber-2024-04OTUS/hyperique_repo.git kubernetes-networks`


4. Поднимаем service для публикации приложения, используя соответствующий манифест и убеждаемся, что сервис поднялся:


   `kubectl apply -f service.yaml`

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/f13d56ba-3867-4992-b03b-d4cbcafbbf89)

5. Разворачиваем ingress контроллер и убеждаемся, что он запущен:

  ` minikube addons enable ingress `
  
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e2be3f55-7b0b-4262-aaef-08ea6035f9c5)

   
6. Применяем манифест ingress для нашего приложения:


   `kubectl apply -f hwingress.yaml`

7. Вносим изменения в /etc/hosts для адреса minikube
   
   `192.168.49.2 homework.otus`

8. Проверяем работоспособность приложения:

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/7438c4cf-5a71-4afc-bc90-7f9f17516927)


9. Дополнительно проверим работоспособность readinessProbe, для этого удалим Index.html в одном из подов и посмотрим результат.

`kubectl exec -it mywebsrv-deployment-6fcb44d67-dxbbz -n homework /bin/bash`

 `rm /homework/index.html`
 
 `exit`
 
 В результате один из подов выключается из ротации:

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e83bd217-e9aa-4870-bb07-2548fe325662)

10. Написана аннотация в hwingress.yaml для возможности открытия приложения не по index.html. Проверим возможность открытия проекта по пути homework.otus/homepage
    
 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/4fb282f4-ca30-4c6a-b0f3-10413a3ba017)

    

