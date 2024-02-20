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

Release Notes Ubuntu Server 22.04.x: https://discourse.ubuntu.com/t/jammy-jellyfish-release-notes/24668<br>
Ubuntu Advantage for Infrastructure: https://ubuntu.com/advantage<br>
Ciclo de Lançamento do Ubuntu Server: https://ubuntu.com/about/release-cycle<br>
Releases All Ubuntu Server: https://wiki.ubuntu.com/Releases

#01_ Verificando as informações do Locale (Localidade) do Sistema Operacional Ubuntu Server<br>
	
	#verificando as informações de localidade do sistema
	#opção do comando locale: -a (all-locales)
	sudo localectl
	sudo locale -a

#02_ Configurando o Locale (Localidade) do Brasil no Sistema Operacional Ubuntu Server<br>
	
	#gerando a localidade do Português do Brasil
	sudo locale-gen pt_BR.UTF-8

	#configurando a localidade Português do Brasil
	#opção do comando localectl: set-locale (Set the system locale)
	sudo localectl set-locale LANG=pt_BR.UTF-8

	#atualizando as localidades do Português do Brasil e Linguagem do Sistema
	sudo update-locale LANG=pt_BR.UTF-8 LC_ALL=pt_BR.UTF-8 LANGUAGE="pt_BR:pt:en"
	
	#recomendado rebootar o sistema para testar as localidades
	sudo reboot

	#opção do comando locale: -a (all-locales)
	sudo localectl
	sudo locale -a

#03_ Verificando as informações do Timezone (Fuso Horário) do Sistema Operacional Ubuntu Server<br>

	#verificando as informações de fuso horário
	sudo timedatectl

#04_ Configurando o Timezone (Fuso Horário) de São Paulo no Sistema Operacional Ubuntu Server<br>

	#OBSERVAÇÃO IMPORTANTE: geralmente mudar para o Time Zone de America/Sao_Paulo a hora
	#fica errada no sistema, nesse caso podemos mudar para America/Fortaleza ou America/Bahia
	#esse error e por causa do Fuso Horário em relação ao Horário de Verão que não existe 
	#mais no Brasil

	#configurando o fuso horário de America São Paulo
	#OBSERVAÇÃO: ALTERAR CONFORME A SUA LOCALIDADE DO SEU SERVIDOR
	#opção do comando timedatectl: set-timezone (set the system time zone to the specified value)
	sudo timedatectl set-timezone "America/Sao_Paulo"
	sudo timedatectl

#05_ Configurando o Sincronismo de Data e Hora com o Protocolo NTP no Ubuntu Server<br>

	#O NTP é um protocolo para sincronização dos relógios dos computadores baseado no protocolo 
	#UDP sob a porta 123. É utilizado para sincronização do relógio de um conjunto de computadores 
	#e dispositivos em redes de dados com latência variável.
	
	#editando o arquivo de configuração timesyncd.conf
	sudo vim /etc/systemd/timesyncd.conf
	INSERT
		
		#descomentar e alterar os valores das variáveis a partir da linha 14
		[Time]
		NTP=a.st1.ntp.br
		FallbackNTP=a.ntp.br
	
	#salvar e sair do arquivo
	ESC SHIFT : x <Enter>

#06_ Reinicializar o serviço do Systemd Timesyncd (Sincronismo de Data e Hora) no Ubuntu Server<br>

	#reiniciar o serviço do Timesyncd
	sudo systemctl restart systemd-timesyncd.service
	
	#verificar o status do serviço do Timesyncd
	sudo systemctl status systemd-timesyncd.service
	
	#verificar as informações do fuso horário e sincronismo
	sudo timedatectl

#07_ Configuração de Data e Hora manualmente do Sistema Operacional Ubuntu Server

	#OBSERVAÇÃO IMPORTANTE: só utilizar as configurações de Data e Hora em modo manual caso
	#as configurações de sincronismo automático não funcione de forma adequada, não recomendo
	#configuração Data e Hora em modo manual, pois isso é um alerta de erro de sistema.

	#opção do comando date: -s (set), %d (day of month), %m (month), %Y (year), %H (hour), 
	#%M (minute), %S (second)
	sudo date
	sudo date +%d/%m/%Y
	sudo date -s 20/01/2023
	sudo date +%H:%M:%S
	sudo date -s 13:30:00

#08_ Sincronizando Data e Hora do Sistema Operacional com o Hardware (BIOS) no Ubuntu Server<br>

	#OBSERVAÇÃO IMPORTANTE: mesmo cenário da utilização do comando date, da Data e hora da BIOS
	#do Hardware e mantida pela CMOS e Bateria que mantém essa hora armazenada, caso a Data e
	#Hora de BIOS esteja errada, recomendo verificar a Bateria pois já é um sinal de falha de
	#Hardware, no GNU/Linux você pode sincronizar a Data hora de Software para o Hardware e 
	#vice-versa, também não recomendo a sua utilização.

	#opção do comando hwclock: --systohc (system clock to hardware clock), --hctosys (hardware 
	#clock to system clock)
	sudo hwclock --show
	sudo hwclock --systohc
	sudo hwclock --hctosys