# MINECRAFT SERVER SETUP WITH GOOGLE CLOUD

## Intro

Usar o período de 90 dias de teste do Google Cloud provavelmente será mais do que suficiente para sanar sua febre de algumas semanas no minecraft com os amigos. Por isso, que tal criar um servidor dedicado, sem intermediários, no qual você tem total controle e sem necessitar pagar por um domínio?

Simples, crie um gmail e acesse o google cloud para ter 90 dias gratuitos de uso, configurando um servidor em menos de 10 minutos.

O tutorial tentará te guiar passo a passo na criação do servidor.

## 1. Criação da VM

- Abrir google cloud
- Ir em VM Machines
- Criar VM n2-standard-2 (2vCPUs, 8 GB Memória) Intel Cascade Lake

## 2. Criação da regra de firewall

- Na barra de pesquisa digite firewall e selecione o primeiro resultado;
- Abaixo da barra de pesquisa clique no botão de "Create firewall policy";
- No passo 1, coloque um nome para sua regra, e deixe o escopo como "Global", e clique continue;
- No passo 2, clique no botão de "Create firewall rule". Uma aba irá se abrir, e coloque as configurações:
    1. Priority: 0
	2. Descrição: [o que quiser]
	3. Direction of Traffic: Ingress
	4. Action on match: Allow
	5. Logs: Off
	6. Target: Apply to all
	7. Source: All network types

- Então em source filters, logo abaixo:
	- IP ranges: 0.0.0.0/0

- Os próximos campos podem ser deixados vazios, até chegar a parte de "Protocols and ports":
	1. Selecione "Specified protocols and ports";
	2. Selecione a caixa de TCP e UDP;
	3. No campo abaixo de ambos coloque a porta que será aberta para os jogadores conectarem em seu servidor;

> Em relação a porta, o padrão do minecraft é 25565. Porém caso necessário como uma medida de segurança, principalmente se for rodar o servidor em modo offline (para clientes piratas), é interessante mudar essa porta visto que muitos hackers ativamente procuram por servidores abertos nessa porta.

- Criado a regra, adicione ela selecionando na caixa do lado esquerdo da regra;
- Vá até o fim da pagina, clique em continue;
- Para o passo 3, adicione uma associação com default e clique em create;

## 3. Acesso a VM e download do JDK

- pesquise por VM Instances na barra de pesquisa e clique no primeiro resultado;
- no canto esquerdo clicar na opção VM instances;
- clicar no botão SSH na linha da VM criada;
- O google irá pedir autorização de sua conta para acessar, apenas autorize clicando no botão de "Authorize" e espere o terminal aparecer;

- Copie e cole o seguinte comando:

`` sh
curl https://download.oracle.com/java/24/latest/jdk-24_linux-x64_bin.tar.gz --output jdk-24.tar.gz && tar -xvzf jdk-24.tar.gz && cd /usr/local/bin && sudo cp -s ~/jdk-24.0.1/bin/java ./java && cd ~ && export PATH=$PATH:/usr/local/bin/java
``

- Ao final, cheque se o comando roda e se a versão está correta com o comando: java -version

## 4. Criação do servidor:

- Qualquer coisa entre [] pode colocar o nome que quiser, normalmente o nome do seu servidor

`` sh
mkdir [nome-da-pasta] && curl https://piston-data.mojang.com/v1/objects/e6ec2f64e6080b9b5d9b471b291c33cc7f509733/server.jar --output server.jar && java -jar server.jar
``

- Deixe o server rodar para criar os arquivos iniciais;
- O server irá fechar automaticamente pois não aceitamos a eula;

- Após rodar, digite o seguinte comando: 
- vim eula.txt

- clicando a tecla i, é possível editar o arquivo
- delete o eula=false e troque por eula=true
- aperte ESC para sair do modo de edição e digite :x para sair e salvar do VIM
- após isso, rode novamente o servidor com java -jar server.jar
- quando receber a mensagem Done! e parar de receber texto no console, digite: stop; e dê enter

## 5. Configuração do servidor:

- Para melhor desempenho do servidor criaremos um arquivo shell para rodar automaticamente o server sem necessitar colocar o comando java

- touch run.sh
- chmod +x run.sh
- vim run.sh

Ao entrar no vim dentro do arquivo, pressione i para entrar em modo edição e cole o seguinte comando:
- java -server -Xms512M -Xmx2048M -XX:+UseG1GC -jar server.jar

Principais pontos para atenção:
- -Xms se refere a quantidade mínima de memória alocada para o servidor, normalmente pode se deixar como está, a não ser que tenha muito lag na hora de iniciar o server;
- -Xmx se refere a memória máxima alocada para o servidor, importante ressaltar que quanto mais memória alocada, mais lixo o servidor irá gerar, o que pode causar lag. Porém também uma quantidade baixa de memória, principalmente com muitos jogadores, também causa lag. Um meio termo no caso é a solução melhor;

Após configurar o comando, pressione ESC para sair do modo de edição e digite :x e ENTER;

Finalmente, vamos configurar as propriedades do servidor, usando o comando:
- vim server.properties

Edite o documento e mude as configurações como quiser, apenas levando em conta o view-distance que se for muito alto pode causar lag,
mas não é recomendado deixar ele abaixo de 10, pois existem bugs de não spawnar mobs com view distance muito baixo.

Após as configurações, está tudo pronto para rodar o servidor de minecraft a vontade!

## 6. Toques finais:

Porém, ainda há um problema, se rodar o servidor com ./run.sh e fechar o console, após alguns minutos o servidor irá desligar.
Como a sessão do terminal que estávamos usando foi finalizada, o servidor acaba fechando, então precisamos deixar ele rodando em segundo plano.

Para isso, vamos entrar em uma sessão que irá continuar aberta com o comando:

- screen

Após escrever isso e dar ENTER, vamos entrar em "outro" terminal, um que irá persistir mesmo após fecharmos.
Vocẽ pode então rodar ./run.sh lá e fechar a vontade o console do google.

Apenas se lembre, se tiver que desligar o servidor por algum motivo, terá de abrir terminal com conexão SSH e digitar o comando:
- screen -x

Para resumir sua sessão no terminal onde o servidor está rodando.
