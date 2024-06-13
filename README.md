# Репозиторий для выполнения ДЗ №7

1. Клонируем репозиторий kubernetes-monitoring в новую ветку kubernetes-operators

2. Создаем манифест myCRD.yaml, в котором описываем параметры согласно домашнему заданию.

   `kubectl apply -f myCRD.yaml`
   
3. Создаем service account, clusterrole, clusterrolebinding для нашего CRD.

   `kubectl apply -f sa.yaml && kubectl apply -f crb.yaml && kubectl apply -f cr.yaml`

4. Создаем манифест для развертывания оператора.

   `kubectl apply -f mysqloperator.yaml`

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/2d5e08e4-2e55-4018-accc-faa67dad2f19)


5. Создаем манифест deployment.yaml для развертывания кастомного объекта kind: MySQL и применяем.

   `kubectl apply -f deployment.yaml`

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e2c5f642-5106-4d28-a9b1-13226b8da93a)

Как видим наш deployment с указанным образом mysql поднялся, создал нужные ресурсы и успешно функционирует.
 
 - service:
   
   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/d7805fcb-7e65-44cc-95b7-2381ee1ed4a0)

 - pv:
   
   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/ede4d951-c178-4202-9c0d-8540c0eef4eb)

 - pvc:
 - 
   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5f2f6b8f-e5dc-452a-abc1-4365f41e56e7)
 
6. Проверяем удаление объекта:
   
   `kubectl delete mysqls.otus.homework my-mysql`

   По логам оператора видим, что удаление прошло успешно, все объекты созданные оператором удалены.

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/9e10290f-852e-4fab-bc4f-7936c02d7ad0)


Доп задание:

  В манифест cr.yaml были внесены изменения для прав ClusterRole с минимально возможными правами для запуска и работы оператора без ошибок.
 
