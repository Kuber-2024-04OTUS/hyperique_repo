

# Репозиторий для выполнения ДЗ №9
1. Клонируем репозиторий kubernetes-monitoring в новую ветку kubernetes-logging

2. Применяем лейбл и taint для ноды, которую будем использовать в качестве инфраструктурной:

   `kubectl label node cl190vqqqoksldfc3u8c-alif node-role.kubernetes.io/infra=infra`

   `kubectl taint nodes cl190vqqqoksldfc3u8c-alif infra=true:NoSchedule`

   `kubectl get node -o wide --show-labels` :

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e1374d55-0d26-48d9-8871-b8f6767d4790)

   `kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints`
   
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/f87ee5b0-86ec-41d3-9842-c05d27969096)

3. Немного отойдем от условий ДЗ и для экономии ресурсов будем использовать не яндекс S3 бакет, а инстанс S3 minio, который развернем на кластере, который использовали для выполнения предыдущих ДЗ.

   - Через minio operator развернем tennant и создадим бакеты для loki:
     
     ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e2d66468-0150-4eff-a671-dd464b0113bb)

   - Создадим access key и access key id для доступа к бакетам:
  
     ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/fe6551c2-cc29-4e64-91b5-9628bfc45f1c)

 4. Далее запулим чарт с локи и создадим кастомный values:

    `helm pull grafana/loki`

    В качестве схемы развертывания будем использовать монолитную, Single Binary. Устанавливаем:

    `helm install loki --namespace=logging --values=loki/andrewloki.yaml loki/`

    Убеждаемся, что наш инстанс развернулся и залетел только на ифнраструктурную ноду:

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5e675954-ca67-436c-820d-616ce47a5d85)


5. Пулим чарт промтейла grafana/promtail, настраиваем values для размещения на обоих нодах и устанавливаем:
   
    `helm install promtail --namespace=logging --values=values.yaml promtail/`

  Убеждаемся, что promtail залетел на обе ноды:

  ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/a15bf206-4167-4a28-9d8f-b376920b1be4)

6. Создаем кастомный values.yaml для графаны и ставим из helm чарта вместе с prometheus stack:

   `helm install grafana --namespace=logging --values=grafana/values.yaml prometheus-community/kube-prometheus-stack`

   Убеждаемся, что все залетело на infra ноду:
   
   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/94d4eb1e-8ab1-4db9-bf9d-9b45e5bfd91f)


7. Авторизируемся в графану и добавляем loki gateway:

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/1f8aca9d-1212-473e-89f9-ffe8100273fd)

8. Убеждаемся, что loki как data source работает и отдает метрики кластера, которые ему предоставил promtail.

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/6918d0ab-58eb-4c7a-bd9d-271d0920a43d)

9. Так же убеждаемся, что Loki успешно подключился к нашему S3 хранилищу.
    
 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/26454fbc-6a8d-4743-913b-0575c939dcf5)





