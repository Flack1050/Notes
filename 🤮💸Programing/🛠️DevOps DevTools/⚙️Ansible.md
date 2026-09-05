---
tags: [ansible, iac, devops]
---

# ⚙️ Ansible

Связано: [[🔄CI-CD Pipelines]] · [[🏗️Terraform]] · [[🐳Docker]]

Agentless-инструмент автоматизации конфигурации: коннектится по SSH, ничего не нужно ставить на управляемые машины (только Python).

## Ключевые понятия

- **Inventory** — список хостов (`ini` или `yaml`).
- **Playbook** — YAML-сценарий из одного или нескольких **plays**.
- **Play** — набор **tasks**, применяемых к группе хостов.
- **Module** — единица действия (`apt`, `copy`, `service`, `docker_container`...).
- **Role** — переиспользуемый набор tasks/vars/templates/handlers.
- **Handler** — task, срабатывающий по событию `notify` (например, рестарт сервиса).
- **Fact** — собранная инфа о хосте (`ansible_facts`).

## Установка (Arch)

```bash
sudo pacman -S ansible
```

## Inventory пример

`inventory/prod.ini`:

```ini
[web]
web1.example.com
web2.example.com

[db]
db1.example.com ansible_user=admin

[web:vars]
ansible_python_interpreter=/usr/bin/python3
```

## Playbook пример

`deploy.yml`:

```yaml
---
- name: Deploy web app
  hosts: web
  become: true
  vars:
    app_dir: /opt/myapp

  tasks:
    - name: Ensure app dir exists
      ansible.builtin.file:
        path: "{{ app_dir }}"
        state: directory
        mode: "0755"

    - name: Sync code
      ansible.builtin.copy:
        src: ./dist/
        dest: "{{ app_dir }}"

    - name: Install dependencies
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Deploy nginx config
      ansible.builtin.template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/myapp
      notify: Reload nginx

  handlers:
    - name: Reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded
```

## Основные команды

```bash
ansible all -i inventory/prod.ini -m ping          # проверить связь
ansible-playbook -i inventory/prod.ini deploy.yml
ansible-playbook deploy.yml --check                 # dry-run
ansible-playbook deploy.yml --diff                  # показать diff изменений
ansible-playbook deploy.yml --limit web1.example.com
ansible-playbook deploy.yml --tags "nginx"
ansible-vault encrypt secrets.yml                    # шифрование секретов
ansible-vault edit secrets.yml
```

## Структура роли

```
roles/
  webserver/
    tasks/main.yml
    handlers/main.yml
    templates/nginx.conf.j2
    files/
    vars/main.yml
    defaults/main.yml
    meta/main.yml
```

Использование роли в playbook:

```yaml
- hosts: web
  roles:
    - webserver
    - { role: firewall, tags: ["security"] }
```

## Идемпотентность
Главный принцип Ansible: повторный запуск playbook не должен ничего ломать и не должен менять состояние, если оно уже соответствует описанному. Используй модули (`ansible.builtin.*`), а не сырые `shell`/`command`, там где это возможно — модули сами следят за идемпотентностью.

## Ansible + Docker
Модуль `community.docker.docker_container` позволяет управлять контейнерами прямо из playbook — удобно для развёртывания на bare-metal без Kubernetes.

#ansible #iac #automation #devops
