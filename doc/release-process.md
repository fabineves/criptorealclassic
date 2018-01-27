Processo de lançamento
======================

Antes do lançamento de qualquer versão:

* Atualizar traduções (ping wumpus no IRC) veja [translation_process.md](https://github.com/bitcoin/bitcoin/blob/master/doc/translation_process.md#synchronising-translations).

* Atualizar manpages, veja [gen-manpages.sh](https://github.com/criptoreal/criptoreal/blob/master/contrib/devtools/README.md#gen-manpagessh).

Antes de qualquer menor ou maior lançamento:

* Atualize [bips.md](bips.md) para contabilizar as alterações desde a última versão.
* Atualize a versão nos fontes (veja abaixo)
* Escreva as notas do lançamento da nova versão (veja abaixo)
* Atualize `src/chainparams.cpp` nMinimumChainWork com informações do rpc do getblockchaininfo.
* Atualize `src/chainparams.cpp` defaultAssumeValid  com informações do rpc do getblockhash.
  - O valor selecionado não deve ser órfão, então pode ser útil definir o valor de dois blocos de volta da ponta.
  - Testnet deve ser definido algumas dezenas de milhares de vezes devido as reorgs.
  - Esta atualização deve ser revisada com um reindex-chainstate com assumevalid=0 para capturar qualquer defeito que cause rejeição de blocos no hisórico passado.

Antes de cada lançamento maior:

* Atualize hardcoded [seeds](/contrib/seeds/README.md), veja [este pull request](https://github.com/bitcoin/bitcoin/pull/7415) para um exemplo.
* Atualize [`BLOCK_CHAIN_SIZE`](/src/qt/intro.cpp) para o tamanho atual.

### Primeira vez / Novos compiladores

Se você estiver usando o script automatizado (encontrado em [contrib/gitian-build.sh](/contrib/gitian-build.sh)), então neste ponto, você deve executá-lo com o comando "--setup". Do contrário, ignore isso.

Confira o código fonte na seguinte hierarquia de diretórios.

    cd /path/to/your/toplevel/build
    git clone https://github.com/CriptoReal/gitian.sigs.cripto.git
    git clone https://github.com/criptoreal/criptoreal-detached-sigs.git
    git clone https://github.com/devrandom/gitian-builder.git
    git clone https://github.com/criptoreal/criptoreal.git

### Mantenedores/engenheiros de lançamento do Criptoreal, atualizem a versão nos fontes

Atualizem o seguinte:

- `configure.ac`:
    - `_CLIENT_VERSION_MAJOR`
    - `_CLIENT_VERSION_MINOR`
    - `_CLIENT_VERSION_REVISION`
    - Não esqueça de configurar `_CLIENT_VERSION_IS_RELEASE` para `true`
- `src/clientversion.h`: (estes espelhos `configure.ac` - veja problema #3539)
    - `CLIENT_VERSION_MAJOR`
    - `CLIENT_VERSION_MINOR`
    - `CLIENT_VERSION_REVISION`
    - Não esqueça de configurar `CLIENT_VERSION_IS_RELEASE` para `true`
- `doc/README.md` e `doc/README_windows.txt`
- `doc/Doxyfile`: `PROJECT_NUMBER` contém a versão completa
- `contrib/gitian-descriptors/*.yml`: geralmente, deve fazer isso no mestre depois de ramificar o lançamento - mas certfique-se pelo menos, de fazê-lo antes da nova versão principal

Escreva as notas de lançamento. O git shortlog ajuda muito, por exemplo:

    git shortlog --no-merges v(versão atual, e.g. 0.7.2)..v(nova versão, e.g. 0.8.0)

(ou ping @wumpus no IRC, ele possui ferramentas específicas para gerar as listas de pulls combinadas e classificá-las em categorias com base em etiquetas)

Gerar lista de autores:

    git log --format='%aN' "$*" | sort -ui | sed -e 's/^/- /'

Coloque Tag na versão no git

    git tag -s v(new version, e.g. 0.8.0)

### Configurar e executar compilações do Gitian

Se você está usando o script automático (encontrado em [contrib/gitian-build.sh](/contrib/gitian-build.sh)), então, neste ponto, você deve executá-lo com o comando "--build". Caso contrário, ignore isto.

Configuração dos descritores Gitian:

    pushd ./criptoreal
    export SIGNER=(sua chave Gitian, ie bluematt, sipa, etc)
    export VERSION=(nova versão, e.g. 0.8.0)
    git fetch
    git checkout v${VERSION}
    popd

Certifique-se de que o seu gitian.sigs.cripto esteja atualizado se deseja verificar suas compilações contra outras assinaturas do Gitian.

    pushd ./gitian.sigs.cripto
    git pull
    popd

Certifique-se que o gitian-builder esteja atualizado:

    pushd ./gitian-builder
    git pull
    popd

### Obtenha e crie entradas: (primeira vez ou quando as versões de dependência mudam)

    pushd ./gitian-builder
    mkdir -p inputs
    wget -P inputs https://bitcoincore.org/cfields/osslsigncode-Backports-to-1.7.1.patch
    wget -P inputs http://downloads.sourceforge.net/project/osslsigncode/osslsigncode/osslsigncode-1.7.1.tar.gz
    popd

Crie o tarball do OS X SDK, veja [OS X readme](README_osx.md) para detalhes, e copie para os diretórios de entrada.

### Opcional: Sinalize o cache das fontes Gitian e os repositórios git offline

Por padrão, o Gitian irá buscar arquivos de origem conforme necessário. Para armazená-los antes do tempo:

    pushd ./gitian-builder
    make -C ../criptoreal/depends download SOURCES_PATH=`pwd`/cache/common
    popd

Somente os arquivos ausentes serão obtidos, então isto é seguro para re-executar para cada compilação.

NOTA: Compilações offline devem usar o sinalizador --url para garantir que o Gitian seja recuperado apenas de URLs locais. Por exemplo:

    pushd ./gitian-builder
    ./bin/gbuild --url criptoreal=/path/to/criptoreal,signature=/path/to/sigs {rest of arguments}
    popd

As invocações gbuild abaixo <b>NÃO FAZEM ISTO</b> por padrão.

### Compilar e assinar o Criptoreal Core para Linux, Windows, e OS X:

    pushd ./gitian-builder
    ./bin/gbuild --memory 3000 --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
    ./bin/gsign --signer $SIGNER --release ${VERSION}-linux --destination ../gitian.sigs.cripto/ ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
    mv build/out/criptoreal-*.tar.gz build/out/src/criptoreal-*.tar.gz ../

    ./bin/gbuild --memory 3000 --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
    ./bin/gsign --signer $SIGNER --release ${VERSION}-win-unsigned --destination ../gitian.sigs.cripto/ ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
    mv build/out/criptoreal-*-win-unsigned.tar.gz inputs/criptoreal-win-unsigned.tar.gz
    mv build/out/criptoreal-*.zip build/out/criptoreal-*.exe ../

    ./bin/gbuild --memory 3000 --commit criptoreal=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml
    ./bin/gsign --signer $SIGNER --release ${VERSION}-osx-unsigned --destination ../gitian.sigs.cripto/ ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml
    mv build/out/criptoreal-*-osx-unsigned.tar.gz inputs/criptoreal-osx-unsigned.tar.gz
    mv build/out/criptoreal-*.tar.gz build/out/criptoreal-*.dmg ../
    popd

Compilação de saída esperada:

  1. fonte tarball (`criptoreal-${VERSION}.tar.gz`)
  2. linux 32-bit e 64-bit dist tarballs (`criptoreal-${VERSION}-linux[32|64].tar.gz`)
  3. windows 32-bit e 64-bit instaladores não assinados e dist zips (`criptoreal-${VERSION}-win[32|64]-setup-unsigned.exe`, `criptoreal-${VERSION}-win[32|64].zip`)
  4. Instalador não assinado do OS X e dist tarball (`criptoreal-${VERSION}-osx-unsigned.dmg`, `criptoreal-${VERSION}-osx64.tar.gz`)
  5. Assinaturas Gitian (em `gitian.sigs.cripto/${VERSION}-<linux|{win,osx}-unsigned>/(your Gitian key)/`)

### Verifique outras assinaturas dos compiladores gitian para o seu próprio. (Opcional)

Adicione outras chaves de compiladores gitian ao seu chaveiro gpg keyring, e/ou atualize as chaves.

    gpg --import criptoreal/contrib/gitian-keys/*.pgp
    gpg --refresh-keys

Verifique as assinaturas

    pushd ./gitian-builder
    ./bin/gverify -v -d ../gitian.sigs.cripto/ -r ${VERSION}-linux ../criptoreal/contrib/gitian-descriptors/gitian-linux.yml
    ./bin/gverify -v -d ../gitian.sigs.cripto/ -r ${VERSION}-win-unsigned ../criptoreal/contrib/gitian-descriptors/gitian-win.yml
    ./bin/gverify -v -d ../gitian.sigs.cripto/ -r ${VERSION}-osx-unsigned ../criptoreal/contrib/gitian-descriptors/gitian-osx.yml
    popd

### Próximos passos:

Confirme sua assinatura ao gitian.sigs.cripto:

    pushd gitian.sigs.cripto
    git add ${VERSION}-linux/${SIGNER}
    git add ${VERSION}-win-unsigned/${SIGNER}
    git add ${VERSION}-osx-unsigned/${SIGNER}
    git commit -a
    git push  # 
    popd

Aguarde assinaturas independentes do Windows/OS X:

- Uma vez que o Windows/OS X compila, cada um tem 3 assinaturas correspondentes, eles serão assinados com as respectivas chaves de lançamento.
- As assinaturas separadas serão comprometidas com o repositório [criptoreal-detached-sigs](https://github.com/criptoreal/criptoreal-detached-sigs), que pode ser combinado com os aplicativos não assinados para criar binários assinados.

Criar (e verificar opcionalmente) o binário OS X assinado:

    pushd ./gitian-builder
    ./bin/gbuild -i --commit signature=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
    ./bin/gsign --signer $SIGNER --release ${VERSION}-osx-signed --destination ../gitian.sigs.cripto/ ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
    ./bin/gverify -v -d ../gitian.sigs.cripto/ -r ${VERSION}-osx-signed ../criptoreal/contrib/gitian-descriptors/gitian-osx-signer.yml
    mv build/out/criptoreal-osx-signed.dmg ../criptoreal-${VERSION}-osx.dmg
    popd

Criar (e verificar opcionalmente) os binários do Windows assinados:

    pushd ./gitian-builder
    ./bin/gbuild -i --commit signature=v${VERSION} ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
    ./bin/gsign --signer $SIGNER --release ${VERSION}-win-signed --destination ../gitian.sigs.cripto/ ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
    ./bin/gverify -v -d ../gitian.sigs.cripto/ -r ${VERSION}-win-signed ../criptoreal/contrib/gitian-descriptors/gitian-win-signer.yml
    mv build/out/criptoreal-*win64-setup.exe ../criptoreal-${VERSION}-win64-setup.exe
    mv build/out/criptoreal-*win32-setup.exe ../criptoreal-${VERSION}-win32-setup.exe
    popd

Confirme sua assinatura para os binários OS X / Windows assinados:

    pushd gitian.sigs.cripto
    git add ${VERSION}-osx-signed/${SIGNER}
    git add ${VERSION}-win-signed/${SIGNER}
    git commit -a
    git push  # Assuming you can push to the gitian.sigs.cripto tree
    popd

### Depois que 3 ou mais pessoas tiverem compilado o gitian e seus resultados coincidem:

- Crie `SHA256SUMS.asc` para as compilações, e assine GPG:

```bash
sha256sum * > SHA256SUMS
```

A lista de arquivos deverá ser:
```
criptoreal-${VERSION}-aarch64-linux-gnu.tar.gz
criptoreal-${VERSION}-arm-linux-gnueabihf.tar.gz
criptoreal-${VERSION}-i686-pc-linux-gnu.tar.gz
criptoreal-${VERSION}-x86_64-linux-gnu.tar.gz
criptoreal-${VERSION}-osx64.tar.gz
criptoreal-${VERSION}-osx.dmg
criptoreal-${VERSION}.tar.gz
criptoreal-${VERSION}-win32-setup.exe
criptoreal-${VERSION}-win32.zip
criptoreal-${VERSION}-win64-setup.exe
criptoreal-${VERSION}-win64.zip
```
Os arquivos `*-debug*` gerados pela compilação gitian build contém símbolos de depuração para solução de problemas por desenvolvedores. Supõe-se que qualquer pessoa que esteja interessada em depuração pode executar o gitian para gerar os arquivos por eles mesmos. Para evitar a confusão do usuário final sobre qual arquivo escolher, além de salvar o armazenamento *não carregue estes no servidor criptoreal.org, nem coloque-os em torrent*.

- Assine GPG, exclua o arquivo não assinado:
```
gpg --digest-algo sha256 --clearsign SHA256SUMS # outputs SHA256SUMS.asc
rm SHA256SUMS
```
(o algoritmo digest é forçado a sha256 para evitar a confusão do cabeçalho `Hash:` que GPG adiciona com o SHA256 usado para os arquivos)
Nota: Verifique se o próprio SHA256SUMS não termina em SHA256SUMS, que é uma entrada falsa/absurda.

- Faça o upload dos zips e instaladores, assim como `SHA256SUMS.asc` do último passo, para o servidor criptoreal.org.

```

- Atualize a versãoa criptoreal.org

- Anuncie o lançamento:

  - criptoreal-dev e lista de email criptoreal-dev

  - post no blog.criptoreal.org 

  - Atualize o título #criptoreal e #criptoreal-dev no Freenode IRC

  - Opcional twitter, reddit /r/Criptoreal, ... mas isso geralmente já se resolve sozinho

  - Atualize o arquivo de notas de lançamento para a nova versão em `doc/release-notes/` (branch `master` e versão)

  - Crie um novo [release no GitHub](https://github.com/criptoreal/criptoreal/releases/new) com um link para as notas de lançamento arquivadas.

  - Comemore
