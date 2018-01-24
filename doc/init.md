Exemplos de script de inicialização e configuração de serviço para bitcoind
==========================================================

Scripts de exemplo e arquivos de configuração para systemd, Upstart e OpenRC podem ser encontrados na pasta contrib/init.

    contrib/init/bitcoind.service:    unidade de configuração de serviço systemd
    contrib/init/bitcoind.openrc:     Script de inicialização OpenRC compatível com SysV
    contrib/init/bitcoind.openrcconf: arquivo OpenRC conf.d 
    contrib/init/bitcoind.conf:       Arquivo de configuração do serviço de inicialização
    contrib/init/bitcoind.init:       Script de inicialização SysV compatível com CentOS
    
1. Usuário de serviço
---------------------------------

Todas as três configurações de inicialização do Linux assumem a existência de um usuário e um grupo "bitcoin". Eles devem ser criados antes de tentar usar estes scripts. A configuração do OS X assume que o bitcoind será configurado para o usuário atual.

2. Configuração
---------------------------------

Em um mínimo, o bitcoind exige que a configuração rpcpassword seja definida quando executada como um daemon. Se o arquivo de configuração não existir ou esta configuração não estiver definida, o bitcoind será desligado imediatamente após a inicialização.

Esta senha não precisa ser lembrada ou digitada, pois é usada principalmente como um token fixo onde o bitcoind e os programas são lidos a partir do arquivo de configuração, entretanto é recomendado qeu uma senha forte e segura seja usada, pois ela é de segurança crítica para garantir a carteira caso esteja habilitada.

Se bitcoind for executado com o sinalizador "-server" (definido por padrão) e nenhuma rpcpassword está configurada, ele usará um arquivo de cookie especial para autenticação. O cookie é gerado com conteúdo aleatório quando o daemon é iniciado e excluído quando terminado. O acesso de leitura a este arquivo controla quem pode acessá-lo através do RPC.

Por padrão, o cookie é armazenado no diretório de dados, mas sua localização pode ser substituída pela opção '-rpccookiefile'.

Isto permite a execução do bitcoind sem ter que fazer qualquer configuração manual.

`conf`, `pid`, e `wallet` aceitam caminhos relativos que são interpretados como relativos ao diretório de dados. `wallet` *somente* suporta caminhos relativos.

Para um exemplo de arquivo de configuração que descreve as configurações, consulte `contrib/debian/examples/bitcoin.conf`.

3. Caminhos
---------------------------------

3a) Linux

Todas as três configurações assumem vários caminhos que talvez precisem ser ajustados.

Binários              `/usr/bin/bitcoind`  
Arqivo de configuração:  `/etc/bitcoin/bitcoin.conf`  
Diretório de dados:      `/var/lib/bitcoind`  
Arquivo PID:            `/var/run/bitcoind/bitcoind.pid` (OpenRC e Upstart) ou `/var/lib/bitcoind/bitcoind.pid` (systemd)  
Arquivo Lock:           `/var/lock/subsys/bitcoind` (CentOS)  

O arquivo de configuração, o diretório PID (se aplicável) e o diretório de dados devem ser todos pertencentes ao usuario e ao grupo bitcoin. É aconselhável por motivos de segurança tornar o arquivo de configuração e o diretório de dados legíveis apenas para o usuário e grupo bitcoin.  O acesso ao bitcoin-cli e outros clientes rpc bitcoind podem ser controlados pela associação de grupo.

3b) Mac OS X

Binário:              `/usr/local/bin/bitcoind`  
Arquivo de Configuração:  `~/Library/Application Support/Bitcoin/bitcoin.conf`  
Diretório de dados:      `~/Library/Application Support/Bitcoin`
Arquivo Lock:           `~/Library/Application Support/Bitcoin/.lock`

4. Instalando a configuração do serviço
---------------------------------------

4a) systemd

A instalação deste arquivo .service consiste apenas em copiá-lo para /usr/lib/systemd/diretório do sistema, seguido pelo comando `systemctl daemon-reload` para atualizar a execução da configuração systemd.

Para testar, execute `systemctl start bitcoind` e para habilitar para inicialização execute `systemctl enable bitcoind`

4b) OpenRC

Renomeie bitcoind.openrc para bitcoind e solte-o em /etc/init.d. Verifique a propriedade e as permissões e torne-o executável.  Teste com `/etc/init.d/bitcoind start` e configure para executar na inicialização com `rc-update add bitcoind`

4c) Upstart (para distribuições baseadas em Debian/Ubuntu)

Coloque bitcoind.conf em /etc/init. Teste executando `service bitcoind start`. Ele será executado automaticamente na inicialização.

NOTA: Este script é incompatível com o CentOS 5 e o Amazon Linux 2014, pois usam versões antigas do Upstart e não fornecem o utilitário start-stop-daemon.

4d) CentOS

Copie bitcoind.init para /etc/init.d/bitcoind. Teste executando `service bitcoind start`.

Usando este script, você pode ajustar o caminho e os sinalizadores para o programa bitcoind definindo as variáveis de ambiente BITCOIND e FLAGS no arquivo /etc/sysconfig/bitcoind. Você tambem pode usar a variável de ambiente DAEMONOPTS aqui.

4e) Mac OS X

Copie org.bitcoin.bitcoind.plist para ~/Library/LaunchAgents. Carregue o agente executando `launchctl load ~/Library/LaunchAgents/org.bitcoin.bitcoind.plist`.

Este agente fará com que o bitcoind inicie assim que o usuário efetuar o login.

NOTA: Esta abordagem destina-se a quem desejar executar o bitcoind como usuário atual. Você precisará modificar org.bitcoin.bitcoind.plist  se você pretende usá-lo como Launch Daemon com um usuário bitcoin dedicado.

5. Auto-respawn
-----------------------------------

Auto respawning atualmente só está configurado para Upstart e systemd. Padrões razoáveis foram escolhidos, mas YMMV.
