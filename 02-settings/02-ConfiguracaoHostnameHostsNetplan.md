#Autor: Robson Vaamonde<br>
#Procedimentos em TI: http://procedimentosemti.com.br<br>
#Bora para Prática: http://boraparapratica.com.br<br>
#Robson Vaamonde: http://vaamonde.com.br<br>
#Facebook Procedimentos em TI: https://www.facebook.com/ProcedimentosEmTi<br>
#Facebook Bora para Prática: https://www.facebook.com/BoraParaPratica<br>
#Instagram Procedimentos em TI: https://www.instagram.com/procedimentoem<br>
#YouTUBE Bora Para Prática: https://www.youtube.com/boraparapratica<br>
#Data de criação: 20/02/2024<br>
#Data de atualização: 25/02/2024<br>
#Versão: 0.02<br>

Release Notes Ubuntu Server 22.04.x: https://discourse.ubuntu.com/t/jammy-jellyfish-release-notes/24668<br>
Ubuntu Advantage for Infrastructure: https://ubuntu.com/advantage<br>
Ciclo de Lançamento do Ubuntu Server: https://ubuntu.com/about/release-cycle<br>
Releases All Ubuntu Server: https://wiki.ubuntu.com/Releases

Netplan é um utilitário para configurar facilmente a rede em um sistema Linux. Você<br>
simplesmente cria uma descrição YAML das interfaces de rede necessárias e o que cada<br> 
uma deve ser configurada para fazer. A partir desta descrição o Netplan irá gerar toda<br> 
a configuração necessária para a ferramenta de renderização escolhida.

Hostname: é usado para exibir o nome DNS do sistema e para exibir ou defina seu nome<br> 
de host ou nome de domínio NIS. O arquivo /etc/hostname armazena as informações de<br> 
nome de máquina e domínio no formato FQDN (Fully Qualified Domain Name)

FQDN, algumas vezes denominado nome de domínio absoluto, é um nome de domínio que<br> 
especifica sua localização exata na árvore hierárquica do Domain Name System. Ele<br> 
especifica todos os níveis de domínio, incluindo, pelo menos, um domínio de segundo<br> 
nível e um domínio de nível superior.

Hosts: pesquisa de tabela estática para nomes de host, é utilizado quando não temos<br> 
servidores DNS (Domain Name System) e fazermos o apontamento diretamente no arquivo<br> 
localizado em /etc/hosts

#01_ Alterando o nome FQDN do Ubuntu Server<br>

	#editando o arquivo de configuração do Hostname
	sudo vim /etc/hostname
	INSERT
		
		#adicionar o nome de domínio na linha 1
		#OBSERVAÇÃO IMPORTANTE: ALTERAR O NOME DO DOMÍNIO PARA O SEU CENÁRIO
		ocsinventory.pti.intra
	
	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

#02_ Alterando as entradas no arquivo Hosts do Ubuntu Server<br>

	#editando o arquivo de configuração do Hosts
	sudo vim /etc/hosts
	INSERT
		
		#adicionar o nome de domínio e apelido na linha 2 e 3
		#OBSERVAÇÃO IMPORTANTE: ALTERAR O NOME DO DOMÍNIO E APELIDO PARA O SEU CENÁRIO
		127.0.0.1    localhost.localdomain    localhost
		127.0.1.1    ocsinventory.pti.intra   ocsinventory
		172.16.1.30  ocsinventory.pti.intra   ocsinventory
	
	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

#03_ Instalando os principais software de rede no Ubuntu Server<br>

	#atualizando as lista do sources.list e instalando os pacotes
	sudo apt update
	sudo apt install bridge-utils ifenslave net-tools

#04_ Verificando informações do Hardware de Rede no Ubuntu Server<br>

	#verificando os dispositivos PCI de Placa de Rede instalados
	#opções do comando lspci: -v (verbose), -s (show)
	#opção do comando grep: -i (ignore-case)
	#opção do redirecionador | (pipe): Conecta a saída padrão com a entrada padrão de outro comando
	sudo lspci -v | grep -i ethernet

	#verificando os detalhes do hardware de Placa de Rede instalada
	sudo lshw -class network

#05_ Verificando as informações de Endereços IPv4 no Ubuntu Server<br>

	#verificando as configurações de endereçamento IP da Placa de Rede instalada
	#opção do comando ifconfig: -a (all)
	sudo ifconfig -a
	sudo ip address show
	
	#verificando as configurações de Gateway (route)
	#opção do comando route: -n (number)
	sudo route -n
	sudo ip route
	
	#verificando as informações de cache dos Servidores DNS (resolução de nomes)
	sudo resolvectl

#06_ Alterando as configurações da Placa de Rede do Ubuntu Server<br>

	#OBSERVAÇÃO: o nome do arquivo pode mudar dependendo da versão do Ubuntu Server.
	#/etc/netplan/00-installer-config.yaml #Padrão do Ubuntu Server 22.04.x LTS
	
	#OBSERVAÇÃO IMPORTANTE: o arquivo de configuração do Netplan e baseado no formato 
	#de serialização de dados legíveis YAML (Yet Another Markup Language) utilizado 
	#pela linguagem de programação Python, muito cuidado com o uso de espaços e 
	#tabulação e principalmente sua indentação.

	#listando o conteúdo do diretório do Netplan
	#opção do comando ls: -l (long listing), -h (human-readable)
	ls -lh /etc/netplan/

	#fazendo o backup do arquivo de configuração original do Netplan
	#opção do comando cp: -v (verbose)
	sudo cp -v /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.old

	#editando o arquivo de configuração do Netplan
	sudo vim /etc/netplan/00-installer-config.yaml
	INSERT

    network:
      ethernets:
        #configuração da placa de rede cabeada
        enp0s3:
          #desativando o suporte ao protocolo IPv6 na interface
          link-local: []
          #desativando o DHCP Client na interface
          dhcp4: false
          #configurando o endereço IPv4 para o seu cenário
          #OBSERVAÇÃO IMPORTANTE: configuração do Endereço IPv4 dentro de Colchetes
          addresses: [172.16.1.30/24]
          #configurando o gateway padrão para o seu cenário
          #OBSERVAÇÃO IMPORTANTE: a opção de Gateway4 foi descontinuada, recomendo
          #utilizar as opções de Routes do Netplan para configurar o Gateway padrão
          #gateway4: 172.16.1.254
          routes:
            - to: default
              via: 172.16.1.254
          #configuração dos servidores de DNS para o seu cenário
          nameservers:
            #configurar os servidores DNS para o seu cenário
            #OBSERVAÇÃO: configuração do Endereço IPv4 dentro de Colchetes
            addresses: [172.16.1.254]
            #configurar a pesquisa de domínio para o seu cenário
            #OBSERVAÇÃO: configuração da pesquisa de Domínio dentro de Colchetes
            search: [pti.intra]
      version: 2

	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

#07_ Aplicando as configurações do Netplan e verificando as informações de Rede do Ubuntu Server<br>

	#aplicando as mudanças do Netplan em modo Debug (detalhado)
	sudo netplan --debug apply

	#OBSERVAÇÃO IMPORTANTE: você pode utilizar a opção: try que caso aconteça alguma
	#falha na hora de configurar a placa de rede ele reverte a configuração anterior
	sudo netplan --debug try

	#verificando o endereço IPv4 da Interface de Rede
	sudo ifconfig
	sudo ip address show

	#verificando as informações de Gateway padrão
	#opção do comando route: -n (numeric)
	sudo route -n
	sudo ip route

	#verificando as informações dos Servidores DNS e Pesquisa de Domínio
	sudo resolvectl

#08_ Desativando o suporte ao IPv6 no Ubuntu Server<br>

	#OBSERVAÇÃO IMPORTANTE: para esse cenário não será utilizado o suporte ao IPv6 no 
	#Servidor Ubuntu, caso seja necessário recomendo ver a documentação referente ao 
	#IPv6 do Netplan. UTILIZAR SOMENTE O RECURSO DE DESATIVAR O IPv6 NO UBUNTU SERVER
	#EDITANDO O ARQUIVO: /etc/sysctl.conf SOMENTE SE A OPÇÃO DO: link-local: [] NO
	#ARQUIVO: /etc/netplan/00-installer-config.yaml NÃO FUNCIONAR DE FORMA CORRETA.

	#editando o arquivo de configuração do sysctl.conf
	#opção do comando vim: + (For the first file the cursor will be positioned on line)
	sudo vim /etc/sysctl.conf +
	INSERT

		#adicionar o suporte para desabilitar o IPv6 no final do arquivo
		net.ipv6.conf.all.disable_ipv6=1
		net.ipv6.conf.default.disable_ipv6=1
		net.ipv6.conf.lo.disable_ipv6 = 1

	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

	#aplicando as alterações no sistema
	#opção do comando sysctl: -p (Load in sysctl settings from the file /etc/sysctl.conf)
	sudo sysctl -p

	#verificando se o IPv6 foi desativado no Ubuntu Server (valor 1 = Desativado)
	sudo cat /proc/sys/net/ipv6/conf/all/disable_ipv6

#09 Testando a conexão com a Internet utilizando somente o IPv4<br>

	#testando a conexão com a Internet
	ping 8.8.8.8
	ping google.com

	#verificando as informações do nome do servidor Ubuntu Server
	#opção do comando hostname: -A (all-fqdns), -d (domain), -i (ip address)
	sudo hostname
	sudo hostname -A
	sudo hostname -d
	sudo hostname -i

#10_ Acessando a máquina virtual do Ubuntu Server remotamente via SSH<br>

	#OBSERVAÇÃO: após a configuração da Placa de Rede do Ubuntu Server você já pode
	#acessar remotamente o seu servidor utilizando o Protocolo SSH nos clientes Linux
	#ou Microsoft para dá continuidade nas configurações do servidor, ficando mais
	#fácil administrar e configurar os principais serviços de rede e forma remota.

	#testando a conexão com o Ubuntu Server
	ping 172.16.1.30

	#acessando remotamente o Ubuntu Server
	ssh vaamonde@172.16.1.30

	#confirmando a troca das chaves públicas e do fingerprint do SSH
	Yes <Enter>