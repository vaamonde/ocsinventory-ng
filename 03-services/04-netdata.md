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

Site Oficial do Netdata: https://www.netdata.cloud/<br>

Netdata é uma ferramenta de código aberto projetada para coletar métricas em tempo real,<br>
como uso de CPU, atividade de disco, uso de largura de banda, visitas a sites etc..., e<br>
exibi-las em gráficos ao vivo e fáceis de interpretar.

#01_ Instalando as Dependências do Netdata Server<br>

	#atualizando as lista do apt
	sudo apt update

	#instalando as dependências do Netdata Server
	sudo apt install git vim build-essential software-properties-common gnupg apt-transport-https \
	ca-certificates zlib1g-dev gcc make git autoconf autogen automake pkg-config uuid-dev python3 \
	python3-mysqldb python3-pip python3-dev libmysqlclient-dev libuv1-dev netcat libwebsockets16 \
	libwebsockets-dev libjson-c-dev libbpfcc-dev liblz4-dev libjudy-dev libelf-dev libmnl-dev \
	autoconf-archive curl cmake protobuf-compiler protobuf-c-compiler lm-sensors python3-psycopg2 \
	python3-pymysql libssl-dev libprotobuf-dev g++ flex bison nmap

#02_ Clonando o projeto do Netdata Server do Github<br>

	#clonando o projeto do Github do Netdata
	#opção do comando git clone: --recurse-submodules (initialize and clone submodules within based 
	#on the provided pathspec), --depth (create a shallow clone with a history truncated to the 
	#specified number of commits)
	git clone --recurse-submodules https://github.com/netdata/netdata --depth=100

#03_ Compilando e Instalando o Netdata Server<br>

	#acessando o diretório clonado e instalando o Netdata Server
	#opção do ./: execução de script desenvolvido em Shell Script .sh
	cd netdata/
		sudo ./netdata-installer.sh
	cd ..

#04_ Verificando o Serviço e Versão do Netdata Server<br>

	#verificando o serviço do Netdata Server
	sudo systemctl status netdata
	sudo systemctl restart netdata
	sudo systemctl stop netdata
	sudo systemctl start netdata

	#verificando a versão do Netdata Server
	#opção do comando netdata: -v (version)
	sudo netdata -v

#05_ Verificando a Porta de Conexão do Netdata Server<br>

	#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
	sudo lsof -nP -iTCP:'19999' -sTCP:LISTEN

#06_ Habilitando as atualizações do Netdata Server<br>

	#habilitando o suporte para atualização do Netdata Server
	sudo /usr/libexec/netdata/netdata-updater.sh --enable-auto-updates

#07_ Criando o usuário de monitoramento do MySQL Server do Netdata Server<br>

	#opções do comando mysql: -u (user), -p (password)
	sudo mysql -u root -p

		#criando o usuário do Netdata no MySQL
		CREATE USER 'netdata'@'localhost';

		#aplicando as permissões do usuário do Netdata no MySQL
		GRANT USAGE, REPLICATION CLIENT ON *.* TO 'netdata'@'localhost';

		#fazendo o flush das permissões e saindo do MySQL
		FLUSH PRIVILEGES;

		#verificando o usuário do Netdata criado no MySQL
		SELECT user,host FROM mysql.user;

		#saindo do MySQL
		exit

#09_ Adicionado o Usuário Local no Grupo Padrão do Netdata Server<br>

	#opções do comando usermod: -a (append), -G (groups), $USER (environment variable)
	sudo usermod -a -G netdata $USER
	newgrp netdata
	id
	
	#recomendado reinicializar a máquina para aplicar as permissões
	sudo reboot

#10_ Localização dos Arquivos de Configuração do Netdata Server<br>

	/etc/netdata/netdata.conf           <-- arquivo de configuração do serviço do Netdata Server
	/etc/netdata/apps_groups.conf       <-- arquivo de configuração dos Grupos de Aplicativos do Netdata Server
	/etc/netdata/go.d/apache.conf       <-- arquivo de monitoramento do Apache2 Server
	/etc/netdata/go.d/mongodb.conf      <-- arquivo de monitoramento do MongoDB Server
	/etc/netdata/go.d/mysql.conf        <-- arquivo de monitoramento do MySQL Server
	/etc/netdata/python.d/tomcat.conf   <-- arquivo de monitoramento do Apache Tomcat
	/etc/netdata/go.d/ping.conf         <-- arquivo de monitoramento do ICMP Ping
	/etc/netdata/go.d/portcheck.conf    <-- arquivo de monitoramento do Port Check

#11_ Configurando os Serviços de Monitoramento do Netdata Server<br>

	#OBSERVAÇÃO IMPORTANTE: cuidado na hora de configurar os serviços de monitoramento do
	#Netdata Server, os arquivos de configuração são baseados na Linguagem de Programação
	#Python utilizando o conceito do YAML (YAML Ain't Markup Language), não se utiliza TAB
	#sempre utilizar 02 (dois) espaços para endentar o código.

	#acessando o diretório de configuração do Netdata Server
	cd /etc/netdata/

	#configuração do serviço de monitoramento do Apache Server
	#https://learn.netdata.cloud/docs/data-collection/web-servers-and-web-proxies/apache
	sudo ./edit-config go.d/apache.conf

	#editar as informações a partir da linha: 8
	jobs:
	  - name: ocsinventory
	    url: http://localhost/server-status?auto

	  - name: ocsinventory
	    url: http://127.0.0.1/server-status?auto

	#salvar e sair do arquivo
	Ctrl + X
		Save modified buffer? Y
		File Name to Write: <Enter>

	#configuração do serviço de monitoramento do MySQL Server
	#https://learn.netdata.cloud/docs/data-collection/databases/mysql
	sudo ./edit-config go.d/mysql.conf

	#editar as informações a partir da linha: 52
	jobs:
	  - name: ocsinventory
	    dsn: netdata@tcp(127.0.0.1:3306)/

	#salvar e sair do arquivo
	Ctrl + X
		Save modified buffer? Y
		File Name to Write: <Enter>

	#configuração do serviço de monitoramento do ICMP Ping
	#https://learn.netdata.cloud/docs/data-collection/synthetic-checks/ping
	sudo ./edit-config go.d/ping.conf

	#editar as informações a partir da linha: 10
	jobs:
	  - name: google 
		hosts:
		- 8.8.8.8

	  - name: ocsinventory 
		hosts:
		- 172.16.1.50

	#salvar e sair do arquivo
	Ctrl + X
		Save modified buffer? Y
		File Name to Write: <Enter>

	#configuração do serviço de monitoramento das Portas TCP Endpoint
	#https://learn.netdata.cloud/docs/data-collection/synthetic-checks/tcp-endpoints
	sudo ./edit-config go.d/portcheck.conf

	#editar as informações a partir da linha: 8
	jobs:
	  - name: ocsinventory
	    host: 172.16.1.50
	    ports: [22, 80, 443, 3306, 19999]

	#salvar e sair do arquivo
	Ctrl + X
		Save modified buffer? Y
		File Name to Write: <Enter>

	#verificando os arquivos de configuração do monitoramento criados
	#opção do comando ls: -l (long listing), -h (human-readable)
	ls -lh go.d/

	#reinicializar o serviço do Netdata Server
	sudo systemctl restart netdata
	sudo systemctl status netdata

	#verificando todos os serviços iniciados e rodando no Ubuntu Server
	#opção do comando systemctl: list-units (List units that systemd currently has in memory)
	#--type (list of unit types such as service and socket), --state (list of unit LOAD, SUB, 
	#or ACTIVE states)
	sudo systemctl list-units --type=service --state=running

	#OBSERVAÇÃO IMPORTANTE: no vídeo as portas listadas com o comando: nmap só listou
	#as portas conhecidas, para listar todas as portas adicionei a opção: -p-

	#verificando todas as portas abertas no Ubuntu Server
	#opção do comando nmap: -p- (port ranges all) -sS (scan TCP SYN), -sU (scans UDP)
	#opção do comando grep: -i (ignore-case)
	#opção do comando cat: -n (number line)
	#opção do redirecionador |: Conecta a saída padrão com a entrada padrão de outro comando
	sudo nmap -p- 172.16.1.50 -sS -sU | grep -i open | cat -n

	#verificando todas as portas de serviços no Ubuntu Server
	#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
	sudo lsof -nP -iTCP:'22,80,443,3306,19999' -sTCP:LISTEN

#12_ Acessando e configurando o Netdata Server no navegador<br>

	firefox ou google chrome: http://endereço_ipv4_ubuntuserver:19999