# TUTORIAL DE CONFIGURAÇÃO DE FIREWALL - UBUNTU (UFW - Uncomplicated Firewall)

## Passo a passo

### 1º Passo: **Verificação do status do UFW**

Para a verificação de status do ufw no Ubuntu, utilize do comando abaixo:

```bash
sudo ufw status verbose
```

- **Obs.:** Caso o status seja 'inativo', não há problema. Nos próximos passos, iremos ativá-lo.


### 2º Passo: **Definição de Políticas Padrão**

Bem, o **núcleo de um firewall** caracteriza-se pelo **bloqueio de todo o tráfego de entrada por padrão e a permissão de todo o tráfego de saída** (permitindo navegação externa, porém protegendo a máquina de possíveis invasões). Para tal, execute os comandos abaixo:

- Primeiramente, o bloqueio padrão de toda a circulação de entrada:

```bash
sudo ufw default deny incoming
```

- Após, a permissão de toda a circulação de saída:

```bash
sudo ufw default allow outgoing
```

### 3º Passo: Permissão de conexões essenciais (SSH, etc.)


- Permissão do SSH:

```bash
sudo ufw allow ssh
```

- Caso queiras uma porta __diferente__ da padrão SSH, podes definí-la de seguinte forma:

```bash
sudo ufw allow porta_desejada/tcp
```

### 4º Passo: Ativação do Firewall

- Ativando o firewall:

```bash
sudo ufw enable
```

- Para conferir se tudo está correto, execute:

```bash
sudo ufw numbered
```

## Dicas Extras de Segurança

Caso precises ou queiras executar algum serviço local, por exemplo, um servidor web local (como Apache ou Nginx) ou ferramentas de desenvolvimento, podes liberar portas específicas facilmente. 

- Exemplo para liberar o servidor web padrão:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

- Para desativar ou resetar:

```bash
sudo ufw reset
```