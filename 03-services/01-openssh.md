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
#Versão: 0.02<br>

Site Oficial do OpenSSH: https://www.openssh.com/<br>
Site Oficial do OpenSSL: https://www.openssl.org/<br>
Site Oficial do PuTTY: https://www.putty.org/

OpenSSH é um conjunto de utilitários de rede relacionado à segurança que provém a criptografia<br> 
em sessões de comunicações em uma rede de computadores usando o protocolo SSH.

#01_ Instalando o OpenSSH Server e Client no Ubuntu Server<br>

	#atualizando as listas do Apt
	sudo apt update
	
	#OBSERVAÇÃO IMPORTANTE: executar a instalação somente se você no processo de instalar
	#o Ubuntu Server não marcou a opção: Install OpenSSH, caso contrário o mesmo já está
	#instalado e pré-configurado.

	#instalando o OpenSSH Server e Client
	sudo apt install openssh-server openssh-client openssl 

#02_ Verificando o Serviço e Versão do OpenSSH Server e Client no Ubuntu Server<br>

	#verificando o serviço do OpenSSH Server
	sudo systemctl status ssh
	sudo systemctl restart ssh
	sudo systemctl stop ssh
	sudo systemctl start ssh

	#verificando as versões do OpenSSH Server e Client
	#opção do comando sshd e ssh: -V (version)
	sudo sshd -V
	sudo ssh -V

#03_ Verificando a Porta de Conexão do OpenSSH Server<br>

	#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
	sudo lsof -nP -iTCP:'22' -sTCP:LISTEN

#04_ Localização dos Arquivos de Configuração do OpenSSH Server<br>

	/etc/ssh/             <-- Diretório de configuração do OpenSSH Server e Client
	/etc/ssh/sshd_config  <-- Arquivo de configuração do OpenSSH Server
	/etc/ssh/ssh_config   <-- Arquivo de configuração do OpenSSH Client
	/etc/hosts.deny       <-- Arquivo de configuração do Firewall de Aplicação TCPWrappers Deny
	/etc/hosts.allow      <-- Arquivo de configuração do Firewall de Aplicação TCPWrappers Allow
	/etc/issue.net        <-- Arquivo de configuração do Banner do Ubuntu Server para acesso remoto
	/var/log/             <-- Diretório de Logs do Sistema Operacional Ubuntu Server
	/var/log/syslog       <-- Log principal do Sistema Operacional Ubuntu Server
	/var/log/auth.log     <-- Log principal das autenticações do Sistema Operacional Ubuntu Server

#05_ Habilitando a segurança de acesso ao OpenSSH Server<br>

	#editando o arquivo de configuração de Negação de Serviço e Host
	sudo vim /etc/hosts.deny
	INSERT

		#inserir as informações na linha 17
		ALL: ALL

	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

	#editando o arquivo de configuração de Liberação de Serviço e Host
	sudo vim /etc/hosts.allow
	INSERT

		#inserir as informações na linha 10
		#OBSERVAÇÃO: ALTERAR A REDE CONFORME A SUA NECESSIDADE
		sshd: 172.16.1.0/24

	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

#06_ Atualizando e editando os arquivos de configuração do OpenSSH Server e do Banner<br>

	#atualizando o arquivo de configuração do OpenSSH Server
	#opção do comando wget: -v (verbose), -O (output file)
	sudo wget -v -O /etc/ssh/sshd_config https://raw.githubusercontent.com/vaamonde/ocsinventory-ng/main/conf/sshd_config

	#atualizando arquivo de configuração do Banner do Ubuntu Server
	sudo wget -v -O /etc/issue.net https://raw.githubusercontent.com/vaamonde/ocsinventory-ng/main/conf/issue.net

	#editando o arquivo de configuração do OpenSSH Server
	sudo vim /etc/ssh/sshd_config
	INSERT

		#alterar a linha 27: ListenAddress 172.16.1.xxx para: SEU_ENDEREÇO_IPV4_DO_UBUNTU
		#OBSERVAÇÃO: ALTERAR O ENDEREÇO IPv4 CONFORME A SUA NECESSIDADE
		ListenAddress 172.16.1.30

		#alterar a linha 77: AllowUsers
		#OBSERVAÇÃO: ALTERAR O USUÁRIO DE ACESSO CONFORME A SUA NECESSIDADE
		AllowUsers vaamonde

		#alterar a linha 83: AllowGroups
		#OBSERVAÇÃO: ALTERAR O GRUPO DE ACESSO CONFORME A SUA NECESSIDADE
		AllowGroups vaamonde

	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

	#editando o arquivo de configuração do Banner do Ubuntu Server
	sudo vim /etc/issue.net
	INSERT

		#alterar a linha 5: Servidor e Admin
		#OBSERVAÇÃO: ALTERAR O BANNER CONFORME A SUA NECESSIDADE
		Servidor: ocsinventory - Admin: Robson Vaamonde

	#salvar e sair do arquivo
	ESC SHIFT :x <Enter>

	#reiniciar o serviço do OpenSSH Server
	sudo systemctl restart ssh
	sudo systemctl status ssh

#07_ Acessando remotamente o OpenSSH Server via Powershell e pelo software PuTTY<br>

	Windows
		Pesquisa do Windows
			Powershell

	ssh vaamonde@172.16.1.30 (alterar para o endereço IPv4 do seu servidor)

	Windows
		Pesquisa do Windows
			PuTTY

	Category
		Session
			Host Name (or IP address): vaamonde@172.16.1.30 (alterar para o endereço IPv4 do seu servidor)
			Port: 22
			SSH: On
	<Open>

	Linux
		Terminal: Ctrl + Alt + T
			ssh vaamonde@172.16.1.30 (alterar o usuário e endereço IPv4 do seu servidor)
	
	#verificando os usuários logados remotamente no Ubuntu Server
	#opção do comando who: -H (heading), -a (all)
	w
	who -Ha
	users

#08_ Criando um usuário Administrador no Ubuntu Server<br>

	#OBSERVAÇÃO IMPORTANTE: NESSE EXEMPLO ESTÁ SENDO CRIADO UM USUÁRIO ADMIN PARA A
	#ADMINISTRAÇÃO DO SERVIDOR, NÃO RECOMENDO CRIAR UM USUÁRIO CHAMADO: admin POIS
	#É UM USUÁRIO CONHECIDO E EXISTE VÁRIOS SOFTWARE DE FORÇA BRUTA QUE USA ESSE
	#USUÁRIO PARA INVADIR SERVIDORES.

	#criando o usuário Admin
	sudo adduser admin
		New password: pti@2018
		Retype new password: pti@2018
			Full Name []: Admin Bora para Prática
			Room Number []: <Enter>
			Work Phone []: <Enter>
			Home Phone []: <Enter>
			Other []: <Enter>
		Is the information correct? [Y/n] y <Enter>
	
	#listando o usuário criado no arquivo passwd
	sudo cat /etc/passwd | grep admin

	#listando o usuário criado com o comando getent
	sudo getent passwd admin

	#listando o grupo criado no arquivo group
	sudo cat /etc/group | grep admin

	#listando o grupo criado com o comando getent
	sudo getent group admin

#09_ Adicionando o usuário Admin no grupo SUDO (Super User Do)<br>

	#adicionando o usuário Admin ao grupo do SUDO
	#opção do comando usermod: -a (append), -G (groups)
	sudo usermod -aG sudo admin

	#verificando os grupos do usuário Admin
	sudo groups admin

	#verificando as identificações de grupos do usuário Admin
	sudo id admin

#10_ Se logando no Terminal (Bash/Shell) do Ubuntu Server<br>

	OBSERVAÇÃO IMPORTANTE: fazer o teste de Login no Terminal do Ubuntu Server para
	verificar se está tudo OK na criação do usuário admin.