
# Репозиторий для выполнения ДЗ №11
1. Клонируем репозиторий kubernetes-gitops в новую ветку kubernetes-vault

2. Использовать будем кластер из предыдущего задания kubernetes-gitops
   
3. Клонируем репозиторий consul из  github
   
   `git clone https://github.com/hashicorp/consul-k8s.git`

 4. Устанавливаем через helm с кастомным values consulvalues.yaml

   `helm upgrade consul --install --namespace=consul --values=kubernetes-vault/consul/consulvalues.yaml /opt/projects/consul/consul-k8s/charts/consul/`

   ![image](https://github.com/user-attachments/assets/313eac8b-3cd3-4dd3-b6df-b60728b8254f)


   развернем ingress для web-ui

   `kubectl apply -f kubernetes-vault/consul/consulingress.yaml`

  Наблюдаем, что statefulset поднялся с указанным в ДЗ количеством реплик:
  
   ![image](https://github.com/user-attachments/assets/3b988ab1-5735-4506-b7aa-b00f4d8b352c)

  Проверим работоспособность приложения:

  ![image](https://github.com/user-attachments/assets/abea1355-366c-40f6-99f3-c2359924d153)

5. Клонируем репозиторий vault из github:

    `git clone https://github.com/hashicorp/vault-helm.git`

   Конфигурируем кастомный values для helm чарта и устанавливаем:

   `helm upgrade --install vault --namespace=vault --values=kubernetes-vault/vault/valuesvault.yaml /opt/projects/vault/vault-helm/`

![image](https://github.com/user-attachments/assets/e4c12e50-e828-4c87-aa64-2daaff4af85c)

![image](https://github.com/user-attachments/assets/3b2f579d-d2a6-4a4c-bfd4-f4cb276c1859)

Распечатываем:

      kubectl exec -it vault-0  -- vault operator init
     
      kubectl exec -it vault-0  -container vault -- vault operator unseal
     
![image](https://github.com/user-attachments/assets/0aa606b5-5d0f-421f-8082-051a06b57bf5)

В consul появилось хранилище под vault:

 ![image](https://github.com/user-attachments/assets/21dd6d57-e4c0-4457-b82e-f460dfebac65)

Делаем ingress для vault и проверяем доступность:

    `kubectl apply -f kubernetes-vault/vault/vaultingress.yaml`
    
 ![image](https://github.com/user-attachments/assets/76bb7720-5168-4082-a367-aef22f015a91)

 Логинимся в vault под root:

    `vault login`

![image](https://github.com/user-attachments/assets/eca984b6-a778-4420-95a8-3df081d26e0b)

 
   
Создаем хранилище otus с secret engine kv:

   `vault secrets enable -path otus -version=2 kv`
   
Создаем секрет:

   `vault kv put otus/cred username=otus password=asajkjkahs`

 ![image](https://github.com/user-attachments/assets/6708c160-183b-4dac-a528-850bcadbcf6f)

6. Создаем Serviceaccount и CRB:

   `kubectl apply -f rbac.yaml`

7. Включаем авторизацию k8s в vault и конфигурируем:

   `vault auth enable kubernetes`

   `TOKEN_REVIEW_JWT=$(kubectl get secret vault-auth -o go-template='{{ .data.token }}' | base64 --decode)`
   
   `KUBE_CA_CERT=$(kubectl config view --raw --minify --flatten -o jsonpath='{.clusters[].cluster.certificate-authority-data}' | base64 --decode)`

   `KUBE_HOST=$(kubectl config view --raw --minify --flatten --output='jsonpath={.clusters[].cluster.server}')`
   
   `vault write auth/kubernetes/config \
    token_reviewer_jwt="$TOKEN_REVIEW_JWT" \
    kubernetes_host="$KUBE_HOST" \
    kubernetes_ca_cert="$KUBE_CA_CERT"`

![image](https://github.com/user-attachments/assets/fc37db85-af6d-467b-89f8-0c5d60e17609)

8. Создадим политику:

   `vault policy write otus-policy - <<EOF
path "otus/*" {
  capabilities = ["read", "list"]
}
EOF`

9. Создадим роль otus для k8s:
 
  `vault write auth/kubernetes/role/otus \
    bound_service_account_names="vault-auth" \
    bound_service_account_namespaces="vault" \
    policies="otus-policy" \
    ttl="1h"`

10. Устанавливаем external secrets operator:

    `helm repo add external-secrets https://charts.external-secrets.io`

    `helm install external-secrets --namespace=vault external-secrets/external-secrets`
    
11. Получим client-token для передачи в k8s.

    `curl --request POST --data '{"jwt": "'$TOKEN_REVIEW_JWT'", "role": "otus"}'  https://vault.ustinovich.online/v1/auth/kubernetes/login`

  И перекодируем его в base64

    `echo -n hvs.CAESICDEQSsPXuc-XegZpXaELo3QZGSGfLLyOvcpW3medwKDGh4KHGh2cy51bGtEREowdXJ0N1BwQ0ZqMjFnN2l0cTE | base64`

  Полученный токен разместим в secret k8s:

    `kubectl apply -f vault-otus-token.yaml`

12. Создадим secretstore:

    `kubectl apply -f otussecretstore.yaml"

   ![image](https://github.com/user-attachments/assets/41148ee1-c8ee-45d0-98fb-ce265ea9fc68)

13. И создадим externalsecret:

    `kubectl apply -f externalsecret.yaml`

    ![image](https://github.com/user-attachments/assets/6705424a-3d33-4079-b4ec-6458d6db611b)


14. Убеждаемся, что результаты ДЗ достигнуты, в k8s создан secret в нужном NS с именем otus-cred и хранящий в себе правильные значения:

    ![image](https://github.com/user-attachments/assets/a1082d38-fd22-4286-9498-51d1aa666b6c)



    

    
   


   


   
