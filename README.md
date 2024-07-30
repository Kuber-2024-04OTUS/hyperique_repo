
# Репозиторий для выполнения ДЗ №12

1. Клонируем репозиторий kubernetes-vault в новую ветку kubernetes-csi

2. Будем использовать yandex cluster из предыдущих дз.
   
3. Создаем бакет в yc

   ![image](https://github.com/user-attachments/assets/54624f72-368b-49b8-8cb5-402bd3f453f2)

4. Делаем для него serviceaccount с ключами:

   ![image](https://github.com/user-attachments/assets/e0c47180-f896-4ae0-8592-03c8aac03555)
 
5. Создаем манифест для доступа к бакету с ключами:
   
   `kubectl apply -f csisecret.yaml`
   


6. StorageClass с указанием бакета:
   
   `kubectl apply -f storageclass.yaml`

7. Устанавливаем драйвер и provisioner:

   `kubectl apply -f provisioner.yaml
    kubectl apply -f driver.yaml
    kubectl apply -f csi-s3.yaml`

8. Создаем PVC:

   `kubectl apply -f pvc.yaml`

   ![image](https://github.com/user-attachments/assets/2be79a4d-6594-474a-a640-da9485f7e760)

9. Запускаем тестовый pod

    `kubectl apply -f pod.yaml`

  Проверяем, что данные появились в бакете:
  
   ![image](https://github.com/user-attachments/assets/1d252175-982e-42d8-b6f1-03bc409850fb)

    

    
   


   


   
