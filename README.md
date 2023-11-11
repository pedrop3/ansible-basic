Playbook to create roles.

1. Na sua máquina local, abra um terminal e execute o seguinte comando para gerar uma chave SSH:

ssh-keygen

2. Quando solicitado, digite um nome para a chave SSH. O nome padrão é id_rsa.

3. Pressione Enter para aceitar o caminho padrão para a chave SSH.

4. Digite uma senha para a chave SSH. Essa senha é opcional, mas é recomendável usar uma senha para proteger sua chave.

5. Pressione Enter para gerar a chave SSH.

6. A chave SSH será gerada em dois arquivos: id_rsa e id_rsa.pub. O arquivo id_rsa é a chave privada, que você deve manter segura. O arquivo id_rsa.pub é a chave pública, que você precisa adicionar à máquina EC2.

7. Para adicionar a chave pública à máquina EC2, siga estas etapas:

8. Conecte-se à máquina EC2 usando o SSH.
Execute o seguinte comando para adicionar a chave pública:

```
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@<endereço-ip-da-máquina-ec2>
```
9. Substitua `<endereço-ip-da-máquina-ec2>` pelo endereço IP da máquina EC2.
Você deve agora ser capaz de conectar-se à máquina EC2 usando o Ansible. Para fazer isso, adicione a máquina EC2 ao seu inventário Ansible.



------------
Commands

To install ansible
```
apt-get install ansible
```
To check
```
ansible debian -m ping
``````

# inventory.yml
You'll define the hosts that can be used.

```
[debian]
192.168.78.134 hostname=srv-debian-01
[ubuntu]
192.168.0.7 hostname=srv-ubuntu-01
```

# 1. Roles
* Create new Role
```
ansible-galaxy init serverTools
```

# 2. Role in playbook
1. Add Role in playbook
    Exemplo:

```
roles:
    - serverTools
    - mysql
```
# 3. Tasks

In the main.yml file in the tasks folder 
    Add the tasks
    Exemplo:
    
```
- name: "Role: serverTools - Ubuntu"
    apt:
        update_cache: yes
        cache_valid_time: 3600
        name:
        - vim
        - iftop
        - zip
        - wget
        - curl
        - python3
        - yum-utils
        state: latest
    when: (ansible_facts['os_family'] == "RedHat")
    notify:
        - enabled yum-utils
    
- name: "Trafego http firewalld"
    firewalld:
        service: http
        permanent: yes
        state: enabled
    when: (ansible_facts['os_family'] == "RedHat")
```

# 4. Handlers

When task execute with sucess this action will be executed.

```
notify:
    - enabled yum-utils
```

```
- name: "enabled yum-utils"
  shell:
    cmd: "yum-config-manager --enable remi-php74"
```

# 5. vars 
In vars you could define var to your roles.
Exemplo:

```
---
# vars file for mysql
mysql_root_password: Password123
db_wp_name: pedro_db-1
dp_wp_username: user_db
db_wp_password: 123456
```
A principal diferença entre `defaults` e `vars`` é a prioridade. As variáveis definidas em defaults têm a menor prioridade, enquanto as variáveis definidas em vars têm a maior prioridade.

Isso significa que as variáveis definidas em vars irão sobrescrever as variáveis definidas em defaults.

Outra diferença importante é que as variáveis definidas em defaults são definidas no nível da role, enquanto as variáveis definidas em vars podem ser definidas no nível da role, do playbook ou do inventory.

Isso significa que as variáveis definidas em vars podem ser usadas para controlar o comportamento da role em diferentes hosts ou ambientes.