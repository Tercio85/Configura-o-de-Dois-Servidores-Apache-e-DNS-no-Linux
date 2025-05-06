# Configura o de Dois Servidores Apache e DNS no Linux
Este repositório descreve o processo de configuração de dois servidores Linux

## 1. Configurar Zona DNS
Primeiramente deverá editar o arquivo para configurar a Zona DNS
Edite o arquivo com o comando:
```Bash
sudo nano /etc/bund/named.conf.local
```
Adicione:
```Bash
zone "com" {
    type master;
    file "/etc/bind/db.com";
};
```
## 2. Criar o Arquivo de Zona /etc/bind/db.com
```Bash
sudo cp /etc/bind/db.local /etc/bind/db.com
sudo nano /etc/bind/db.com
```
Exemplo de conteúdo:
```$TTL    604800
@       IN      SOA     ufrn.com. root.ufrn.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ufrn.com.
ufrn    IN      A       192.168.0.11
ifrn    IN      A       192.168.0.10
```
## 3. Reiniciar e Testar o DNS
Reinicie com o comando:
```Bash
sudo systemctl restart bind9
sudo systemctl enable bind9
```
Após teste a resolução de nomes:
```Bash
dig @192.168.0.11 ifrn.com
```

# Configuração do Servidor Web (ifrn.com)
## 1. Instalar o Apache
Realize o seguinte comando para isntalar o Apache:
```Bash
sudo apt update
sudo apt install apache2 -y
```
## 2. Definir Hostname
Após defina o hostname com o comando:
```Bash
sudo hostnamectl set-hostname ifrn.com
```
## 3. Criar um Virtual Host (opcional)
Realize o comando:
```Bash
sudo nano /etc/apache2/sites-available/ifrn.com.conf
```
Aplique como conteúdo:
```Bash
<VirtualHost *:80>
    ServerName ifrn.com
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Agora ative o site e reinicie o Apache com os comandos:
```Bash
sudo a2ensite ifrn.com.conf
sudo systemctl reload apache2
```
## 4.  Configurar Cliente para Usar o Servidor DNS
Agora qualquer máquina cliente (ou no próprio servidor Apache), edite o ```resolv.conf```:
Realize o comando:
```Bash
sudo nano /etc/resolv.conf
```
Adicione:
```Bash
nameserver 192.168.0.11
```
# Testes finais:
Realize os seguintes testes para verificar se configuração está conforme:

DNS: ```ping ifrn.com``` deve retornar o IP ```192.168.0.10```.

Apache: ```curl http://ifrn.com``` deve retornar a página HTML padrão do Apache.

DNS com dig: ```dig @192.168.0.11 ifrn.com```
