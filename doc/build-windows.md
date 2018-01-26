NOTAS DE COMPILAÇÃO DO WINDOWS
====================

Abaixo estão algumas notas em como compilar Criptoreal no Windows.

A maioria dos desenvolvedores usam compilação cruzada do Ubuntu para compilar executáveis para o Windows. Isto também é usado para compilar os binários de lançamento.

Embora existam várias maneiras de compilar no Windows (por exemplo, usando msys / mingw-w64), usar o subsistema do Windows para Linux é a forma mais direta. Se você está compilando com outro método, por favor, contribua aqui com instruções para outras pessoas que estão executando versões do Windows que não são compatíveis com o subsistema Windows para Linux. 

Compilando com o subsistema Windows para Linux
-------------------------------------------

Com o Windows 10, a Microsoft lançou um novo recurso chamado [Subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Este recurso permite que você execute um shell bash diretamente no Windows dentro de um ambiente baseado no Ubuntu. Dentro deste ambiente, você pode cruzar a compilação para o Windows sem a necessidade de uma VM rodando Linux ou um servidor.

Este recurso não é suportado nas versões anteriores ao Windows 10 ou em SKUs do Windows Server. Além disso, está disponível [somente para versões 64-bit do Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).

Para obter o shell bash, você deve ativar primeiramente este recurso no Windows.

1. Ative o modo desenvolvedor
  * Abrir Configurações -> Atualização e Segurança -> Para desenvolvedores
  * Selecione o botão de modo desenvolvedor
  * Reinicie se necessário
2. Habilite o recurso do subsistema de Windows para Linux
  * A partir do Iniciar, procure por "Ligar recursos do Windows" (type 'turn')
  * Selecione Subsistema do Windows para Linux (beta)
  * Clique em OK
  * Reinicie se necessário
3. Complete a instalação
  * Abra o prompt de comando (cmd) e digite "bash"
  * Aceite a licença de uso
  * Crie uma nova conta de usuário UNIX (esta é uma conta separada da sua conta Windows)

Após o shell bash estiver ativo, você pode seguir as instruções abaixo, começando com a seção "Compilação Cruzada". Compilar a versão de  64-bit é recomendada, mas é possível compilar também a versão de 32-bit.

Compilação Cruzada
-------------------

Estas etapas podem ser realizadas em, por exemplo, uma VM do Ubuntu. O sistema dependente também funcionará em outras distribuições Linux, entretanto, os comandos para instalar a cadeia de ferramentas serão diferentes.

Primeiramente, instale as dependências gerais:

    sudo apt-get install build-essential libtool autotools-dev automake pkg-config bsdmainutils curl

Uma cadeia de ferramentas padrão (`build-essential`) é necessária pois alguns pacotes de dependência (como o `protobuf`) precisa compilar os utilitários de host que são usados no processo de compilação.

## Compilando para o Windows 64-bit

Para compilar executáveis para o Windows 64-bit, instale as seguintes dependências:

    sudo apt-get install g++-mingw-w64-x86-64 mingw-w64-x86-64-dev

Em seguida compile usando:

    cd depends
    make HOST=x86_64-w64-mingw32
    cd ..
    ./autogen.sh # não é necessário quando compilando a partir do tarball
    CONFIG_SITE=$PWD/depends/x86_64-w64-mingw32/share/config.site ./configure --prefix=/
    make

## Compilando para o Windows 32-bit 

Para compilar executáveis para o Windows 32-bit, instale as seguintes dependências:

    sudo apt-get install g++-mingw-w64-i686 mingw-w64-i686-dev 

Em seguida compile usando:

    cd depends
    make HOST=i686-w64-mingw32
    cd ..
    ./autogen.sh # not required when building from tarball
    CONFIG_SITE=$PWD/depends/i686-w64-mingw32/share/config.site ./configure --prefix=/
    make

## Sistema de dependência

Para documentação sobre o sistema de dependência veja [README.md](../depends/README.md) no diretório depends .

Instalação
-------------

Após compilar usando o subsistema do Windows pode ser útil copiar as compilações executáveis para um diretório na mesma unidade do Windows, dentro da estrutura de diretorio que aparece na versão `.zip`. Isto só pode ser feito da seguinte forma: Será instalado em `c:\workspace\criptoreal`, por exemplo:

    make install DESTDIR=/mnt/c/workspace/criptoreal
