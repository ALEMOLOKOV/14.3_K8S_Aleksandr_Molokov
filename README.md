# 14.3_K8S_Aleksandr_Molokov

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.


### Ответ

#### Кластер развернут с помощью kubespray в нем уже установлен сетевой плагин Calico

#### Последовательность команд развертывания кластера
sudo apt update

sudo apt install pip

git clone https://github.com/kubernetes-sigs/kubespray

cd kubespray

sudo pip3 install -r requirements.txt

cp -rfp inventory/sample inventory/mycluster

#### Декларируем ip если установка со своего пк берем внешние ip если установка с мастерноды берем внутренние ip

declare -a IPS=(10.129.0.31 10.129.0.10 10.129.0.27) 

CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

#### необходимо добавить ansible_user для подключения к каждой ноде

vim ./inventory/mycluster/hosts.yaml


#### добавдление ssh ключа на ноды

vim ~/.ssh/id_rsa

chmod 0700 ~/.ssh/id_rsa

#### запуск плейбука

ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml -b -v

#### результат

![kluster UP](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/e9d02c3a-fd78-488e-8c02-f88fae18b8cc)

#### 1. Deployments
![Frontend](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-frontend.yaml)

![Backend](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-backend.yaml)

![Cache](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-cache.yaml)

#### Pods после развертывания манифестов
![pods](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/0342e005-dfbf-4624-b106-64377824474f)

#### 2. Создание namespace app

![создание NS app](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/1791dfa1-d5a8-4e69-ab6a-952edcdead68)

#### 3. Networkpolicy

![default-nwpolicy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/default-nwpolicy.yaml)

![backend-policy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/backend-policy.yaml)

![cache-policy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/cach-policy.yaml)

#### проверка доступа front-back
![front-back](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/4ee91736-af76-431f-84b1-9f9095d4bf0c)

#### проверка доступа back-cache
![back-cache](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/4a29dc5f-d4b7-45c1-8330-7bc4ae26b7c7)

#### проверка доступа fronend-backend-cache
![Inkedall net1](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/a4c4a107-01ff-4fcf-bedd-f7537f3db114)




