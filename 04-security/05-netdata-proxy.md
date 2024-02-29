#Autor: Robson Vaamonde<br>
#Procedimentos em TI: http://procedimentosemti.com.br<br>
#Bora para Prática: http://boraparapratica.com.br<br>
#Robson Vaamonde: http://vaamonde.com.br<br>
#Facebook Procedimentos em TI: https://www.facebook.com/ProcedimentosEmTi<br>
#Facebook Bora para Prática: https://www.facebook.com/BoraParaPratica<br>
#Instagram Procedimentos em TI: https://www.instagram.com/procedimentoem<br>
#YouTUBE Bora Para Prática: https://www.youtube.com/boraparapratica<br>
#Data de criação: 20/02/2024<br>
#Data de atualização: 27/02/2024<br>
#Versão: 0.01<br>

Site Oficial do Netdata: https://www.netdata.cloud/<br>

Netdata é uma ferramenta de código aberto projetada para coletar métricas em tempo real,<br>
como uso de CPU, atividade de disco, uso de largura de banda, visitas a sites etc..., e<br>
exibi-las em gráficos ao vivo e fáceis de interpretar.

#01_ Fazendo o download do Arquivo de Configuração do HTTPS do Netdata Server<br>

	#download do arquivo de configuração do HTTPS do Netdata Server
	#opção do comando wget: -v (verbose), -O (output file)
	sudo wget -v -O /etc/apache2/sites-available/netdata-ssl.conf https://github.com/vaamonde/ocsinventory-ng/blob/main/conf/netdata-ssl.conf

#02_ Editando o arquivo de Configuração do HTTPS do Netdata Server<br>

	#editando o arquivo de configuração do HTTPS do Netdata Server
	sudo vim /etc/apache2/sites-available/netdata-ssl.conf
	INSERT

		#alterar as informações principais do endereços IP e nomes na linha: 20
		# Configuração do Nome de Domínio e dos Apelidos (CNAMES) do Netdata Server
		# OBSERVAÇÃO IMPORTANTE: na geração do Certificado Assinado é recomendado que os nomes
		# configurados no Netdata Server seja o mesmo configurado na geração do certificado

		#alteraras informações de portas de URL Proxy na linha: 27
		# Configuração do Proxy Reverso do Netdata utilizando o Apache2
		# Habilitar os módulos do Apache2: a2enmod proxy proxy_http proxy_balancer lbmethod_byrequests
		# Alterar o arquivo de configuração: /etc/netdata/netdata.conf nas linhas referente a permissão de acesso:
		# allow connections from = localhost netdata.pti.intra 172.16.1.30
		# allow dashboard from = localhost netdata.pti.intra 172.16.1.30

		#alterar as informações principais de email na linha: 36
		# Email do administrador do Apache2 Server

		#alterar as informações principais dos Certificados na linha: 43
		# Configuração do suporte ao TLS/SSL utilizando o certificado criado pelo OpenSSL

		#alterar as informações de autenticação do Proxy na linha: 49
		# Configuração da Autenticação do Netdata utilizando o arquivo .htpasswd

	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

#03_ Habilitando o suporte ao TLS/SSL no Site HTTPS do Apache2 Server no Ubuntu Server<br>

	#habilitando os módulos do Proxy no Apache2 Server
	sudo a2enmod proxy proxy_http proxy_balancer lbmethod_byrequests
	
	#habilitando o Site HTTPS Padrão do Apache2 Server
	sudo a2ensite netdata-ssl

	#verificando as informações do arquivo do HTTPS do Apache2 Server
	sudo apache2ctl configtest

	#reiniciando o Serviços do Apache2 Server
	sudo systemctl restart apache2
	sudo systemctl status apache2

#04_ Verificando a Porta de Conexão do Netdata Server no Ubuntu Server<br>

	#verificando as portas 10443 HTTPS do Apache2 Server e 19999 Netdata Server
	#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
	sudo lsof -nP -iTCP:'10443,19999' -sTCP:LISTEN

#05_ Testando o Certificado TLS/SSL do Apache2 Server no ubuntu Server<br>

	#testando o certificado do Apache2 Server no Ubuntu Server
	#opção do comando echo: | (piper, faz a função de Enter no comando)
	#opções do comando openssl: s_client (command implements a generic SSL/TLS client which 
	#connects to a remote host using SSL/TLS), -connect (The host and port to connect to),
	#-servername (Include the TLS Server Name Indication (SNI) extension in the ClientHello 
	#message), -showcerts (Display the whole server certificate chain: normally only the server 
	#certificate itself is displayed)
	echo | openssl s_client -connect localhost:10443 -servername 172.16.1.30 -showcerts

#06_ Editando o arquivo de Configuração do Netdata Server<br>

	#editando o arquivo de configuração netdata.conf
	sudo vim /etc/netdata/netdata.conf
	INSERT

		#descomentar e acrescentar os nomes e endereços IPv4 da rede na linha:
		# allow connections from = localhost netdata.pti.intra 172.16.1.30
		# allow dashboard from = localhost netdata.pti.intra 172.16.1.30

	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

	#reiniciando o Serviços do Netdata Server
	sudo systemctl restart netdata

#07_ Criando o usuário e senha de acesso ao Proxy do Netdata Server<br>

	#criando o usuário de autenticação do Netdata Server
	#opção do comando htpasswd: -c (Create the passwdfile)
	sudo htpasswd -c /etc/netdata/.htpasswd NOME_DO_USUÁRIO

#08_ Testando o acesso via Proxy Autenticado do Netdata Server<br>

#09_ Integrando o Netdata Agent Local no Netdata Cloud<br>