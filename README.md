## Домашнее задание: NFS.
### Цель:
#### Получить навыки работы с NFS
#### Научиться самостоятельно развернуть сервис NFS и подключить к нему клиента

### Для выполнения работы используются следующие инструменты:
- VirtualBox - среда виртуализации, позволяет создавать и выполнять виртуальные машины;
- Vagrant - ПО для создания и конфигурирования виртуальной среды. В данном случае в качестве среды виртуализации используется VirtualBox;
- Ansible - Система управления конфигурациями, написанная на языке программирования Python;
- Git - система контроля версий

### Аккаунты:
- GitHub - https://github.com/

### Создание образа
#### nfss - server
#### nfsc - client
```
vagrant up
```
### Подготовка ssh 
```
ssh-keygen -R "[127.0.0.1]:2200" -f ~/.ssh/known_hosts
ssh-keygen -R "[127.0.0.1]:2222" -f ~/.ssh/known_hosts
ssh -p 2200 vagrant@127.0.0.1 -i ~/.ssh/known_hosts
ssh -p 2222 vagrant@127.0.0.1 -i ~/.ssh/known_hosts
ansible all -m ping -i hosts.ini
```
### Настройка хоста и расшаривание на хосте nfss каталога
```
ansible-playbook server_1.yml -i hosts.ini
```
### Настройка хоста и монтирование шары на хосте nfsc
```
ansible-playbook client_2.yml -i hosts.ini
```
### Создание файла на шаре на стороне nfss
```
ansible server -m shell -a 'echo "test begin" > /srv/share/upload/check_file ' -i hosts.ini -b
```
### Проверка файла на шаре на стороне nfsc
```
ansible server -m shell -a 'echo "test begin" > /srv/share/upload/check_file ' -i hosts.ini -b
```
### Ребут хоста клиента nfsc и проверка после ребута
```
vagrant ssh nfsc -c "sudo shutdown -r now"
sleep 120
ansible client -m shell -a 'date ; who -b ; cat /mnt/upload/check_file' -i hosts.ini -b
```
### Ребут хоста сервера nfss и проверка после ребута
```
vagrant ssh nfss -c "sudo shutdown -r now"
sleep 120
ansible server -m shell -a 'date ; who -b ; cat /srv/share/upload/check_file' -i hosts.ini -b
ansible server -m shell -a 'systemctl status nfs ; systemctl status firewalld' -i hosts.ini -b
ansible server -m shell -a 'exportfs -s ; showmount -a 192.168.56.10' -i hosts.ini -b
```
### Создание файла на шаре на стороне хоста клиента nfsc
```
ansible client -m shell -a 'cat /mnt/upload/check_file' -i hosts.ini -b
ansible client -m shell -a 'echo "test end" > /mnt/upload/final_check ' -i hosts.ini -b
```
### Проверка файла на шаре на стороне хоста сервера nfss
```
ansible server -m shell -a 'cat /srv/share/upload/final_check' -i hosts.ini -b
```

### Часть выполненых команд зафиксированы script  и в проекте это out.log
