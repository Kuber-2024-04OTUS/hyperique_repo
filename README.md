
# Репозиторий для выполнения ДЗ №14

1. Клонируем репозиторий kubernetes-debug в новую ветку kubernetes-prod

2. В качестве серверов буду использовать виртуальные машины в своем окружении vmware:

   ![image](https://github.com/user-attachments/assets/0c88008c-9efe-47d7-9287-477225dc7b80)


3. Для управления несколькими серверами установим утилиту pdsh:

   ` sudo apt install pdsh `
   Создадим файл hosts:
   
    ![image](https://github.com/user-attachments/assets/ee146239-d7c5-4cdf-8557-50e6e7c43be9)

5. Настроим сервера:
 
   `pdsh -w ^hosts -R ssh "sudo swapoff -a"
    pdsh -w ^hosts -R ssh "cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF" 
    pdsh -w ^hosts -R ssh "sudo modprobe br_netfilter"
    pdsh -w ^hosts -R ssh "sudo modprobe overlay"
    pdsh -w ^hosts -R ssh "echo 'br_netfilter' | sudo tee -a /etc/modules-load.d/br_netfilter.conf"
    pdsh -w ^hosts -R ssh "echo '1' | sudo tee /proc/sys/net/bridge/bridge-nf-call-iptables"
    pdsh -w ^hosts -R ssh "echo '1' | sudo tee /proc/sys/net/bridge/bridge-nf-call-ip6tables"
    pdsh -w ^hosts -R ssh "echo '1' | sudo tee /proc/sys/net/ipv4/ip_forward"
    pdsh -w ^hosts -R ssh "echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf && echo 'net.ipv4.conf.all.accept_redirects = 0' | sudo tee -a /etc/sysctl.conf && echo 'net.ipv4.conf.all.send_redirects = 0' | sudo tee -a /etc/sysctl.conf && echo 'net.netfilter.nf_conntrack_max = 131072' | sudo tee -a /etc/sysctl.conf && echo 'net.ipv4.tcp_syncookies = 1' | 
    sudo tee -a /etc/sysctl.conf && sudo sysctl -p"`
    
   
6. Установим kubeadm, kubelet, kubectl, containerd:

   ` pdsh -w ^hosts -R ssh "sudo apt-get install -y apt-transport-https ca-certificates curl gpg"
     pdsh -w ^hosts -R ssh "curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg"
     pdsh -w ^hosts -R ssh "echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list" #добавим репозиторий версии 1.29
     pdsh -w ^hosts -R ssh "sudo apt-get update"
     pdsh -w ^hosts -R ssh "sudo apt-get install -y kubelet kubeadm kubectl containerd" #установим пакеты
     pdsh -w ^hosts -R ssh "sudo apt-mark hold kubelet kubeadm kubectl" #закрепим версию
     pdsh -w ^hosts -R ssh "sudo systemctl enable --now kubelet"`
   
     Проинициализируем кластер:

    `  sudo kubeadm init --pod-network-cidr=10.244.0.0/16 `

    ![image](https://github.com/user-attachments/assets/7630f6e3-da10-4e65-9e5b-ba3fa67e3bbb)

    добавим kubeconfig:

   `mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config`

   Проверим кластер:

   ![image](https://github.com/user-attachments/assets/566303c8-4d17-4a8f-bd48-72333819fb1e)

   Установим flannel:

   `kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml`

   Убеждаемися, что все поднялось:

   ![image](https://github.com/user-attachments/assets/c29a9d92-e4aa-4de2-8f95-0dfb8eaf3d3f)

   ![image](https://github.com/user-attachments/assets/b600fcaa-b599-42dc-bdf5-7fcc9b03fff5)

7. Подключаем остальные ноды:

   `pdsh -w ^hosts -R ssh kubeadm join 10.10.99.56:6443 --token 765lxg.decb0p91suvs4f71 \
        --discovery-token-ca-cert-hash sha256:0098ee50aa00c77937b03d4281380435bea4b37ee04a425897681979016c83bf`
   Проверяем:

   ![image](https://github.com/user-attachments/assets/e6e43a74-09a4-452e-b654-21be088db00e)

   
8. Обновим местер в кластере:

   сменим репозиторий:

    `  "curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg /
       echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ / | sudo tee /etc/apt/sources.list.d/kubernetes.list
       sudo apt-get update" `
   Посмотрим на версии kubeadm в репозитории:
   
   `apt-cache madison kubeadm`
   
   ![image](https://github.com/user-attachments/assets/1f309c35-6b58-4bbb-9024-da498ab8e5b4)

   Снимем пакеты с холда:

   `sudo apt-mark unhold kubeadm kubelet kubectl`

   И установим kubeadm последней версии:

   ` sudo apt-get install -y kubeadm=1.30.3-1.1`

   ` kubeadm version`

   ![image](https://github.com/user-attachments/assets/4e7aed10-b740-41ff-ba70-ff10573e8486)


   Аналогично обновимм kubelet и kubectl:

   `sudo apt-get install -y kubelet=1.30.3-1.1  kubectl=1.30.3-1.1`

   ![image](https://github.com/user-attachments/assets/da1e7dc3-3a02-424f-abdf-a2d4bd7e9186)

   ![image](https://github.com/user-attachments/assets/71769932-841d-4c21-81b5-f5de30b888e6)

9. Обновим worker ноды:

   
 
   ![image](https://github.com/user-attachments/assets/29f0a14e-32d5-4695-9189-4f9122c1fd88)

    Обновим репозитории на нодах:

   `pdsh -w ^hosts -R ssh "curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg"
    pdsh -w ^hosts -R ssh "echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list"
    pdsh -w ^hosts -R ssh "sudo apt-get update"`

    Снимем холд:

    ` pdsh -w ^hosts -R ssh "sudo apt-mark unhold kubeadm kubelet kubectl"`
   
    Обновляем вторую ноду:
    `kubectl drain k8s-ustinovich-2 --ignore-daemonsets --delete-emptydir-data`
    `ssh 10.10.99.57`
    `sudo apt-get install -y kubeadm=1.30.3-1.1`
    `sudo kubeadm upgrade node`
    `sudo apt-get install -y kubelet=1.30.3-1.1 kubectl=1.30.3-1.1`
    `kubectl uncordon k8s-ustinovich-2`

    ![image](https://github.com/user-attachments/assets/9685cbeb-1886-4e18-8ddf-a42649d05c45)

   По аналогии третью и четвертую:
   
    `kubectl drain k8s-ustinovich-3 --ignore-daemonsets --delete-emptydir-data`
    `ssh 10.10.99.58`
    `sudo apt-get install -y kubeadm=1.30.3-1.1`
    `sudo kubeadm upgrade node`
    `sudo apt-get install -y kubelet=1.30.3-1.1 kubectl=1.30.3-1.1`
    `kubectl uncordon k8s-ustinovich-3`
   
    `kubectl drain k8s-ustinovich-4 --ignore-daemonsets --delete-emptydir-data`
    `ssh 10.10.99.59`
    `sudo apt-get install -y kubeadm=1.30.3-1.1`
    `sudo kubeadm upgrade node`
    `sudo apt-get install -y kubelet=1.30.3-1.1 kubectl=1.30.3-1.1`
    `kubectl uncordon k8s-ustinovich-4`

   ![image](https://github.com/user-attachments/assets/10150cb0-5833-4ef5-bffe-0791e3b3a6be)

Задание выполнено успешно.

Задание со *.

Ранее для Дз и в рамках эксперимента я уже поднимал рабочий кластер через kubespray, прилагаю файл конфигурации развертывания через ansible (hosts.yaml), а так же вывод команды kubectl get nodes -o wide моего распределенного кластера:

![image](https://github.com/user-attachments/assets/6e335156-eacd-4e27-896e-fe64c7b82baf)


   


   
    
