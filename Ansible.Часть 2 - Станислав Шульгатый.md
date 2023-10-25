# Домашнее задание к занятию «Ansible.Часть 2»

### Задание 1

**Выполните действия, приложите файлы с плейбуками и вывод выполнения.**

Напишите три плейбука. При написании рекомендуем использовать текстовый редактор с подсветкой синтаксиса YAML.

Плейбуки должны: 

1. Скачать какой-либо архив, создать папку для распаковки и распаковать скаченный архив. Например, можете использовать [официальный сайт](https://kafka.apache.org/downloads) и зеркало Apache Kafka. При этом можно скачать как исходный код, так и бинарные файлы, запакованные в архив — в нашем задании не принципиально.
2. Установить пакет tuned из стандартного репозитория вашей ОС. Запустить его, как демон — конфигурационный файл systemd появится автоматически при установке. Добавить tuned в автозагрузку.
3. Изменить приветствие системы (motd) при входе на любое другое. Пожалуйста, в этом задании используйте переменную для задания приветствия. Переменную можно задавать любым удобным способом.

#### Ответ
1.
```
---
- name: "Task 1"
  hosts: webservers
  gather_facts: false
  tasks:
   
    - name: "create directory user/tmp"
      file:
        path: /home/megion/tmp/
        state: directory
        owner: megion
        group: megion
   
    - name: "deleting fail"
      file:
        path: /home/megion/tmp/kafka-3.5.1-src.tgz
        state: absent
   
    - name: "dowdload arc"
      uri:
        url: https://downloads.apache.org/kafka/3.5.1/kafka-3.5.1-src.tgz
        dest: /home/megion/tmp/
       
    - name: "create directory kafka"
      file:
        path: /home/megion/kafka/3.5.1/
        state: directory
        owner: megion
        group: megion
   
    - name: "extract arc"
      unarchive:
        src: /home/megion/tmp/kafka-3.5.1-src.tgz
        dest: /home/megion/kafka/3.5.1/
        remote_src: yes

...
```
![Screenshot from 2023-10-24 14-56-28](https://github.com/megasts/home_works/assets/71494027/0d22a2bd-7d6b-4640-81f2-8c3e2eeb8f29)

![Screenshot from 2023-10-24 15-06-53](https://github.com/megasts/home_works/assets/71494027/4d6388a9-f528-4d8e-99d3-83e20084e121)



2.
```
---
- name: "Task 2"
  hosts: webservers
  become: true
  gather_facts: yes
  tasks:
    - name: "tuned install for apt"
      apt:
        update_cache: true
        cache_valid_time: 3600
        force_apt_get: true
        name: tuned
        state: present
      when: ansible_pkg_mgr == "apt"

    - name: "tuned install for yum"
      yum:
        update_cache: true
        name: tuned
        state: present
      when: ansible_pkg_mgr == "yum"


    - name: "reload"
      systemd:
        daemon_reload: true    

    - name: "tuned enable" 
      systemd:
        name: tuned
        enabled: yes

    - name: "status"
      shell: "systemctl status tuned"
...

```

![Screenshot from 2023-10-25 15-34-27](https://github.com/megasts/home_works/assets/71494027/d29105df-920a-4fb0-aa7d-37526138f39e)

![Screenshot from 2023-10-25 15-38-30](https://github.com/megasts/home_works/assets/71494027/11a88b69-bcb6-4cbb-86cf-a34a5b7eb382)

![Screenshot from 2023-10-25 15-39-00](https://github.com/megasts/home_works/assets/71494027/a7449243-14da-459a-9637-6d270e41aa91)




...

### Задание 2

**Выполните действия, приложите файлы с модифицированным плейбуком и вывод выполнения.** 

Модифицируйте плейбук из пункта 3, задания 1. В качестве приветствия он должен установить IP-адрес и hostname управляемого хоста, пожелание хорошего дня системному администратору. 



### Задание 3

**Выполните действия, приложите архив с ролью и вывод выполнения.**

Ознакомьтесь со статьёй [«Ansible - это вам не bash»](https://habr.com/ru/post/494738/), сделайте соответствующие выводы и не используйте модули **shell** или **command** при выполнении задания.

Создайте плейбук, который будет включать в себя одну, созданную вами роль. Роль должна:

1. Установить веб-сервер Apache на управляемые хосты.
2. Сконфигурировать файл index.html c выводом характеристик каждого компьютера как веб-страницу по умолчанию для Apache. Необходимо включить CPU, RAM, величину первого HDD, IP-адрес.
Используйте [Ansible facts](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html) и [jinja2-template](https://linuxways.net/centos/how-to-use-the-jinja2-template-in-ansible/). Необходимо реализовать handler: перезапуск Apache только в случае изменения файла конфигурации Apache.
4. Открыть порт 80, если необходимо, запустить сервер и добавить его в автозагрузку.
5. Сделать проверку доступности веб-сайта (ответ 200, модуль uri).

В качестве решения:
- предоставьте плейбук, использующий роль;
- разместите архив созданной роли у себя на Google диске и приложите ссылку на роль в своём решении;
- предоставьте скриншоты выполнения плейбука;
- предоставьте скриншот браузера, отображающего сконфигурированный index.html в качестве сайта.
