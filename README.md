# Configuração do Servidor Web com Nginx (Ubuntu Server no Proxmox)

## 1. Criação da VM no Proxmox
- Criada VM com **Ubuntu Server 22.04 LTS**  
- Recursos: **1 vCPU**, **1,5 GB RAM**, **15 GB disco**  
- ISO montada e instalação mínima.

---

## 2. Atualização do Sistema

```
sudo apt update -y && sudo apt upgrade -y 
```
## 3. Instalação do Nginx 

Instalar o servidor web Nginx 

```
apt install nginx -y
```
verificar se está ativo 

```
systemctl status nginx
```
## 4. Estrutura do site 

Criar diretório do site 

```
mkdir -p /var/www/site1/html/
```

Criar página inicial

```
vim /var/www/site1/html/index.html
```
index.html
```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Site de Teste - Nginx</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; margin-top: 100px; background: #f4f4f4; }
    h1 { color: #2c3e50; }
    .status { color: green; font-weight: bold; }
  </style>
</head>
<body>
  <h1>Bem-vindo ao Site de Teste!</h1>
  <p class="status">Nginx rodando com sucesso</p>
  <p>Servidor: <strong>Nginx</strong> | IP: <strong id="ip"></strong></p>

  <script>
    fetch('https://api.ipify.org?format=json')
      .then(r => r.json())
      .then(data => document.getElementById('ip').textContent = data.ip)
      .catch(() => document.getElementById('ip').textContent = 'indisponível');
  </script>
</body>
</html>
``` 
## 5. Configuração do Virtual Host

Criar arquivo de configuração

```
vim /etc/nginx/sites-available/site1
```
Configuração: 
```
server {
    listen 80;
    listen [::]:8081;

    server_name _;

    root /var/www/site1/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

```
## 6. Habilitar site

Criar link simbólico 

```
ln -s /etc/nginx/sites-available/site1 /etc/nginx/sites-enabled/
```
Remover configuração padrão 
```
rm /etc/nginx/sites-available/default
rm /etc/nginx/sites-enabled/default
```

## 7. Criar e Configurar o Site 2

Criar diretório do site
```
mkdir -p /var/www/site2/html/
```
Criar página principal
```
vim /var/www/site2/html/index.html
```
index.html
```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Site de Teste - Nginx</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; margin-top: 100px; background: #f4f4f4; }
    h1 { color: #2c3e50; }
    .status { color: green; font-weight: bold; }
  </style>
</head>
<body>
  <h1>Bem-vindo ao Site de Teste!</h1>
  <p class="status">Nginx rodando com sucesso</p>
  <p>Servidor: <strong>Nginx</strong> | IP: <strong id="ip"></strong></p>

  <script>
    fetch('https://api.ipify.org?format=json')
      .then(r => r.json())
      .then(data => document.getElementById('ip').textContent = data.ip)
      .catch(() => document.getElementById('ip').textContent = 'indisponível');
  </script>
</body>
</html>
``` 
Criar arquivo de configuração 
```
vim /etc/nginx/sites-available/site2
```
Configuração
```
server {
    listen 8082;
    listen [::]:8082;

    server_name _;

    root /var/www/site2/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Criar link simbólico
```
ln -s /etc/nginx/sites-available/site2 /etc/nginx/sites-enabled/
```

## 8. Teste e reiniciar o Nginx

Testar sintaxe de configuração

``` 
nginx -t
```

Reiniciar o serviço

```
systemctl restart nginx
``` 

## 9. Verificação final 

Acessar o servidor via navegador com o curl:

``` 
curl 192.168.0.4:8081
curl 192.168.0.4:8082
```

Resposta esperada: HTML da página.
