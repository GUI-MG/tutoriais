# GUIA PARA CONFIGURAÇÃO BÁSICA DE UM SERVIDOR NGINX - **UBUNTU**

## Passo a passo para instalação e configuração de um servidor Nginx


### Passo 1: Instalação

Antes de começarmos, façamos **update** no sistema e a instalação do Nginx utilizando o gerenciador de pacotes **apt**:

- Refreshing o sistema:
  
```bash
sudo apt update
```

- Instalando o Nginx:

```bash
sudo apt install nginx
```

### Passo 2: Ajuste do Firewall (caso tenhas ativado)

Ao ativar-se o firewall (se não tens e queres configurá-lo, porém não sabes como fazê-lo, acesse o arquivo **ufwFirewall.md**), tem-se o bloqueio padrão do tráfego de entrada, por conseguinte, é necessária a liberação do Nginx no firewall.

- Liberação do Nginx no firewall (**ufw**):

```bash
sudo ufw allow 'Nginx Full'
```

- Para verificar se tivera a liberação, execute:

```bash
sudo ufw status
```

### Passo 3: Verificar se o Nginx está sendo executado

Posteriormente a instalação, o Nginx inicia-se sozinho. Com isso, podes verificar seu status com o comando abaixo.

- Verificando o status do Nginx:

```bash
sudo systemctl status nginx
```

### Passo 4: Estrutura básica de Configuração

Os arquivos relacionados ao Nginx ficam localizados no diretório ```/etc/nginx/```.

- **Arquivo principal:** ```/etc/nginx/nginx.conf``` (geralmente não precisa-se alterá-lo inicialmente);
- **Sites disponíveis:** ```/etc/nginx/sites-available``` (local de repouso dos arquivos de configuração de seus sites);
- **Sites Ativos:** ```/etc/nginx/sites-enabled``` (diretório onde se localizam os links simbólicos dos sites ativos).

### Passo 5: Configurando um bloco de servidor **(Server Block)**

- Acesse a pasta de sites disponíveis:

```bash
cd /etc/nginx/sites-available
```

- Após, crie um arquivo de configuração para o seu site/projeto;

```bash
sudo nano meusite
```

- Adicione uma configuração básica, como a exemplificada abaixo:

```txt
server {
    listen 80;
    server_name meusite.local;

    root /var/www/meusite;
    index index.html index.htm;

    location / {
        try_files $uri/ =404;
    }
}
```

- Salve o arquivo (`Ctrl + 0`, depois `Enter`) e saia (`Ctrl + X`).

### Passo 6: ativação do Site e Teste

Para a ativação do site criado, tens de criar um link simbólico para a pasta `sites-enabled`.

- Criação do link simbólico:

```bash
sudo ln -s /etc/nginx/sites-available/meusite /etc/nginx/sties-enabled/
```

- **Obs.:** Antes de reiniciar o Nginx, **sempre teste** se há **erros de sintaxe** na configuração:

```bash
sudo nginx -t
```

- Se a resposta for: `syntax is ok` e `test is successful`, reinicie o Nginx para a aplicação das alterações:

```bash
sudo systemctl restart nginx
```

### Passo 7: Adendo

Caso haja algum outro serviço rodando na mesma porta em que o **Nginx** deseja executar, podes verificar qual é o serviço ou aplicação que está sendo executada naquela porta com o comando abaixo e desativá-lo utilizando `systemctl` ou outro gerenciador caso seja necessário. 

Por exemplo, abaixo temos um caso do `Apache` estar sendo executado na **porta 80**, esta que o **Nginx** deseja utilizar para executar: 

- Por tratar-se do `Apache`, utiliza-se o `systemctl` para sua desativação:

```bash
sudo systemctl stop apache2
sudo systemctl disable apache2
```

- Caso seja algum processo fantasma ou instância travada, pode-se encerrá-lo utilizando o PID obtido no uso do comando disposto na seção `Curiosidade`:

```bash
sudo kill -9 pid_obtido
```

- Após, reinicie novamente o **Nginx**:

```bash
sudo systemctl restart nginx
```

- E para confirmar que tudo ocorrera bem, execute:

```bash
sudo systemctl status nginx
```

## **CURIOSIDADE**

### Para verificar um serviço ou aplicação que esteja sendo executada numa porta que desejas alocar algum outro serviço ou aplicação, utilize do comando abaixo.

- Verificação de serviço ou aplicação sendo executada na porta desejada:

```bash
sudo ss -lptn 'sport = :porta_desejada'
```

- Ou se preferires utilizar o comando tradicional `lsof`:

```bash
sudo lsof -i :porta_desejada
```

- Caso não possuas o `lsof` **(List Open Files)** instalado, tens de instalá-lo utilizando o `apt`:

```bash
sudo apt update
sudo apt install lsof
```

### Se quiseres algo mais ágil e que seja ideal para automações, utilize o `fuser`

- Primeiramente, verifique se o command existe:

```bash
command -v fuser
```

- Agora, verifique o PID do processo que está utilizando uma porta específica:

```bash
sudo fuser porta_desejada/formato_desejado
```

- Com o PID em mãos, podes utilizar do comando `kill` para encerrar o processo, liberando a porta especificada:

```bash
sudo kill -9 pid_retornado
```
