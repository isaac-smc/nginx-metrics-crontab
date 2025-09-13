
[README.md](https://github.com/user-attachments/files/22312186/README.md)
# 📌 Automação de Métricas do Nginx com Crontab

## 🔹 Resumo
Este projeto demonstra como automatizar a **coleta periódica de métricas do Nginx** utilizando **scripts em Bash** e agendamento com **crontab**.  
O objetivo é facilitar o monitoramento de conexões e requisições, reforçando práticas de **DevOps e CloudOps** em ambientes Linux.

---

## 🔹 Requisitos
- VM ou instância **Linux** com acesso `sudo`.
- Utilitários: `curl`, `awk` (normalmente já disponíveis nas distros).

---

## 🔹 Documento de configuração do script (Ubuntu/Debian)

### Atualizar pacotes
```bash
sudo apt update
```

### Instalar Nginx
```bash
sudo apt install -y nginx
sudo systemctl enable --now nginx
systemctl status nginx --no-pager
curl -I http://localhost
```

### Instalar o Crontab (ferramenta de agendamento)
```bash
sudo apt install -y cron
sudo systemctl enable --now cron
systemctl status cron --no-pager
crontab -l || echo "Sem crontab definida. Use: crontab -e"
```

### Criar pasta para armazenar logs
```bash
mkdir -p /home/usuario/logs
```

### Criar script com `nano`
```bash
nano /home/usuario/scripts/check_nginx.sh
```

Cole o seguinte código no arquivo:
```bash
#!/bin/bash

# Garante que a pasta de logs existe
mkdir -p /home/usuario/logs

if pgrep nginx &> /dev/null
then
    echo "nginx está operando $(date +'%Y-%m-%d %H:%M:%S')" >> /home/usuario/logs/nginx_status.log
else
    echo "nginx fora de operação $(date +'%Y-%m-%d %H:%M:%S')" >> /home/usuario/logs/nginx_status.log
fi
```

### Salvar e sair do editor
- Pressione `CTRL + O` para salvar.  
- Pressione `ENTER` para confirmar.  
- Pressione `CTRL + X` para sair.  

### Dar permissão de execução ao script
```bash
chmod +x /home/usuario/scripts/check_nginx.sh
```

---

## 🔹 Configurar com Crontab

Abra o editor do `crontab` para o seu usuário:
```bash
crontab -e
```

Adicione a linha abaixo para executar o script a cada 5 minutos e salvar a saída no log:
```bash
*/5 * * * * /home/usuario/scripts/check_nginx.sh >> /home/usuario/logs/nginx_cron.log 2>&1
```

### Explicação
- `*/5 * * * *` → executa a cada 5 minutos.  
- `/home/usuario/scripts/check_nginx.sh` → caminho do script criado.  
- `>> /home/usuario/logs/nginx_cron.log` → salva a saída em um arquivo de log.  
- `2>&1` → redireciona mensagens de erro para o mesmo log.

### Validar funcionamento
```bash
crontab -l
systemctl status cron --no-pager
cat /home/usuario/logs/nginx_cron.log
```
