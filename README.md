# Репозиторий для выполнения ДЗ №5
1. Клонируем репозиторий kubernetes-volumes в новую ветку kubernetes-security

2. Создаем манифест monitoring.yaml, в котором описываем service account monitoring, создаем роль для чтения endpoint metrics и RoleBinding для нашего аккаунта и применяем его.

   `kubectl apply -f monitoring.yaml`

3. Запускаем наш deployment с serviceaccount monitoring и убеждаемся, что все рабоатет.

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/defa86ed-3857-4655-b631-4c8b0293f937)


4. Создаем манифест cd.yaml, в котором описываем аккаунт cd и даем ему роль admin,в рамках namespace homework. Роль admin предварительно описываем в этом же манифесте, давая ей полные права на все API-группы и ресурсы в рамках namespace homework. Применяем.

   `kubectl apply -f cd.yaml`

![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/9dcff4a4-0581-4683-bf0f-8bce7161f66b)


5. Генерируем манифест cdtoken.yaml, в котором описываем создание токена со сроком жизни в сутки и применяем:

    `kubectl apply -f cdtoken.yaml`
   
  При необходимости мы можем вывести содержимое токена в файл:

    `kubectl get secret cd-token -n homework -o=jsonpath='{.data.token}' | base64 --decode > token.txt`     
 
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/f5c421de-c44c-450d-b22d-71a04ead7eec)

 
6. Получаем kubeconfig файл для нашего service account CD, я делал это с помощью LENS

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/f054ea5f-7bcb-4756-8201-7720daf3bcc1)



7. В инстанс k8s был установлен компонент kube-state-metrics в ns kube-system и развернут service.
   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/09911d7a-cb43-4428-93dd-9fba93573f6c)
   
Далее был изменен манфест deployment, в init контейнер была добавлена команда для скачивания эндпоинта /metrics из инстанса kube-state-metrics и помещения его по пути работы моего nginx.
В результате после редеплоя , по адресу приложения/metrics.html получил нужный результат:
![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/dce814c2-ffe1-4ae0-886f-9f0982f827ca)

 
