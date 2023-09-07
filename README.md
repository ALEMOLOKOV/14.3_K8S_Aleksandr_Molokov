# 14.3_K8S_Aleksandr_Molokov

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.


### Ответ

#### Кластер развернут с помощью kubespray в нем уже установлен сетевой плагин Calico

![Yandexcloude vm](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/d14c31ef-f47a-482d-a158-da56d83001e8)

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

#### Необходимо добавить ansible_user для подключения к каждой ноде

vim ./inventory/mycluster/hosts.yaml


#### Добавдление ssh ключа на ноды

vim ~/.ssh/id_rsa

chmod 0700 ~/.ssh/id_rsa

#### Запуск плейбука

ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml -b -v

#### Результат

![kluster UP](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/cf347c61-0dc9-4640-a5ab-0c974807dd6e)

#### 1. Deployments
![Frontend](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-frontend.yaml)

![Backend](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-backend.yaml)

![Cache](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/deployment-cache.yaml)

#### Состояние объектов

![all objects](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/92d2c843-2932-49cf-bfc3-509f2b9597c0)

#### 2. Создание namespace app

![создание NS app](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/1791dfa1-d5a8-4e69-ab6a-952edcdead68)

#### 3. Networkpolicy

#### Политика Запрет на Ingress
![default-nwpolicy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/default-nwpolicy.yaml)

#### Политика Доступ frontend к backend
![backend-policy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/backend-policy.yaml)

#### Политика Доступ backend к cache
![cache-policy](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/blob/6ff3b89b7c197ccd7e2b43da206024663981b8ff/cach-policy.yaml)

#### Проверка доступа fronend-backend-cache по имени сервиса

![Доступность по сервису](https://github.com/ALEMOLOKOV/14.3_K8S_Aleksandr_Molokov/assets/109212419/801dcc7c-f542-4d56-81f8-8d584e29d301)




