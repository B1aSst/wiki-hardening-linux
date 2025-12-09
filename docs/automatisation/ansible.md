# Ansible

Le durcissement peut vite devenir une tâche chronophage et répétitive si le nombre de systèmes devient trop important. Afin d'automatiser ces tâches, un outil comme Ansible peut être utilisé. 

Ansible utilise des "Playbooks" qui permettent d'automatiser de nombreuses types de tâches sur un système local ou distant. Ansible fonctionne en mode sans agent, ce qui veut dire qu'aucun logiciel n'a besoin d'être installé sur le système cible à part un interpréteur Python. Dans notre cas, une connexion SSH vers le système peut être utilisée pour exécuter les "Playbooks".

## Configuration

### Service OpenSSH

Afin de durcir la configuration du service OpenSSH, on va utiliser un "Playbook" afin de modifier le fichier "sshd_config". Le "Playbook" suivant permet d'implêmenter les recommendations présentés dans le guide [OpenSSH](../mesures/openssh.md)

```yaml
- hosts: deb-hardening
  become: yes

  vars_files:
    - sshd-variables.yaml

  tasks:
    - name: Convert boolean values to yes/no
      set_fact:
        sshd_params:
          Port: "{{ ssh_port }}"
          StrictModes: "{{ strict_modes | bool | ternary('yes','no') }}"
          PermitRootLogin: "{{ permit_root | bool | ternary('yes','no') }}"
          PermitEmptyPasswords: "{{ permit_empty_passwords | bool | ternary('yes','no') }}"
          PasswordAuthentication: "{{ password_auth | bool | ternary('yes','no') }}"
          PubkeyAuthentication: "{{ pubkey_auth | bool | ternary('yes','no') }}"
          AllowUsers: "{{ allow_users | join(' ') }}"
          MaxAuthTries: "{{ max_auth_tries }}"
          LoginGraceTime: "{{ login_grace_time }}"
          PrintLastLog: "{{ print_last_log | bool | ternary('yes','no') }}"
          PermitUserEnvironment: "{{ permit_user_env | bool | ternary('yes','no') }}"
          AllowTcpForwarding: "{{ allow_tcp_forwarding | bool | ternary('yes','no') }}"
          X11Forwarding: "{{ x11_forwarding | bool | ternary('yes','no') }}"

    - name: Update sshd_config parameters
      ansible.builtin.lineinfile:
        path: /etc/ssh/sshd_config
        regexp: "^(#\\s*)?{{ item.key }}\\b"
        line: "{{ item.key }} {{ item.value }}"
        backrefs: yes
      loop: "{{ sshd_params | dict2items }}"
      notify: reload sshd

  handlers:
    - name: reload sshd
      ansible.builtin.service:
        name: sshd
        state: reloaded
```

Le fichier `sshd-variables.yaml` permet d'indiquer les paramètres que l'on veut :

```yaml
ssh_port: 22
strict_modes: yes
permit_root: no
permit_empty_passwords: no
password_auth: no
pubkey_auth: yes
allow_users:
  - robert-admin
max_auth_tries: 2
login_grace_time: 30
print_last_log: yes
permit_user_env: no
allow_tcp_forwarding: no
x11_forwarding: no
```

On peut ensuite exécuter le playbook avec la commande suivante :
```sh
ansible-playbook -i inventory.ini configuration-sshd.yaml --ask-become-pass
```

Le paramètre `--ask-become-pass` permet de demander le mot de passe pour élever ses privilèges sur le système cible. Le "Playbook" redémarre également le service SSH si des modifications dans le fichier de conf sont effectuées.

## Sources

- [Ansible Documentation](https://docs.ansible.com/projects/ansible/latest/index.html)
