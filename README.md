# Репозиторий для выполнения ДЗ №6
   Клонируем репозиторий kubernetes-security в новую ветку kubernetes-templating

1. Для разделения наших заданий в ДЗ создадим два рабочих каталога - task1 и task2
   
   Для первого задания создадим структуру нашего чарта. Каталоги charts и templates, а так же манифесты Chart.yaml и values.yaml.

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/08256924-d173-47c1-972b-7129ccc3fe8b)


   В каталоге templates размещаем все необходимые для запуска нашего приложения манифесты

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5747a905-088b-4e56-9768-c85633715f6f)

   А в каталоге charts будут размещаться наши зависмости.

   Изменяем deployment.yaml на взаимодействие с values, определяем значения переменных в values.yaml и запускаем наш deployment.

`helm install hometask task1/`

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e8681146-18f4-471e-b043-13e16cee5f1b)

   Наш deployment поднялся и работает

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/af483a9c-0ffb-4708-a83d-339ae8c6d949)

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5ded3164-0469-42ca-96c6-f0ff748a6e79)

   Все условия, описанные в ДЗ - соблюдены.

 

2. Для выполнения второй части задания перейдем в каталог task2.
   - Создаем два каталога для наших окружений, dev и prod. Реализуем там values манифесты для каждого инстанса с необходимыми нам параметрами развертывания.
   - В репозитории bitnami/kafka отсутствует чарт необходимой нам версии для развертывания kafka 3.5.2
    
      `helm search repo "bitnami/kafka" -l`
     
     ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/741ad32a-9742-413b-a1d4-fb49f04d9160)

     Поэтому будем использовать image из registry docker.io и опишем это в values-prod.yaml файле. Версию чарта будем использовать ближайшую к необходимой.

   - Делаем helmfile манифест для поднятия наших окружений через helmfile.
  
   - Запускаем поулчившийся deployment через helmfile:

     `helmfile apply helmfile.yaml`

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5650583d-ab62-4ee0-aa99-e55b2743a9ae)

     `helm list -A`

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e8440cd5-7e47-4541-b633-116d7047047a)

   Наши приложения развернуты и функционируют с необходимыми нам параметрами, задача выполнена:

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/0999326f-ae55-4a87-9daa-1031d4d93fd0)