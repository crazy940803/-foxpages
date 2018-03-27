# Fox Pages Server 3.0

## O que � isto?

O Fox Pages Server (FPS) � um servidor HTTP, HTTPS e FastCGI multithread para Visual FoxPro.

Com o Fox Pages Server � poss�vel desenvolver, depurar e distribuir conte�do e aplica��es para web utilizando o Visual FoxPro.

O Fox Pages Server n�o possibilita que c�digo Visual FoxPro seja executado na internet. Por isso � necess�rio conhecimento das liguagens e ferramentas de desenvolvimento para internet que ser�o utilizadas, por exemplo: HTML, CSS, Javascript, JQuery, Dojo etc.

## Requisitos
Microsoft Visual FoxPro 9.0

## Distribui��o
O Fox Pages Server � distribu�do em dois modos: modo de desenvolvimento e modo de distribui��o.

### Modo de desenvolvimento
Neste modo, o servidor funciona por padr�o em singlethread, para que seja poss�vel a utiliza��o do Visual FoxPro para o desenvolvimento de p�ginas FXP. O modo de desenvolvimento requer o Visual FoxPro instalado.

### Modo de distribui��o
Neste modo, o servidor funciona em multithread, proporcionando um ganho extremo de processamento. O modo de distribui��o requer o runtime do Visual FoxPro instalado.

Nos modos de desenvolvimento e distribui��o qualquer erro � registrado em tabelas na pasta LOGS permitindo a detec��o e rastreamento de erros.

## Antes de instalar
N�o tente fazer o procedimento de instala��o em modo de desenvolvimento e distribui��o no mesmo computador. Se isto for necess�rio, execute o arquivo install.bat do modo de distribui��o, pois ele registrar� os componentes tamb�m criar� o Servi�o do Windows. Configure o modo de desenvolvimento para utilizar um IP ou porta diferente do modo de distribui��o.

### Instala��o em modo de desenvolvimento
1) Localize a pasta Development.
2) Execute o arquivo install.bat. (Para instalar no Windows 10 execute usando Prompt de comando como administrador)
3) Execute o programa do protocolo desejado, localizado na pastas SERVERS, HTTP.FXP � o padr�o.

O debuguer do Visual FoxPro s� funcionar� no modo de desenvolvimento, pois � imposs�vel a exibi��o de qualquer interface quando o c�digo � executado em uma DLL multithread. Qualquer tentativa gerarm erros ou congelamento da thread.

### Instala��o em modo de distribui��o
1) Localize a pasta Distribuition.
2) Execute o arquivo install.bat. (Para instalar no Windows 10 execute usando Prompt de comando como administrador)

### Dica de seguran�a
N�o � recomendado deixar os arquivos .HTML com os seus compilados .FXP na mesma pasta do servidor, eles podem ser baixados se a extens�o for trocada no webbrowser.

## Antes de iniciar
O Fox Pages usa a porta 80 como padr�o para HTTP, portanto antes de iniciar � necess�rio parar qualquer servi�o que esteja usando a porta 80 (IIS, Apache, etc) ou alterar a porta usada no programa HTTP.PRG localizado na pasta SERVERS no o modo desenvolvimento, ou no campo PORT da tabela SERVERS.DBF localizado na pasta DATA no modo de distribui��o.

Outros protocolos seguem o mesmo procedimento.

## Configura��o de servidores HTTP e HTTPS

O banco de dados FPS.DBC localizado na pasta DATA armazena a configura��o dos servidores.
A documenta��o das tabelas e seus respectivos campos podem ser encontrados no arquivo FPS.HTML.
O relacionamento entre as tabelas podem ser visualizados na imagem FPS.JPG.

### Servidores
O servidores s�o respons�veis pelas conex�es dos clientes (IE, Chrome, Firefox, etc) e servidores (NGinX, etc).

Cada servidor � executado em uma thread separada e podem, dependendo da configura��o do n�mero IP, escutar uma mesma porta. Em caso de conflitos de n�meros IP e portas o primeiro servidor configurado receber� as conex�es.

Configure os servidores adicionando, modificando ou excluindo registros na tabela SERVERS.DBF.

Cada protocolo tem como padr�o uma porta espec�fica:

- HTTP deve ser configurado para porta 80.
- HTTPS deve ser configurado para porta 443.

FastCGI � normalmente utilizado na comunica��o entre servidores, n�o h� uma porta padr�o.

### Sites
Os sites estabelecem uma rela��o entre um HOSTNAME (e.g. www.example.com) com a pasta onde os arquivos do site est�o localizados (e.g. c:\sites\example), e configura a sua p�gina inicial (e.g. index.fxp, index.php, index.html, etc).

Configure os sites adicionando, modificando ou excluindo registros na tabela SITES.DBF.

Caso o campo HOSTNAME seja preenchido com "*" todos os HOSTNAMES ser�o relacionados com uma mesma pasta.

Nesta mesma tabela configuramos redirecionamentos preenchendo o campo REDIRECT com o endere�o completo do redirecionamento. Este recurso � muito util quando precisamos, por exemplo, redirecionar conex�es n�o seguras (HTTP) para um servidor seguro (HTTPS), isto � feito por exemplo, preenchendo o campo REDIRECT do site www.example.com do servidor n�o seguro (HTTP) com "https://www.example.com", o endere�o do site seguro (HTTPS).

### Gateways
Gateways s�o utilizados para enviar requisi��es para outras ferramentas de desenvolvimento. O PHP foi a �nica testada at� o momento, entretando qualquer ferramenta que suporte FastCGI deve ser compat�vel.

Configure os gateways adicionando, modificando ou excluindo registros na tabela GATEWAYS.DBF.

O �nico protocolo suportado � FastCGI.

Gateways funcionam de uma forma semelhante aos Sites, estabelecendo uma rela��o entre um HOSTNAME (e.g. www.example.com) com a pasta onde os arquivos do site est�o localizados (e.g. c:\sites\example). A diferen�a est� no fato de que o conte�do do campo URI (e.g. ".php") deve estar contido na URI da requisi��o para que a mesma seja enviada ao gateway.

Atendendo estes crit�rios, o Fox Pages Server transforma a requisi��o HTTP em uma requisi��o FastCGI e a enviada ao servidor configurado. A resposta FastCGI ent�o � transformada em uma resposta HTTP e enviada ao cliente.

Requisi��es que n�o atendem os crit�rios, ser�o processadas pelo servidor HTTP, portanto para cada Gateway um Site deve ser configurado.

### Seguran�a
Nem todas a pastas e arquivos contidos em um site devem estar acess�veis. Bancos de dados, tabelas e programas s�o alguns exemplos.

O Fox Pages Server possui o sistema de controle de acesso que permite o acesso autorizado ou o bloqueio completo a pastas do site.

O controle de acesso � configurado adicionando, modificando ou excluindo registros das tabelas REALMS.DBF, USERS.DBF e REALMUSER.DBF

A tabela REALM.DBF define as configura��es de acesso as pastas do site.

A tabela USERS.DBF define os usu�rios que ter�o acesso as pastas.

A table REALMUSER.DBF relaciona os usu�rios com as pastas.

## Configura��o de servidores FastCGI

O Fox Pages Server pode ser configurado para ser utilizado atrav�s de outro servidores web utilizando o protocolo FastCGI.

O arquivo nginx.conf localizado na pasta NGINX � um modelo de configura��o para o servidor NGinX. Copie este arquivo para a pasta CONF onde o NGinX est� instalado e configure o parametro ROOT com o caminho completo da pasta dos arquivos do site.

Para configurar o Fox Pages Server para utilizar o protocolo FastCGI preencha o campo TYPE da tabela SERVERS.DBF com "FCGI".

Como todas as informa��es necess�rias para o processamento da requisi��o devem ser fornecidas pelo servidor web, n�o h� necessidade de configura��o de sites, gateways ou seguran�a.

## Acessando a primeira vez
Ap�s a inicializa��o do servidor utilize qualquer browser digitando no endere�o do servidor configurado (e.g. http://localhost, https://localhost).

## Site de demonstra��o
Para entrar no site de demonstra��o existem duas contas, uma para o cliente e outra para o representante.

A conta do cliente da acesso a area do cliente. Para acessar utilize o email cliente@teste.com.br e a senha 123456.

A conta do representante inicia uma aplica��o para cadastro de clientes e pedidos. Para acessar utilize o email representante@teste.com.br e a senha 123456.

## Como desenvolver usando o Fox Pages Server

### P�ginas din�mincas
Uma p�gina din�mica utiliza uma linguagem de programa��o server-side no desenvolvimento de um site ou aplica��o para internet.

O Fox Pages Server torna poss�vel o desenvolvimento destas p�ginas utilizando os recursos de desenvolvimentos de pag�nas est�ticas (e.g. HTML, CSS, Javascript) com os recursos de programa��o do Visual Fox Pro (e.g. liguagem de programa��o, banco de dados).

No Fox Pages Server uma pagina HTML � convertida em um arquivo de programa PRG e compilada para um arquivo compilado FXP, assim o processamento das paginas � extremamente r�pido e n�o tem as limita��es do uso de outro interpretador.

No processo de compila��o apenas o c�digo entre as tag `<FPS>` e `</FPS>` ser�o processados, o restante � enviado como conte�do est�tico.

Um exemplo de conte�do est�tico.
```
<HTML>
Ol� mundo
</HTML>
```
Resultado:

Ol� mundo  

Um exemplo de um programa como conte�do est�tico por falta das tags `<FPS>` e `</FPS>`.
```
<HTML>
for nCounter = 1 to 3
   Ol� mundo
next
</HTML>
```
Resultado:

for lnCounter = 1 to 3  
   Ol� mundo  
next  

Um exemplo usando as tags `<t>` e `<e>`, elas s�o repons�veis pelo envio de textos est�ticos e express�es.
```
<HTML>
   <FPS>
      cMundo = "Mundo"
      for nCounter = 1 to 3
         <t>Ol� </t><e>cMundo</e><br>
      next
   </FPS>
</HTML>
```
Resultado:

Ol� mundo  
Ol� mundo  
Ol� mundo  

Um exemplo usando outras tags HTML combinadas a programa��o.
Toda linha iniciada com uma tag HTML ou pela tag `<t>` � enviada.
```
<HTML>
   <FPS>
      <b>Come�o</b><br><br>

      cWorld = "Mundo"
      for nCounter = 1 to 3
         <b><t>Ol� </t><e>cWorld</e></b><br>
      next

	<br>
	<t>Fim</t>
   </FPS>
</HTML>
```
Resultado:

Come�o  

Ola mundo  
Ola mundo  
Ola mundo  

Fim  

## Aplica��es RESTfull
REST (Representational State Transfer) � um estilo arquitet�nico que defende que os aplicativos da Web devem usar o HTTP como era originalmente previsto, onde as requisi��es GET, PUT, POST e DELETE devem ser usados para consulta, altera��o, cria��o e exclus�o, respectivamente.

O Fox Pages Server processa uma requisi��o como REST sempre que o header Accept for "application/json".

Mais detalhes podem ser encontrados no aplicativo dispon�vel no site de demonstra��o ao entrar com a conta do representante.

## Incompatibilidade com a vers�o 2.0
Para o suporte ao protocolo FastCGI o processamento das propriedades dos objetos Request e Response foram alteradas.

Na vers�o 2.0 headers com hifen (e.g. Accept-Encoding) tinham o hifen removido (e.g. AcceptEnconding). Na vers�o 3.0 estes hifens s�o alterados para o sublinhado (e.g. Accept_Encoding).

## Licenciamento
O Fox Pages Server � um software livre e de c�digo aberto. A licen�a esta localizada no arquivo LICENSE.

O componente usado para as conex�es � o Socketwrench da empresa Catalyst Development Corporation (www.sockettools.com).

Este componente � distribu�do nas vers�es gratuita e comercial. A vers�o gratuita n�o tem suporte para conex�es seguras (SSL/TLS).

A vers�o de desenvolvimento do Fox Pages Server est� configurada para utilizar a vers�o gratuita do SocketWrench. O que ser� uma limita��o somente se o uso de conex�es seguras no ambiente de desenvolvimento for necess�rio.

Para usar a vers�o comercial do SocketWrench � necess�rio comprar uma licen�a, pois o Fox Pages Server n�o inclui essa licen�a.

A configura��o vers�o utilizada, gratuita ou comercial, ou a vers�o do SocketWrench, est� localizada no arquivo FOXPAGES.H da pasta CORE, como segue abaixo:

//SOCKETWRENCH  
#DEFINE USEFREEVERSION  
#DEFINE CSWSOCK_CONTROL		"SocketTools.SocketWrench.6"  

//SocketWrench 8  
//#DEFINE CSWSOCK_CONTROL		"SocketTools.SocketWrench.8"  
//#DEFINE CSWSOCK_LICENSE_KEY	"INSERT YOUR RUNTIME LICENSE HERE"  

//SocketWrench 9  
//#DEFINE CSWSOCK_CONTROL		"SocketTools.SocketWrench.9"  
//#DEFINE CSWSOCK_LICENSE_KEY	"INSERT YOUR RUNTIME LICENSE HERE"  

� necess�rio recompilar o projeto ap�s alterar estas configura��es.

## Cr�ditos

Multithreading - VFP2C32T.FLL - Christian Ehlscheid  
Compacta��o - VFPCompression - Craig Boyd  
Encripta��o - VFPEncryption - Craig Boyd  
JSON Parser - Vers�o modificada da biblioteca - Craig Boyd  
Sockets - Socketwrench - Catalyst Development  

## Doa��o
Se este projeto � �til para voc�, cosidere uma doa��o.

[![paypal](https://www.paypalobjects.com/pt_BR/BR/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=PLGPFM9SF2X8G)