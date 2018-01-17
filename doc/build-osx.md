Notas e instruções de compilação no Mac OS X
====================================
Os comandos neste guia devem ser executados em um aplicativo do terminal.
O nativo está localizado em `/Applications/Utilities/Terminal.app`.

Preparação
-----------
Instale as ferramentas da linha de comando do OS X:

`xcode-select --install`

Quando aparecer o popup, clique `Install`.

Então instale [Homebrew](http://brew.sh).

Dependências
----------------------

    brew install automake berkeley-db4 libtool boost --c++11 miniupnpc openssl pkg-config protobuf --c++11 qt5 libevent

No caso de você querer montar a imagem do disco com `make deploy` (.dmg / opcional), você precisa do RSVG

    brew install librsvg

NOTA: Compilar com Qt4 ainda é suportado, entretanto, pode resultar em uma UI quebrada. Compilar com Qt5 é recomendado.

Compilar Criptoreal Core
------------------------

1. Faça uma cópia do código fonte do criptoreal source code e cd em `criptoreal`

        git clone https://github.com/criptoreal/criptoreal
        cd criptoreal

2.  Compilar criptoreal-core:

    Configure e compile os binários criptoreal assim como o GUI (se Qt for encontrado).

   Você pode desabilitar GUI build passando `--without-gui` para configurar.

        ./autogen.sh
        ./configure
        make

3.  É recomendado a compilação e a execução das unidades de teste:

        make check

4.  Você também pode criar um .dmg que contenha o pacote .app (opcional):

        make deploy

Execução
-------

Criptoreal Core agora está disponível em `./src/criptoreald`

Antes de executá-lo, é recomendado criar um arquivo de configuração RPC.

    echo -e "rpcuser=criptorealrpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/Criptoreal/criptoreal.conf"

    chmod 600 "/Users/${USER}/Library/Application Support/Criptoreal/criptoreal.conf"

A primeira vez que você executar o criptoreald, ele dará início ao download da blockchain. Este processo pode levar várias horas.

Você pode monitorar o processo de download verificando o arquivo debug.log:

    tail -f $HOME/Library/Application\ Support/Criptoreal/debug.log

Outros comandos:
-------

    ./src/criptoreald -daemon # Starts the criptoreal daemon.
    ./src/criptoreal-cli --help # Outputs a list of command-line options.
    ./src/criptoreal-cli help # Outputs a list of RPC commands when the daemon is running.

Usando o Qt Creator como IDE
------------------------
Você pode usar o Qt Creator como um IDE, para desenvolvimento de criptoreal development.
Faça o download e instale a edição do [Qt Creator](https://www.qt.io/download/).
Desmarque todas as opções exceto Qt Creator durante o processo de instalação.

1. Tenha certeza de que você instalou tudo através do Homebrew mencionado acima
2. Execute um ./configure --enable-debug
3. No Qt Creator escolha "Novo Projeto" -> Importar Projeto -> Importar Projeto Existente
4. Digite "criptoreal-qt" como nome do projeto, informe src/qt como local
5. Deixe a seleção do arquivo como está
6. Confirme a "página de resumo"
7. Na aba "Projetos" selecione "Gerenciar Kits..."
8. Escolha o kit "Desktop" padrão e selecione "Clang (x86 64bit in /usr/bin)" como compilador
9. Selecione LLDB como depurador (talvez seja necessário configurar o caminho para sua instalação)
10. Comece a depurar com o Qt Creator

Notas
-----

* Testados nas versões OS X 10.8 até 10.12 de 64-bit somente processadores Intel.

* Não há suporte para a compilação de binários Qt que foram baixados. Veja as notas em [#7714](https://github.com/bitcoin/bitcoin/issues/7714)
