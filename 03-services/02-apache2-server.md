#Autor: Robson Vaamonde<br>
#Procedimentos em TI: http://procedimentosemti.com.br<br>
#Bora para Prática: http://boraparapratica.com.br<br>
#Robson Vaamonde: http://vaamonde.com.br<br>
#Facebook Procedimentos em TI: https://www.facebook.com/ProcedimentosEmTi<br>
#Facebook Bora para Prática: https://www.facebook.com/BoraParaPratica<br>
#Instagram Procedimentos em TI: https://www.instagram.com/procedimentoem<br>
#YouTUBE Bora Para Prática: https://www.youtube.com/boraparapratica<br>
#Data de criação: 20/02/2024<br>
#Data de atualização: 20/02/2024<br>
#Versão: 0.01<br>

Site Oficial do Apache2: https://httpd.apache.org/<br>
Site Oficial do PHP (7.x ou 8.x): https://www.php.net/

O Servidor HTTP Apache ou Servidor Apache ou HTTP Daemon Apache ou somente Apache, é o servidor<br>
web livre criado em 1995 por um grupo de desenvolvedores da NCSA, tendo como base o servidor<br>
web NCSA HTTPd criado por Rob McCool.

PHP é uma linguagem interpretada livre, usada originalmente apenas para o desenvolvimento de<br>
aplicações presentes e atuantes no lado do servidor, capazes de gerar conteúdo dinâmico na<br> 
World Wide Web.

#01_ Instalando o Apache2 Server e PHP 8.x<br>

	#atualizando as listas do Apt
	sudo apt update
	
	#instalando as dependências do Apache2 Server
	sudo apt install git vim perl python2 python3 unzip ghostscript zlib1g zlib1g-dev apt-transport-https

	#instalando o Apache2 Server e PHP 8.x
	#opção da contra barra (\): criar uma quebra de linha no terminal
	sudo apt install apache2 apache2-utils apache2-bin apache2-data php8.1 php8.1-cli php8.1-common \
	php8.1-mysql php8.1-opcache php8.1-readline php8.1-common php8.1-bcmath php8.1-curl php8.1-intl \
	php8.1-mbstring php8.1-xml php8.1-zip php8.1-soap php-imagick php-json libapache2-mod-php libapr1 \
	libapache2-mod-php8.1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap

#02_ Verificando o Serviço e Versão do Apache2 Server e do PHP<br>

	#verificando o serviço do Apache2 Server
	sudo systemctl status apache2
	sudo systemctl restart apache2
	sudo systemctl reload apache2
	sudo systemctl stop apache2
	sudo systemctl start apache2

	#verificando as versões do Apache2 Server e do PHP
	#opção do comando apache2ctl: -V (version)
	#opção do comando php: -v (version)
	sudo apache2ctl -V
	sudo php -v

#03_ Verificando a Porta de Conexão do Apache2 Server<br>

	#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
	sudo lsof -nP -iTCP:'80' -sTCP:LISTEN

#04_ Localização dos Arquivos de Configuração do Apache2 Server e do PHP 8.x<br>

	/etc/apache2/                  <-- Diretório de configuração do Apache 2 Server
	/etc/apache2/apache2.conf      <-- Arquivo de configuração do Apache 2 Server
	/etc/apache2/sites-available/  <-- Diretório padrão dos Sites Acessíveis do Apache 2 Server
	/etc/apache2/conf-available/   <-- Diretório padrão das Configurações Acessíveis do Apache 2 Server
	/etc/php/                      <-- Diretório de configuração do PHP 7.x ou 8.x
	/etc/php/8.1/apache2/php.ini   <-- Arquivo de configuração do PHP 8.x do Apache 2 Server
	/var/www/html/                 <-- Diretório padrão das Hospedagem de Site do Apache 2 Server
	/var/log/apache2/              <-- Diretório padrão dos Logs do Apache 2 Server

#05_ Adicionado o Usuário Local no Grupo Padrão do Apache2 Server<br>

	#adicionando o seu usuário no grupo do Apache2
	#opções do comando usermod: -a (append), -G (groups), $USER (environment variable)
	sudo usermod -a -G www-data $USER
	
	#fazendo login em um novo grupo do Apache2
	newgrp www-data
	
	#verificando os identificadores de usuário e grupos
	id
	
	#recomendo fazer o logout do usuário para testar as permissões de grupos
	#OBSERVAÇÃO: você pode utilizar o comando: exit ou tecla de atalho: Ctrl +D
	exit

	#OBSERVAÇÃO IMPORTANTE: caso a conexão SSH trave, utilize os caracteres de escape para 
	#finalizar a conexões SSH.
	#caracteres: ~ (til) e . (ponto)
	~.

#06_ Criando a página de Teste do PHP para testar o Apache2 Server<br>

	#criando o arquivo de informações do PHP
	sudo vim /var/www/html/phpinfo.php
	INSERT

```php
<?php
	/** Módulo do PHP para gerar a página de documentação e parâmetros do PHP*/
	phpinfo(); 
?>
```
	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

#07_ Testando o Apache2 Server e o PHP no navegador<br>

	#utilizar os navegadores para testar suas páginas
	firefox ou google chrome: http://endereço_ipv4_ubuntuserver
	firefox ou google chrome: http://endereço_ipv4_ubuntuserver/phpinfo.php