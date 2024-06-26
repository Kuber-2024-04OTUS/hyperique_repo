
# Репозиторий для выполнения ДЗ №10
1. Клонируем репозиторий kubernetes-logging в новую ветку kubernetes-gitops

2. Использовать будем кластер из предыдущего задания kubernetes-logging 
   
3. Пулим helm chart для argocd.
   
   `helm repo add argo https://argoproj.github.io/argo-helm`

   `helm update https://argoproj.github.io/argo-helm`

   `helm pull argo/argo-cd`

4. Создаем кастомный манифест myvalues.yaml и настраиваем согласно условиям ДЗ (node labels, tolerations).
   
5. Разворачиваем argocd с нашим myvalues.yaml и проверяем, что все запустилось на нужной ноде.

   `helm install argocd --namespace=argocd --values=myvalues.yaml argo-cd/`
   
  ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5dda6ce5-4d0c-4e3a-ae57-e557ad9f2578)


6. Создаем ingress и подключаемся к argocd.

   `kubectl apply -f argoingress.yaml`

   Получаем пароль для авторизации:
   
   `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/02f9e1a3-5015-456f-a76f-9b83bdc880bb)


7.  Подключаем наш репозиторий через ssh:

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/3cfcc092-71b3-426d-a04f-0ef177b25d14)

8. Устанавливаем argocd cli и подключаемся к серверу argoCD.

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5753714b-067d-44ee-ba12-2f208e3db408)

   `argocd login argocd.ustinovich.online`

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/d3a89280-882b-47d6-a7f0-899ca099c6b7)

9. Запускаем манифест для создания проекта otus в argoCD.

    `kubectl apply -f project.yaml`

   
10. Создаем манифест app.yaml для приложения из ветки kubernetes-networks нашего репозитория и запускаем.

    `argocd app create -f app.yaml app`

    Приложение создалось:

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/c95dfcaf-5aa4-4ed9-954d-1db3b299a86b)


11. Запустим синхронизацию с репозиторием и увидим, что наше приложение успешно запустилось с помощью argocd в нужной нам конфигурации и успешно функционирует:

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/cb299677-55cf-42a0-9d57-333511e0da83)

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/47fe0c3b-6420-476d-8c14-794d5530c4a9)

    ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/5c7f4e5c-e66e-4c70-890b-33ec475f94dd)


12. Создаем манифест apphelm.yaml для запуска приложения из репозитория kubernetes-templating и настраиваем согласно условиям ДЗ, запускаем:

    `argocd app create -f apphelm.yaml apphelm`

   Приложение создалось и автоматически синхронизировалось:

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/1ae7b490-acaf-4992-bc93-31a8eda6e9db)

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/e76952da-15cf-47c4-bba1-bd157cb93455)

   ![image](https://github.com/Kuber-2024-04OTUS/hyperique_repo/assets/90676858/d37c126d-466b-4570-b74a-08e534528424)

   





     




