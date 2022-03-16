# CoCo3FPGA

Roteiros e dicas e arquivos da instalação do CoCo3FPGA na Altera DE-1

## Hardware

### Mínimo

* Uma placa Terasic Altera DE-1, que inclui, no kit original:
* Cabo USB A-B para ligá-la num PC
* Fonte de 7,5Vcc, 0.8A positivo no centro
* Cabo VGA para ligar ela num ...
* Monitor que aceite VGA 25kHz
* Um teclado PS/2
* PC rodando Windows/Linux

### Recomendado

* Uma porta serial + um cabo null model, para ligar essa porta a entrada DB-9 da placa, ou: uma porta USB, um conversor USB/Serial e algum cabo para ligar o conversor no PC
	- A porta serial é importante para rodar o Drivewire. Dá para ligar o CoCo3FPGA sem isso, e carregar programas pela ROM, mas se quiser usar "diskette", a comunicação é por serial.
* Vários Gigas de espaço em disco do PC
	- Com muito espaço em disco é possível instalar o Quartus II completo, o que permite "compilar" o CoCo3FPGA a partir dos fontes.

## Softwares

### Mínimo

* Quartus II Programmer 13.0sp1
	- Essa versão é bem antiga, mas é a mais recente que suporta o chip da Altera DE-1. As versões mais novas (13.1 pra frente) não suportam mais o chip da DE-1
	- A Intel comprou a Altera, então ficou mais difícil para baixar, especialmente as versões mais antigas. mas catando se acha.
	
* A DE-1 Control Panel versão 2.0.1
	- A versão 1.0 é bem bugada e não funcionou comigo
	- Ela vem no CD que pode ser baixado
	- É possível usar um outro software para substituí-lo, mas o Control Panel, uma vez acertado, é bem mais fácil de usar.
	- Onde baixar: https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=165&No=83&PartNo=4#heading no link do DE1 Control Panel. Recomendo baixar o CD ROM todo (DE1_v.1.0.2_SystemCD.zip), pois tem exemplos, testes, etc. que podem ajudar.

* Quartus II Web Edition 13.0sp1
	- Importante ser a Web Edition, que é gratuita (suponho que ninguém queira pagar pelo programa, mas se tiver a versão full, claro que ela funciona).
	- Com essa versão completa você vai poder compilar os fontes. Só com o Programmer só é possível fazer upload do circuito já compilado.

### Recomendado

* pyDrivewire
	- Em tese qualquer Drivewire 3 pra frente funciona, mas o Drivewire 3/4 foi escrito em Java 1.5, e mesmo colocando essa versão antiquíssima do Java ele não rodou. O pyDrivewire roda de boas.

## Outros

* Entrar no grupo de e-mail coco3fpga@groups.io. Lá a galera é bem legal e ajuda bastante. Tem os desenvolvedores originais e muita gente que manja muito de Color Computer. No entanto a lista é em inglês.

# Conceitos

Longe de entender muito das tecnologias envolvidas no projeto, esse é um apanhado do que fui aprendendo no processo de fazer o CoCo3FPGA funcionar. Algumas imprecisões ou simplificações aconteceram, inevitavelmente, e aceito correções dessa imprecisões.

Abaixo segue um glossário do quem-é-quem e o que é o que, com um monte de siglas que eventualmente aparecerão:

## Glossário:

*CoCo3FPGA*: O *CoCo3FPGA* é a síntese de Hardware do Tandy Color Computer 3. Ele é sintetizado em *HDL* originalmente para a *Terasic* *Altera* *DE-1*, mas existem adaptações para a DE-2, e, em tese, é possível portá-lo para placas mais novas. O projeto foi iniciado em 2007 pelo Gary Becker. Existem outros projetos de simular um hardware de CoCo1, 2 e 3 na internet com diferente níveis de desenvolvimento (Multicore, MiST Box, MisterCoCo, Matchbox CoCo, etc.) Esses diferentes projetos possuem diferentes requisitos e capacidades. Tipicamente, senão todos eles, usam uma placa Terasic (DE1, DE0-Nano, DE-10, etc.) e ligam a uma ou duas placas analógicas, que são placas com sensores/dispositivos/DACs, etc. para simular/permitir usar os periféricos. O CoCo3FPGA tem duas placas analógicas diferentes: ado Ed Snyder e a do Gary. Cada placa oferece recursos diferentes.

Ao que me parece, em algum momento do projeto a intenção foi fazer um Super Color 3 (ou 3+, 4, etc.), permitindo uma quantidade excepcional de memória (5MB), aumentar o Clock (para 25MHrz, ao invés do máximo de 1.78), etc. Isso como prova de conceito é interessante, mas os softwares clássicos ou não rodariam ou dariam muito problema. Diferentes projetos podem ter objetivos diferentes (preservar compatibilidade, etc.)

Junto com o ZIP que tem o projeto vem um PDF que recomendo ler. Ele especifica todos os recursos que foram implementados. Na 4.1 tem:

* Implementação do 6809 (ou seja, nada de 6309)
	- O Clock é o Clock original e o Switch 0 (SW0) permite que o modo turbo (originalmente 1.78MHz) vá para 5 ou 25 MHz (depende da placa DE-1, depois explico)
	- O timing das instruções não é exatamente igual ao do processador original. Logo alguns programas dependente de tempo podem dicar estranhos. A princípio o core é 15% mais rápido.

* ROM/RAM
	- A ROM é programada na memória Flash da placa (usando o Control Panel). A ROM pode ser a ROM original do Color 3, sem modificações.
	- Ele vem com 512KB de RAM estática (o Coco3 tinha 128K de ROM dinâmica, podendo se expandido depois)

* MPI (Multi Pack Interface)
	- O projeto já vem com a MPI "instalada". Ou seja ele vem com 4 "entradas de cartuchos", selecionadas pelos Switch SW1 e SW2.
		- Slot 1 (Off Off): Orchestra 90CC ROM ($0A000 para 8K)
		- Slot 2 (Off On): Disk BASIC ROM ($3F8000 para 32K, $3FC000 para 8K)
		- Slot 3: (On Off): Multiple Cartridge ROM, que é uma interface para simular cartuchos. É possível com ela gravar os cartuchos na FLASH e escolher qual cartucho rodar.
		- Slot 4: (On On): Disk BASIC ROM and Disk Interface ($08000 para 8K)
	- O Switch 4, permite que o habilitar que cartucho faça Autostart (Off = enable Autostart, On disable Autostart)

* Vídeo
	- Todos os modos de vídeo do CoCo3 original são suportados, e alguns modos novos e estendidos foram adicionados. Ele suporta os modos semigráficos 3 (do CoCo 3) e também suporta os modos 1 e 2 do CoCo 1 e 2.
	- Os tempos o vídeo são um pouquinho diferentes, mas espero que seja imperceptível. Como a maioria dos modos de vídeo tem duas linhas de Scan, o Switch SW3, escurece a segunda linha.

* Interface Floppy (Drivewire)
	- A interface de disco é emulada com um processador que fala o protocolo do Drivewire pela porta serial. Para usá-la você vai precisar de um cabo serial (possivelmente um conversor Serial para USB e um cabo USB para ligar ao seu PC).
	- No PC você precisará do Drivewire instalado. O Drivewire mais recente (o 4) roda em Java 5 (1.5), que é bastante antigo. E nem instalando o Java eu consegui rodar. o pyDrivewire é uma implementação do Drivewire em Python que é bem mais moderna. Rodei o Drivewire num CoCoPi (uma imagem do Raspian com vários softwares de suporte para CoCo no Raspberry Pi)
	- Os Switches SW8 e SW7 controlam a velocidade. A menor velocidade é 112500. Como meu USB/Serial não faz mais que isso, nem me preocupei de mudar esses switches (deixe em off e off).

* RS232 PAK
	- Implementaram o RS232 PAK. Ele funciona melhor com a Analog Board, mas é possível usá-lo sem ela (mas vai perder o Drivewire). Com a placa analógica eles disponibilizam uma outra interface RS232. É possível usar essa interface sem a placa, puxando direto o nível TTL do pino. Mas não tentei.
	- O SW9 "inverte" as portas seriais, ou seja, se ele ficar no Off, o Drivewire dica no DB-9 original da DE-1. Se ele for On, o DB-9 fica com a RS-232 e o Drivewire fica na placa analógica.

* Som
	- O som foi implementado com o hardware local da DE-1. Está emulando tanto o som  nativo com a Orchestra-90CC. A saída de áudio é pelo pino verde da placa (mais colado com a saída VGA)

* Teclado
	- O Teclado PS/2 não é o mesmo formato do teclado do CoCo. Alguns ajustes foram feitos (especialmente num teclado ABNT2).
		- A tecla ^~ (ao lado do Ç) é aspas duplas ("). É a tecla mais útil. :-)

* Joysticks:
	- Só com a placa analógica é possível usar os joysticks. Logo, sem ela a maioria dos jogos "de ação" não vão rodar.

	As placas oferecem coisas novas, até funcionalidade de WiFi. Não me pergunte pra que. :-)

### Coisa que não vi implementado:
	* Artifact no modo 256 x 192
	* Leitura de K7 (pra que se você tem o Drivewire?)

*HDL (Hardware Description Language)*: É uma linguagem de descrição de hardware. As mais conhecidas (porém não as únicas) são Verilog e VHDL. São linguagens descritivas, que especificam como hardware ou parte dele devem funcionar, e não procedurais (que descreveriam o que o hardware tem que fazer). São linguagens diferentes que servem par ao mesmo propósito.

*Terasic*: Terasic é uma Empresa de Taiwan que, entre outras coisas desenvolve placas de FPGA para computação de alta performance, trading de alta frequência, processamento de redes, etc. Entre vários produtos ele tem uma linha de placas (DE) para aprendizado e prototipagem. A *Altera* *DE-1* é um dos produtos dela.

*Altera*: a Altera é uma fabricante de PLDs (Programmable Logic Devices - dispositivos de lógicos programáveis) em San José, Califórnia. Foi fundada em 1983 e adquirida pela Intel em 2015. Ela produz chips FPGA. As linhas de chips FPGAs são: Stratix (high-end), Arria (mid-range), Cyclone (lower-cost); a série MAX para dispositivos complexos e FPGAs não voláteis. Além disso ela desenvolve o software de desenvolvimento e design Intel Quartus Prime, entre outros produtos.

*DE-1*: É uma placa de desenvolvimento e educação em FPGA desenvolvida pela Terasic. A DE-1 é uma placa que não é mais produzida. Existe uma placa chamada DE1-SoC (System os a chip), que é diferente da placa DE-1 original. 

Existem várias placas mais modernas (lista das que estão em produção em http://www.terasic.com.tw/wiki/Terasic_Host_FPGA_Board), mas nenhuma foi compatibilizada pelo projeto.
Site oficial da DE-1: https://www.terasic.com.tw/cgi-bin/page/archive.pl?No=83).
Página de suporte e documentação: https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=165&No=83&PartNo=4#heading

A DE-1 contém, entre outras coisas:

* FPGA Cyclone II, que é o chip de lógica programável
* SRAM de 512KB
* Flash de 4MB
* SDRAM de 8MB
* 10 Chaves on/off: para "baixo", ou seja para a borda da placa é off. Para cima (pros leds) é on.
* 4 botões
* 10 leds vermelhos
* 8 leds verdes
* Cartão SD
* Saída DB9 serial
* Saída DB-15 VGA
* Saída de áudio
* Entrada de áudio
* Entrada de microfone mono
* Conector PS/2
* Conector USB para programar a placa direto com um cabo USB A-B (tipo de impressora). Não é preciso de um cabo especial para programar a placa (o mais conhecido é o USB Blaster). É como se o USB-Blaster estivesse embutido na placa.
* Conector de força (7,5v 0,8A Positivo no centro)
* 4 displays de 7 segmentos
* 2 GPIOs de 40 pinos cada.

Há também uma chavinha PROG/RUN que serve para colocar a placa em modo de programação/execução.

*Quartus II/Quartus/Quartus Prime*: É o software que funciona como uma IDE de desenvolvimento para FPGA. É um software bem complexo e bem completo. Para o projeto você precisará da versão 13.0spo1. Versões mais novas (13.1 em diante) não são compatíveis com a Cyclone II. Com a aquisição da Altera pela Intel, agora o software fica no site da Intel. Busque por "web 13.0"

https://www.intel.com/content/www/us/en/collections/products/fpga/software/downloads.html?edition=web&s=Newest&q=web%2013.0 e clique em "Intel® Quartus® II Web Edition Design Software Version 13.0sp1 for Windows"

Se ainda estiver funcionando o link é esse: https://www.intel.com/content/www/us/en/software-kit/711791/intel-quartus-ii-web-edition-design-software-version-13-0sp1-for-windows.html?

Você pode baixar um pacote completo: (aba Multiple Download)

* Quartus-web-13.0.1.232-windows.tar Size: 4.4 GB SHA1: ada26bcb93044169c38c1e5a319cea5acc501438
	- Esse vai instalar tudo com o suporte a todos os chips. Você, em tese só precisará dele.

Ou arquivos individuais: (mais arriscado, pode faltar algo, aba Individual Files) 

* Programa principal:
	- Intel® Quartus® II Software (includes Nios® II EDS)	QuartusSetupWeb-13.0.1.232.exe	Size: 1.5 GB	SHA1: e4a47c813e9f80f930c70d56e14d35b4ea9e0064
* Arquivo de suporte:
	- Intel® Cyclone® II, Intel® Cyclone® III, Intel® Cyclone® IV Device Support (includes all variations)	cyclone_web-13.0.1.232.qdz	Size: 568.9 MB	SHA1: 882b19a8ffee1edd133693ff422a3f6c5a615589

* Se espaço for um problema grave e você não desejar compilar o projeto, pode baixar só o Programmer na aba “Additional software” (se instalar o Quartus, o Programmer vem junto)
	- Intel® Quartus® II Programmer and SignalTap II	QuartusProgrammerSetup-13.0.1.232.exe	Size: 136.6 MB	SHA1: 180bc0bb8fc4e3828d052cce63ae467b86bf6764

*DE-1 Control Panel*: Esse é um software da Terasic que permite controlar a placa DE-1. Você pode acender cada led, ver o status de cada chave ou botão, inspecionar/escrever nas memórias SDRAM, SRAM e Flash, testar os conectores serial e PS/2 e testar o VGA. Serve muito bem para ver se a placa está funcionando. Não precisa do Quartus para ele funcionar. Ele vai ser usado para gravar o conteúdo da Flash, que conterá os dados da ROM (Color Basic), Dos cartuchos do MPI, da memória estendida, etc.

Existem várias versões do Control Panel. a 1.0.0 eu não consegui fazer funcionar. Em todas as versões é preciso programar (temporariamente) a placa para colocar no FPGA o "circuito" que fala com o Control Panel. Na versão 2.0 esse upload é automático.

Existe um programa chamado "flash programmer" que pode fazer o papel do Control Panel.

*Flash Programmer*: feito pelo "Dave Philipsen" no próprio que faz o papel de programar a flash. Eu não usei. Curiosidade que ele implementou um 6809 no FPGA pra fazer a gravação da flash. :-). Também precisa fazer o upload do circuito no FPGA e rodar um terminal XMODEM.

Pode baixar em https://groups.io/g/CoCo3FPGA/files/Support%20files#:~:text=Flash%2Dprogrammer.zip%C2%A0%C2%A0

# Sites

* Wiki do projeto (parece um dos manuais em formato HTML) http://www.davebiz.com/wiki/CoCo3FPGA

# Instalação dos softwares/arquivos para baixar

* Instalar o Quartus II ou só o programmer.
	- O Quartus II serve para desenvolver "a partir dos fontes". É possível instalar só o programmer, que é o software que faz "upload" do circuito pra placa. Claro que exige bem menos espaço em disco, mas você não vai conseguir tunar nada.

* Instalar o DE-1 Control Panel
	- Expanda o Zip do SystemCD (DE1_v.1.0.2_SystemCD.zip) o Control Panel está em: DE1_v.1.0.2_SystemCD\DE1_control_panel

* Baixar os arquivos de https://groups.io/g/CoCo3FPGA/files (precisa participar da lista de discussão)
	- Os dois arquivos de "aa-CoCo3FPGA for terasIC DE1":
		- CoCo3FPGA_4.1.zip:  é o código fonte e a compilação oficial.
		- CoCo3FPGA_4.1_Extra_Builds: O segundo são outras compilações.

	- De “Support files”:
		- DE1_Flash.bin: É uma cópia da memória com as ROMs, DISK Basic, Orquestra, tudo direitinho. Pra começar, é melhor usar ela.

# Rodando o CoCo3FPGA

Se você, como eu, não tem experiência prévia de FPGA, eu recomendo fazer por partes:

## 1) Testar a Placa, a conectividade, o ambiente de desenvolvimento (Quartus) e se familiarizar com o processo de compilação e upload.

A placa tem que estar com o cabo USB e o cabo de força ligado. Sem os dois ligados a placa não liga.
O Switch PROG/RUN deve estar em Run.
Aperte o botão de ligar (vermelho, perto da entrada da fonte).

Quando você liga a placa ela vem de fábrica com um programinha que faz os leds "dançarem" (se não tiver, relaxa. Dá para subir esse circuito). A melhor forma de testar a placa é com o Control Panel.

Rode o EXE que está em DE1_v.1.0.2_SystemCD\DE1_control_panel com a placa em modo "Run"

Ele dá um erro dizendo "Make sure Quartus is installed", click ok e vai na fé. (claro, você tem que ter o Quartus Instalado).

Se conectar direito, no canto inferior esquerdo vai ficar um texto verde escrito “Connected”. E todos os leds vão acender (inclusive do displays de 7 segmentos). Brinca a vontade com os controles.

Aproveita que está com o Control Panel ligado e grava logo a Flash.

* Clique no botão "Memory":
* Escolha o Memory Type: "Flash (200000h WORDS) 4MB
* Em Random Access: Clica em Chip Erase (80 sec)
* Em Sequential Write: Address: 00000000, Clica em File Length
* Escolhe o arquivo DE1_Flash.bin (de 72K mais ou menos)
* Espere até terminar.

## 2) Instalar o CoCo3FPGA já compilado só pra ver a tela verde do DISK BASIC

Você vai precisar colocar o cabo VGA. Aproveita e liga o teclado.

### Tipo da SRAM

As placas DE-1 mais antigas tinham uma RAM melhor do que as placas mais recentes (que já são antigas). O Timing das RAMs das placas novas é pior, e elas não conseguem todos os 25MHz.
A versão padrão do COCo3FPGA é feira para a placa antiga com a RAM mais rápida e com a placa analógica.
Descubra qual Chip de SRAM tem na sua DE-1. Procure o Chip na placa (fica na esquerda e um pouco acima do display de 7 segmentos) onde está escrito SRAM 512K.

Novas placas: IS61WV25616BLL-10
Antigas: IS61LV25616AL-10

A instalação padrão (no CoCo3FPGA_4.1.zip) é feita a placa analógica do Ed e a RAM antiga. A minha placa é "nova" e não tenho placa analógica, então preciso pegar o arquivo da CoCo3FPGA_4.1_Extra_Builds: ...\CoCo3FPGA_4.1_Extra_Builds\Ed_or_No

### Sobre o formato dos arquivos

Agora uma pequena pausa sobre os formatos de arquivos e FPGAs, pois é preciso entender um pouco pra fazer a instalação.

Os dispositivos FPGA simulam os circuitos a partir de uma memória RAM interna do chip que contém o "circuito" que estão sintetizando. Ou seja, a cada vez que liga, essa memória tem que ser "carregada". Eles também têm uma memória não volátil onde você grava o circuito "permanente", ou seja, que vai carregar quando você ligar a máquina.

Os arquivos "compilados" (binários) tem dois formatos: .POF e .SOF. O POF grava na memória "permanente" e o "SOF" na temporária (RAM). Por isso que quando você rodou o Control Panel ele carregou o circuito que controlava a placa e quando desconectou e desligou esse circuito "se perdeu".

Como o circuito fica numa memória flash, carregar permanentemente é mais demorado e desgasta o chip. Por isso é legal testar com o SOF até chegar numa versão estável, e aí programar o POF.

Abra o Programmer. Com o Quartus instalado use o menu Tools/Programmer ou rode pelo menu do Windows se você instalou o Programmer por fora.

### Posição dos Switches

Segue a configuração dos switches que recomendo:

| Switch | Posição | Descrição                                    | Observações                                                                     |
|:------:|:-------:|:---------------------------------------------|:--------------------------------------------------------------------------------|
|   SW0  |   Off   | CPU Turbo Speed                              | Turbo em 1.78MHz (se on, pode dar problema com a memória nova/velha)            |
|   SW1  |   On    | MPI Switch                                   | Slot 4: Disk BASIC ROM. \*                                                      |
|   SW2  |   On    | MPI Switch (2)                               |                                                                                 |
|   SW3  |   Off   | Half Intensity on Duplicate Lines            | Questão de gosto. Pode mudar durante a execução.                                |
|   SW4  |   Off   | Disable Autostart Interrupt in slots 1 and 3 |                                                                                 |
|   SW5  |   On    | SG6 Enable                                   |                                                                                 |
|   SW6  |   Off   | SD Card Presence / Write Protect Override    |                                                                                 |
|   SW7  |   Off   | DriveWire Speed                              | 115200 bauds                                                                    |
|   SW8  |   Off   | DriveWire Speed (2)                          |                                                                                 |
|   SW9  |   Off   | UART Switch                                  | O DB-9 da placa é o Drivewire. Se colocar pra on o Drivewire para de funcionar. |

\* Se for 0ff/Off ele roda o Orquestra90CC, pode ser on no Sw1 e Off no Sw 2 (Slot 2). O Slot 3 não funciona por default.

## Instalando o SOF (temporário)

* Com a placa ligada, clique no Hardware Setup (botão superior a esquerda) e Escolha USB Blaster (USB-0).
* No Mode, use JTAG
* A placa DE-1 deve estar no modo Run.

* Se tiver algum arquivo configurado, delete o arquivo (clique no nome do arquivo e no X vermelho Delete).
* Clique em "Add file ..." e escolha o SOF correspondente (Se a sua placa for a memória nova, tente o CoCo3FPGA_4.1_Extra_Builds\Ed_or_No\coco3fpga_dw.sof)
* Garanta que o Program/Configure está marcado (normalmente está)
* Clique start

Se os displays de 7 segmentos mostratem CoCo, funcionou.

A versão 4.1 quando boota aparece direto no Easter Egg (Los 3 Amigos, do CoCo3 que). Pressione Ctrl-Alt-Del pra dar o boot. O Key3 é um reset, mas não funciona pra sair da tela dos 3 amigos.
você pode voltar a essa tela deixando o Key0 pressionado e clicando o RESET (KEY3 ou CTRL-ALT-DEL).

Tudo dando certo, você pode gravar o POF (permanente).

## Instalando o POF ("permanente")

* Semelhante ao SOF, porém.
* Apague o arquivo anterior (deixe vazio)
* Mode: Active Serial Programming
* Add File: (POF)
* Marque o Program/Configure
* Pode marcar o verify, se quiser
* Coloque o switch PROG/RUN em modo PROG
* Desligue e ligue a placa (os leds vão dicar todos acesos fracos e o display vai ficar vazio)
* Clique em Start
* Espere terminar (100 % Successful na barrinha de progresso no topo a direita)
* Volte a chave para o modo Run
* Desligue a DE-1 no botão e ligue novamente

## Colocar o Drivewire pra funcionar (aí teríamos algo minimamente funcional)
Ligue o cabo da RS232 no adaptador pra USB e ligue o USB no PC.
Rode o pyDrivewire apontando pra porta serial que foi criada (/dev/ttyUSB0 no RPi)

Garanta que um disco está no drive 0.
Basta ligar e dar um DIR que já carrega.

# Bizus

## Debugging

Antes de carregar a DE1_Flash.bin, o sistema não sobe (pudera, sem ROM, queria o que? :-) )
Você pode, depois de gravar a FLASH, pedir para o Control Panel pra ler para um arquivo (do mesmo tamanho do original) e comparar os arquivos, para garantir que está tudo certo.

Quando carreguei a versão oficial do CoCo3FPGA (os as versões com placa analógica), ele não funciona. Os leds R6 e G2 ficam ligados direto e o G3 fica piscando. Aparentemente ele tenta acessar a memória extra e se congela. No vídeo fica a famosa tela verde, mas não aparecem os caracteres do Color Basic.

Na versão Ed_or_No o led R6 e o G0 acendem e roda bem. Começando pela tela dos 3 amigos, é claro.

### Sites

http://www.davebiz.com/wiki/CoCo3FPGA

### Para saber qual versão do CoCo3FPGA você tem rodando:
?HEX$(PEEK(&FFF0))
 41
Versão: 4.1
?HEX$(PEEK(&FFF1))
 1
$FFF1, bits 7-4 Super Major Revision
$FFF1, bit 3 Analog board, 0=Ed's, 1=Gary's
$FFF1, bits 2-0 Max Memory Size, either 1 or 5 Meg
	(ou seja 1MB, Ed Board)

