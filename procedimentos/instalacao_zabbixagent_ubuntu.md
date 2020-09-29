# Instalação Zabbix 5.0 - All In One

Nesta aula iremos instalar o Zabbix 5.0 - All In One

- [Instalação Zabbix 5.0 - All In One](#instalação-zabbix-50---all-in-one)
  - [Instalar Zabbix Server](#instalar-zabbix-server)
    - [Instalar repositório oficial](#instalar-repositório-oficial)
    - [Instalando Zabbix Agent](#instalando-zabbix-agent)
    - [Editar arquivo de configuração do Zabbix Server](#editar-arquivo-de-configuração-do-zabbix-server)
    - [Habilitar inicialização do serviço e inicia-lo](#habilitar-inicialização-do-serviço-e-inicia-lo)
    - [Verifique os logs do Zabbix Server e veja se tem erros](#verifique-os-logs-do-zabbix-server-e-veja-se-tem-erros)

## Instalar Zabbix Server

### Instalar repositório oficial

```bash
cd /tmp
wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
dpkg -i zabbix-release_5.0-1+focal_all.deb
apt update
```

### Instalando Zabbix Agent

```bash
apt install zabbix-agent zabbix-get zabbix-sender
```

### Editar arquivo de configuração do Zabbix Server

```bash
vim zabbix_agentd.conf
```

```bash
Server=127.0.0.1
ServerActive=127.0.0.1
# Hostname=Zabbix server # Deixar comentado
```

### Habilitar inicialização do serviço e inicia-lo

```bash
systemctl enable --now zabbix-agent
systemctl status zabbix-agent
```

### Verifique os logs do Zabbix Server e veja se tem erros

```bash
tail -n50 /var/log/zabbix/zabbix_agentd.log
```
