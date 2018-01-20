Criptoreal Core Fuzz-testing
==========================

Um teste especial `test_criptoreal_fuzzy` é fornecido para trazer um ponto de entrada fácil para fuzzers e similares. Neste documento descrevemos como utilizá-lo com AFL.

Compilando AFL
-------------

É recomendado utilizar somente a última versão da afl:
```
wget http://lcamtuf.coredump.cx/afl/releases/afl-latest.tgz
tar -zxvf afl-latest.tgz
cd afl-<version>
make
export AFLPATH=$PWD
```

Instrumentação
----------------

Para compilar o Criptoreal Core usando instrumentação AFL (isto pressupõe que o `AFLPATH` foi configurado como mostrado acima):
```
./configure --disable-ccache --disable-shared --enable-tests CC=${AFLPATH}/afl-gcc CXX=${AFLPATH}/afl-g++
export AFL_HARDEN=1
cd src/
make test/test_criptoreal_fuzzy
```
Desativamos o ccache porque não queremos poluir o ccache com objetos instrumentados, e da mesma forma não queremos usar objetos em cache não instrumentados ligados.

Preparando o fuzzing
------------------

A AFL precisa de um diretório de entrada com exemplos, e um diretório de saída onde irá colocar exemplos que foram encontrados. Estes podem estar em qualquer lugar no sistema de arquivos, nós iremos definir variáveis de ambiente para facilitar a referência.

```
mkdir inputs
AFLIN=$PWD/inputs
mkdir outputs
AFLOUT=$PWD/outputs
```

Entradas de exemplo estão disponíveis em:

- https://download.visucore.com/bitcoin/bitcoin_fuzzy_in.tar.xz
- http://strateman.ninja/fuzzing.tar.xz

Extraia estas (ou outras entradas de partida) no diretório de `inputs` antes de iniciar o fuzzing.

Fuzzing
--------

Para iniciar o fuzzing atual use:
```
$AFLPATH/afl-fuzz -i ${AFLIN} -o ${AFLOUT} -m52 -- test/test_criptoreal_fuzzy
```

Você pode precisar alterar alguns parâmetros do kernel para testar otimamente - `afl-fuzz`irá informar um erro e sugestão se assim for
