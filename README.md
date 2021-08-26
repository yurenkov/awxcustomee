#### Репозиторий для сборки свей версии AWX-ee ####
#### Ubuntu server 20.04 ####

#### 1. Клонируем репозиторий ####
```
git clone https://github.com/pivzavoz/customawxee.git
```
#### 2. Устанавливаем зависимости для сборки образа. Ctrl+C/Ctrl+V. (docker/python-is-python3/pip3/ansible/ansible-builder) ####
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

sudo groupadd docker 
sudo usermod -aG docker $USER
newgrp docker

sudo systemctl enable docker.service
sudo systemctl enable containerd.service

sudo apt-get install python-is-python3 python3-pip

pip install ansible
pip install ansible-builder

cd customawxee
sudo chmod +x context/run.sh

```
#### 3. Добавляем нужные нам ansible coollection  в файл  requirements.yml ####
nano requirements.yml
```
--- 
collections: 
  - community.general
  - community.routeros
  - ansible.netcommon
  - community.network
  - community.vmware
  - ansible.windows
  - awx.awx
  - community.zabbix
```
#### 4. Добавляем нужные нам python module  в файл  requirements.txt ####
nano requirements.txt 
```
urllib3 
git+https://github.com/ansible/ansible-builder.git@devel#egg=ansible-builder 
netaddr 
pyvmomi 
git+https://github.com/vmware/vsphere-automation-sdk-python.git 
pywinrm 
pywinrm[kerberos]
pywinrm[credssp]
awxkit
zabbix-api
```
#### 5. Запускаем сборку образа ####
```
ansible-builder build --tag=quay.io/##myrepo##/awxcustomee:latest --context=./context --container-runtime=docker
```
#### 6. Загружаем образ на DockerHUB или Quay.io ####
```
# для DockerHUB
docker login
#вводим логин пароль 
docker push ##myrepo##/awxcustomee:latest
# для Quay.io
sudo docker logout
docker login quay.io
#вводим логин пароль 
docker push quay.io/##myrepo##/awxcustomee:latest
