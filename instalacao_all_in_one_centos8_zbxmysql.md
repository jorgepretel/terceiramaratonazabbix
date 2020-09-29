# Instalação Zabbix 5.0 - All In One

Nesta aula iremos instalar o Zabbix 5.0 - All In One

- [Instalação Zabbix 5.0 - All In One](#instalação-zabbix-50---all-in-one)
  - [Ambiente utilizado](#ambiente-utilizado)
  - [Ajustes após instalação do S.O](#ajustes-após-instalação-do-so)
    - [Verificar o timezone atual](#verificar-o-timezone-atual)
    - [Definir timezone](#definir-timezone)
    - [Valide a alteração do timezone](#valide-a-alteração-do-timezone)
    - [Verifique a data e hora atual](#verifique-a-data-e-hora-atual)
    - [Configure o chrony (NTP) para corrigir data e hora](#configure-o-chrony-ntp-para-corrigir-data-e-hora)
      - [Instalando o chrony](#instalando-o-chrony)
      - [Habilitar o chrony](#habilitar-o-chrony)
      - [Validar os ntp servers disponível](#validar-os-ntp-servers-disponível)
      - [Sincronizar data e hora](#sincronizar-data-e-hora)
      - [Configurar regra de firewall](#configurar-regra-de-firewall)
    - [Instalar utilitários](#instalar-utilitários)
    - [Desabilitar SELINUX](#desabilitar-selinux)
      - [Editar o arquivo de configuração e desabilitar o SELINUX](#editar-o-arquivo-de-configuração-e-desabilitar-o-selinux)
  - [Instalando Mysql 8](#instalando-mysql-8)
    - [Verificar versão disponível no repositório padrão](#verificar-versão-disponível-no-repositório-padrão)
    - [Instalar o pacote do mysql server](#instalar-o-pacote-do-mysql-server)
    - [Habilitar e iniciar serviço do MySQL](#habilitar-e-iniciar-serviço-do-mysql)
    - [Definir senha do usuário root do MySQL](#definir-senha-do-usuário-root-do-mysql)
    - [Conectar no banco, criar o banco de dados e o usuário do Zabbix](#conectar-no-banco-criar-o-banco-de-dados-e-o-usuário-do-zabbix)
  - [Instalar Zabbix Server](#instalar-zabbix-server)
    - [Instalar repositório oficial](#instalar-repositório-oficial)
    - [Limpar cache e remover repositórios antigos](#limpar-cache-e-remover-repositórios-antigos)
    - [Instalando Zabbix Server](#instalando-zabbix-server)
    - [Carregar esquema inicial do banco](#carregar-esquema-inicial-do-banco)
    - [Verificar se o banco de dados foi populado](#verificar-se-o-banco-de-dados-foi-populado)
    - [Editar arquivo de configuração do Zabbix Server](#editar-arquivo-de-configuração-do-zabbix-server)
    - [Habilitar inicialização do serviço e inicia-lo](#habilitar-inicialização-do-serviço-e-inicia-lo)
    - [Verifique os logs do Zabbix Server e veja se tem erros](#verifique-os-logs-do-zabbix-server-e-veja-se-tem-erros)
  - [Instalar frontend](#instalar-frontend)
    - [Instalando os pacotes](#instalando-os-pacotes)
    - [Configurando o PHP](#configurando-o-php)
    - [Habilitar inicialização do serviço e inicia-lo](#habilitar-inicialização-do-serviço-e-inicia-lo-1)
    - [Criar regra no firewall](#criar-regra-no-firewall)
    - [Acesse a interface web](#acesse-a-interface-web)

## Ambiente utilizado

Servidor Zabbix
Função: MySQL / Zabbix Server / Frontend

- Sistema Operacional: CentOS 8 (minimal)
- Memória: 4GB de RAM
- CPU: 2 vCPU
- Disco: 50GB

## Ajustes após instalação do S.O

### Verificar o timezone atual

```bash
timedatectl status
```

### Definir timezone

```bash
timedatectl set-timezone America/Sao_Paulo
```

`NOTA: Para listar todos os timezone disponíveis utiliza o comando abaixo`

```bash
timedatectl list-timezones | grep Sao_Paulo
```

### Valide a alteração do timezone

```bash
timedatectl status
```

### Verifique a data e hora atual

```bash
date
```

### Configure o chrony (NTP) para corrigir data e hora

#### Instalando o chrony

```bash
dnf -y install chrony
```

#### Habilitar o chrony

```bash
systemctl enable --now chronyd
```

#### Validar os ntp servers disponível

```bash
chronyc sources
```

`NOTA: Por padrão o chrony vai atualizar o horário a cada 64 segundos`

#### Sincronizar data e hora

```bash
service chronyd restart
```

#### Configurar regra de firewall

```bash
firewall-cmd --permanent --add-service=ntp
firewall-cmd --reload
```

### Instalar utilitários

```bash
dnf install -y net-tools vim nano epel-release wget curl tcpdump
```

### Desabilitar SELINUX

- Verificar status atual do SELINUX

```bash
getenforce
```

#### Editar o arquivo de configuração e desabilitar o SELINUX

`É NECESSÁRIO o REBOOT`

```bash
cat /etc/selinux/config
vim /etc/selinux/config
SELINUX=disabled
```

`CASO NÃO QUEIRA DAR REBOOT`

```bash
setenforce 0
getenforce
```

## Instalando Mysql 8

### Verificar versão disponível no repositório padrão

```bash
dnf info mysql-server
```

Output

```bash
...
Available Packages
Name         : mysql-server
Version      : 8.0.17
Release      : 3.module_el8.0.0+181+899d6349
Architecture : x86_64
Size         : 22 M
Source       : mysql-8.0.17-3.module_el8.0.0+181+899d6349.src.rpm
Repository   : AppStream
Summary      : The MySQL server and related files
URL          : http://www.mysql.com
License      : GPLv2 with exceptions and LGPLv2 and BSD
Description  : MySQL is a multi-user, multi-threaded SQL database server. MySQL is a
             : client/server implementation consisting of a server daemon (mysqld)
             : and many different client programs and libraries. This package contains
             : the MySQL server and some accompanying files and directories.
```

### Instalar o pacote do mysql server

```bash
dnf -y install mysql-server
```

### Habilitar e iniciar serviço do MySQL

```bash
systemctl enable --now mysqld
systemctl status mysqld
```

### Definir senha do usuário root do MySQL

```bash
mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
Please set the password for root here.

New password: Z4bb1x5!2o2o

Re-enter new password: Z4bb1x5!2o2o

Estimated strength of the password: 100
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

### Conectar no banco, criar o banco de dados e o usuário do Zabbix


```bash
mysql -u root -p
create database zabbix character set utf8 collate utf8_bin;
create user 'zabbix'@'localhost' identified by 'Zabbix!Maratona3';
grant all privileges on zabbix.* to 'zabbix'@'localhost';
flush privileges;
exit
```

`character set utf8 - suporte a multilinguagem`
`collate utf8_bin - armazena os dados em case sensitive`

## Instalar Zabbix Server

### Instalar repositório oficial

```bash
rpm -ivh http://repo.zabbix.com/zabbix/5.0/rhel/8/x86_64/zabbix-release-5.0-1.el8.noarch.rpm
```

### Limpar cache e remover repositórios antigos
  
```bash
dnf clean all
```

Output:

```bash
20 files removed
```

### Instalando Zabbix Server

```bash
dnf -y install zabbix-server
```

### Carregar esquema inicial do banco

```bash
zcat /usr/share/doc/zabbix-server-mysql/create.sql.gz | mysql -uzabbix -p zabbix
```

### Verificar se o banco de dados foi populado

```bash
mysql -u zabbix -p zabbix
use zabbix;
show tables;
quit;
```

Output:

```bash
...
166 rows in set (0.01 sec)
```

### Editar arquivo de configuração do Zabbix Server

```bash
vim /etc/zabbix/zabbix_server.conf
```

```bash
### Option: DBPassword
#       Database password.
#       Comment this line if no password is used.
#
# Mandatory: no
# Default:
DBPassword=Curso!Zabbix5
```

### Habilitar inicialização do serviço e inicia-lo

```bash
systemctl enable --now zabbix-server
systemctl status zabbix-server
```

### Verifique os logs do Zabbix Server e veja se tem erros

```bash
tail -n50 /var/log/zabbix/zabbix_server.log
```

## Instalar frontend

### Instalando os pacotes

```bash
dnf -y install zabbix-web-mysql zabbix-apache-conf
```

### Configurando o PHP

```bash
vim /etc/php-fpm.d/zabbix.conf
```

```bash
php_value[date.timezone] = America/Sao_Paulo
```

### Habilitar inicialização do serviço e inicia-lo

```bash
systemctl enable --now httpd php-fpm
systemctl status httpd php-fpm
```

### Criar regra no firewall 

```shell
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

### Acesse a interface web

- http://IP_OU_DNS/zabbix
- Usuário: Admin
- Senha: zabbix