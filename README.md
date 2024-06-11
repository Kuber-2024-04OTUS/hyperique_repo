
# Репозиторий для выполнения ДЗ №8
1. Клонируем репозиторий kubernetes-volumes в новую ветку kubernetes-monitoring
   
2. В configmap nginx нашего приложения вносим изменения, для добавления stub_status. Разворачиваем приложение из чарта:

   `helm install andrewapp myapp/`
   
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/3645817f-7097-4e2a-8145-549b4e2173be)

3. Убеждаемся, что nginx отдает метрики по указанному в config пути.

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/254db606-8612-40dd-9242-d3df9436a2ff)


4. Создаем манифест nginxexporter.yaml, в котором описываем развертывание nginx prometheus exporter и сервиса к нему.

   `kubectl apply -f nginxexporter.yaml`
   
   Убеждаемся, что exporter тянет нужные нам метрики:

 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/0926d18a-3af2-420f-8d65-e322cd347190)

   
5. Тянем helmchart для установки prometheus.

   'helm pull prometheus-community/kube-prometheus-stack'
   
Заложим в values.yaml файл чарта конфигурацию нашего кастомного serviceMonitor, а так же сконфигурируем ингрессы:

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/64f8200e-4f82-4bbb-9a0e-dd131e81cca2)


6. Устанавливаем prometheus:

   'helm install prometheus kube-prometheus-stack/ -n prometheus
   
 ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/0b193392-86ce-4614-b80c-a0f657162627)


7. Заходим в prometheus и смотрим на наличие в targets нашего nginx exporter:
   
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/054d3126-4e70-4757-a24d-c7c55f4cf483)

   Так же видим успешный scrape метрик.
   
 
8. Зайдем в grafana и наглядно увидим, что наше задание выполнено успешно:

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/c92653c3-adfb-4f2c-9978-62aeaeb18454)

