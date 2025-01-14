# Домашнее задание к занятию 1 «Введение в Ansible»

## Подготовка к выполнению

1. Установите Ansible версии 2.10 или выше.
2. Создайте свой публичный репозиторий на GitHub с произвольным именем.
3. Скачайте [Playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть

1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте значение, которое имеет факт `some_fact` для указанного хоста при выполнении playbook.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ansible-playbook -i inventory/test.yml site.yml
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        [WARNING]: Platform darwin on host localhost is using the discovered Python interpreter at /Library/Frameworks/Python.framework/Versions/3.11/bin/python3.11, but future installation of another Python
        interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
        ok: [localhost]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [localhost] => {
            "msg": "MacOSX"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [localhost] => {
            "msg": 12
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        


2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на `all default fact`.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ansible-playbook -i inventory/test.yml site.yml
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        [WARNING]: Platform darwin on host localhost is using the discovered Python interpreter at /Library/Frameworks/Python.framework/Versions/3.11/bin/python3.11, but future installation of another Python
        interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
        ok: [localhost]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [localhost] => {
            "msg": "MacOSX"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [localhost] => {
            "msg": "all default fact"
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-playbook -i inventory/prod.yml site.yml 
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        ok: [ubuntu]
        ok: [centos7]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "CentOS"
        }
        ok: [ubuntu] => {
            "msg": "Ubuntu"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "el"
        }
        ok: [ubuntu] => {
            "msg": "deb"
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   



5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились значения: для `deb` — `deb default fact`, для `el` — `el default fact`.
6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-playbook -i inventory/prod.yml site.yml
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        ok: [ubuntu]
        ok: [centos7]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "CentOS"
        }
        ok: [ubuntu] => {
            "msg": "Ubuntu"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "el default fact"
        }
        ok: [ubuntu] => {
            "msg": "deb default fact"
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   



7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-vault encrypt group_vars/deb/examp.yml 
        New Vault password: 
        Confirm New Vault password: 
        Encryption successful
        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-vault encrypt group_vars/el/examp.yml
        New Vault password: 
        Confirm New Vault password: 
        Encryption successful

        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-playbook -i inventory/prod.yml site.yml                 
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        ERROR! Attempting to decrypt but no vault secrets found
        
        
        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
        Vault password: 
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        ok: [ubuntu]
        ok: [centos7]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "CentOS"
        }
        ok: [ubuntu] => {
            "msg": "Ubuntu"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "el default fact"
        }
        ok: [ubuntu] => {
            "msg": "deb default fact"
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        


9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.
10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь, что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

        (mnt-homeworks) ➜  playbook git:(MNT-video) ✗ ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
        Vault password: 
        
        PLAY [Print os facts] ***************************************************************************************************************************************************************************************
        
        TASK [Gathering Facts] **************************************************************************************************************************************************************************************
        [WARNING]: Platform darwin on host localhost is using the discovered Python interpreter at /Library/Frameworks/Python.framework/Versions/3.11/bin/python3.11, but future installation of another Python
        interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.14/reference_appendices/interpreter_discovery.html for more information.
        ok: [localhost]
        ok: [ubuntu]
        ok: [centos7]
        
        TASK [Print OS] *********************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "CentOS"
        }
        ok: [ubuntu] => {
            "msg": "Ubuntu"
        }
        ok: [localhost] => {
            "msg": "MacOSX"
        }
        
        TASK [Print fact] *******************************************************************************************************************************************************************************************
        ok: [centos7] => {
            "msg": "el default fact"
        }
        ok: [ubuntu] => {
            "msg": "deb default fact"
        }
        ok: [localhost] => {
            "msg": "local default fact"
        }
        
        PLAY RECAP **************************************************************************************************************************************************************************************************
        centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
        

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.
13. Предоставьте скриншоты результатов запуска команд.

## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.
2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.
3. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.
4. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот вариант](https://hub.docker.com/r/pycontribs/fedora).
5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
6. Все изменения должны быть зафиксированы и отправлены в ваш личный репозиторий.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
