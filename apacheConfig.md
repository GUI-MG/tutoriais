# Tutorial de configuração de Server Apache no Ubuntu

## Passo a passo da configuração

### Passo 1: Instalação do Apache

Caso não tenhas o Apache instalado em sua máquina, utilize dos comandos abaixo:

```bash
sudo apt update
sudo apt install apache2
```

- Após a instalação, o mesmo funcionará automaticamente, caso queiras conferir seu status, utilize:

    - ```bash
        sudo systemctl status apache2
        ```

### Passo 2: instalação do PHP e módulos essenciais (para utilizar em servidores de aplicações PHP)

O Apache não executa de forma autônoma códigos PHP, portanto deve-se instalar os módulos necessários para tal:

```bash
sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-gd php-mbstring php-xml php-zip
```

- Para verificar se o PHP fora corretamente instalado:
    - ```bash
        php -v
        ```

### Passo 3: permitir o tráfego HTTP/HTTPS no firewall (UFW)

```bash
sudo ufw allow 'Apache Full'
```

- Para verificar se a regra fora aplicada:
    - ```bash
        sudo ufw app list
        ```

### Passo 4: configurar a aplicação

Primeiramente, precisa-se colocar os arquivos de sua aplicação dnetro da pasta web do Apache:

```bash
cd /var/www/html/
```

Remova a pasta padrão de boas-vindas:

```bash
sudo rm index.html
```

### Passo 5: ajustar as permissões de propriedade

Para evitar problemas de permissão, é importante definir o usuário do servidor web (`www-data`) como dono da pasta:

```bash
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
```

### Passo 6: reiniciar o Apache

Sempre que fizeres alguma alteração estrutural ou que modifique severamente o Apache, tens de reiniciá-lo:

```bash
sudo systemctl restart apache2
```

Para verificar a sua integridade e correta execução:

```bash
sudo systemctl status apache2
```

