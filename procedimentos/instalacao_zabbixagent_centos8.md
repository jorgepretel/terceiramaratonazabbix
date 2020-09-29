# Instalação Zabbix Agent - CentOS 8

## Instalar Zabbix Agent

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
dnf -y install zabbix-agent
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