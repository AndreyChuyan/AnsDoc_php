# apps_web - Развертывание Web приложения
# Автор - Чуян А.А. oksigen_07@bk.ru
ansible-galaxy collection install community.docker

- добавил phpMyAdmin для удобной работы с БД
- в качестве тестового приложения вывожу конфигурацию PHP, надеюсь на работу с реальным приложением :-)























```bash
# настрорйка плейбука
cat <<EOF > ~/ansible/playbooks/connect_mirror.yml
- name: Подключение зеркала репозитория
  hosts: v_ser
  roles:
    - connect_mirror
EOF

cat <<EOF > ~/ansible/inventory/group_vars/all.yml
---
zabbix_server: 192.168.10.22
EOF

#---------
# подготовка роли
role_name=connect_mirror ; \
mkdir -p ~/ansible/roles/$role_name/{mkdir,tasks,handlers,templates,files,vars,defaults,meta} ;\
touch ~/ansible/roles/$role_name/{tasks/main.yml,handlers/main.yml,vars/main.yml,defaults/main.yml,meta/main.yml} ; \
cd ~/ansible/roles/$role_name/

#шаблоны
cat <<EOF > ./templates/repo_mirror_template.j2 
deb http://{{ ip_mirror }}/mirror/{{ repo_url }} {{ distribution }} {{ components }}
EOF

#зависимости
cat <<EOF > ./handlers/main.yml
---
- name: Update apt cache
  apt:
    update_cache: yes
  become: yes
EOF

#переменные роли
cat <<EOF > ./vars/main.yml 
---
ip_mirror : 192.168.10.20
EOF


#задачи
cat <<EOF > ./tasks/main.yml 
---
- name: Add miror Vbox
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_vbox'
    repo_url: 'download.virtualbox.org/virtualbox/debian/'
    distribution: 'stretch'
    components: 'contrib'
  become: yes

- name: Add miror Zabbix
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_zabix'
    repo_url: 'repo.zabbix.com/zabbix/6.4/debian'
    distribution: 'bookworm'
    components: 'main'
  become: yes

- name: Add miror Debian
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_debian'
    repo_url: 'deb.debian.org/debian/'
    distribution: 'bookworm'
    components: 'main non-free-firmware'
  become: yes

- name: Add miror Debian_sec
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_debian_sec'
    repo_url: 'security.debian.org/debian-security'
    distribution: 'bookworm-security'
    components: 'main non-free-firmware'
  become: yes

- name: Add miror Astra_stable
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_astra'
    repo_url: 'dl.astralinux.ru/astra/stable/1.6_x86-64/repository'
    distribution: 'smolensk'
    components: 'main contrib non-free'
  become: yes

- name: Add miror Astra_update
  template:
    src: repo_mirror_template.j2 
    dest: /etc/apt/sources.list.d/{{ repo_name }}.list
  vars:
    repo_name: 'mirr_astra_upd'
    repo_url: 'dl.astralinux.ru/astra/stable/1.6_x86-64/repository-update/'
    distribution: 'smolensk'
    components: 'main contrib non-free'
  become: yes

- name: Copy Zabbix GPG Key
  copy:
    src: zabbix-official-repo.gpg
    dest: /etc/apt/trusted.gpg.d/

- name: Комментируем строки в sources.list
  ansible.builtin.lineinfile:
    path: /etc/apt/sources.list
    regexp: 'deb '
    line: '#'
    backup : true
  become: yes

- name: Update apt cache
  apt:
    update_cache: yes
  become: yes


EOF

cd ~/ansible
ansible-playbook ~/ansible/playbooks/connect_mirror.yml --ask-vault-pass 
```