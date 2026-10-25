# Tutorial de configuração do Pm2

## Explicação passo a passo

### **Passo 1:** Instalação

- Para a instalação do pm2, utilize do seguinte comando:

```bash
sudo npm install -g pm2
```

### **Passo 2:** Executando uma aplicação com o Pm2

Navegue até a pasta raiz de seu projeto (onde está o seu arquivo principal, como o `server.js`, `app.js` ou o build compilado `/dist/index.js`).

- Para iniciar o servidor e dar um nome ao processo, execute o comando abaixo: 

```bash
pm2 start `nome do arquivo` --name "nome-servidor"
```

- **Comandos essenciais para monitoramento:**
  - Ver lista de processos e status:

    ```bash
    pm2 list
    ```
  - Monitorar uso de CPU e Memória em tempo real:

    ```bash
    pm2 monit
    ```
  - Visualizar os logs da aplicação:

    ```bash
    pm2 logs meu-servidor
    ```

  - Reiniciar ou parar um servidor:

    ```bash
    pm2 restart meu-servidor
    pm2 stop meu-servidor
    ```

### **Passo 3:** Configuração Avançada (Arquivo `ecosystem.config.js`)

Em projetos profissionais, gerenciar parâmetros via linha de comando pode se tornar complexo. O recomendado é criar um arquivo de configuração central. 

- Por isso, crie na raiz de seu projeto, o arquivo:

```bash
pm2 init
```

- Abra o arquivo `ecosystem.config.js` gerado e configure-o da seguinte forma:

```js
module.exports = {
    apps: [
        {
            name: "meu-servidor",
            script: "./server.js",
            instances: "max",   //Utiliza todos os núcleos disponíveis da CPU (modo cluster)
            exec_mode: "cluster",   //Ativa o balanceamento de carga nativo
            env: {
                NODE_ENV: "development",
                PORT: 3000
            },
            env_production: {
                NODE_ENV: "production",
                PORT: 3000
            }
        }
    ]
};
```

- Após, para iniciar a aplicação utilizando este arquivo de configuração em modo de produção, use:

```bash
pm2 start ecosystem.config.js --env production
```

### **Passo 4:** Garantindo que o servidor reinicie com o sistema (startup)

Caso sua máquina ou VPS reinicie por problemas de energia ou manutenção, o pm2 precisa 'subir' o(s) servidor(es) sozinho.

1. **Gere o script de inicialização automática:**

```bash
pm2 startup
```

2. **Salve o estado atual dos processos:**

```bash
pm2 save
```

- *Pronto! Sempre que o servidor religar, o pm2 restaurará automaticamente os aplicativos salvos.*

### **Passo extra:** Usando o *Nginx* como *Proxy reverso*

Em produção, aplicações Node.js executando via pm2 em portas internas (como a `3000`) costumam ficar escondidas atrás de um servidor web como o ***Nginx***. O *Nginx* gerencia as portas externas `80` (HTTP) e `433` (HTTPS/SSL), fazendo o redirecionamento de tráfego de forma segura para o pm2.

- Para isso, um bloco básico de configuração do *Nginx* (`/etc/nginx/sites-available/default`) fica assim:

```nginx
server {
    listen 80;
    server_name seudominio.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}