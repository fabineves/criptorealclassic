Notas do desenvolvedor
===============

Vários estilos de codificação foram usados durante o histórico do código base e o resultado não foi muito consistente. Entretanto, estamos tentando convergir para um estilo único, então use-o em um novo código. O codigo antigo será convertido gradualmente e você é encorajado a usar o [clang-format-diff script](/contrib/devtools/README.md#clang-format-diffpy) que é fornecido para limpar o patch automaticamente antes de enviar uma pull request.

- Regras básicas especificadas em  [src/.clang-format](/src/.clang-format).
  - Chaves em novas linhas para namespaces, classes, funções, métodos.
  - Chaves na mesma linha para o restante.
  - 4 indentações de espaço (sem abas) para cada bloco exceto namespaces.
  - Sem indentação para `public`/`protected`/`private` ou para `namespace`.
  - Sem espaço extra dentro de parênteses; não faça ( isto )
  - Sem espaço após os nomes das funções; um espaço após `if`, `for` e `while`.
  - Se um `if` só tem uma única declaração, pode aparecer na mesma linha que o if, sem as chaves. Em qualquer outro caso, chaves são obrigatórias, e as cláusulas then e else devem aparecer corretamente indentadas em uma nova linha.
  - `++i` é preferível ao invés de `i++`.

Exemplo de estilo de bloco:
```c++
namespace foo
{
class Class
{
    bool Function(const std::string& s, int n)
    {
        // Comentário que resume o que esta área do código faz
        for (int i = 0; i < n; ++i) {
            // Quando algo falhar, retorne antes
            if (!Something()) return false;
            ...
            if (SomethingElse()) {
                DoMore();
            } else {
                DoLess();
            }
        }

        // O retorno informando sucesso geralmente está no final
        return true;
    }
}
}
```

Comentários Doxygen 
-----------------

Para facilitar a geração da documentação, use blocos de comentários compatíveis com doxygen para funções, métodos e campos.

Por exemplo, para descrever uma função use:
```c++
/**
 * ... text ...
 * @param[in] arg1    Uma descrição
 * @param[in] arg2    Outra descrição do argumento
 * @pre Pré-requisito para a função...
 */
bool function(int arg1, const char *arg2)
```
Uma lista completa de comandos `@xxx` podem ser encontradas em http://www.stack.nl/~dimitri/doxygen/manual/commands.html.
Como Doxygen reconhece os comentários pelos delimitadores (`/**` and `*/` neste caso), você não
*precisa* informar qualquer comando para um que um comentário seja válido; apenas um texto de descrição é suficiente.

Para descrever uma classe use a mesma construção acima da definição da classe:
```c++
/**
 * Alertas são para notificações de versões antigas caso elas se tornem obsoletas
 * e precisa do upgrade. Esta mensagem é mostrada na barra de status.
 * @see GetWarnings()
 */
class CAlert
{
```

Para descrever um membro ou uso variável:
```c++
int var; //!< Descrição detalhada após o membro
```

ou
```cpp
//! Descrição antes do membro
int var;
```

Também está OK:
```c++
///
/// ... text ...
///
bool function2(int arg1, const char *arg2)
```

Não OK (usado muito no fonte atual, mas não retirado):
```c++
//
// ... texto ...
//
```

Uma lista completa de sintaxes de comentários obtidas pelo doxygen pode ser encontrada em http://www.stack.nl/~dimitri/doxygen/manual/docblocks.html, mas se possível, use um dos estilos acima.

Dicas e truques de desenvolvimento
---------------------------

**compilando para depuração**

Execute a configuração com a opção --enable-debug, então make. Ou então execute a configuração com CXXFLAGS="-g -ggdb -O0" ou qualquer outras flags de depuração que você precisa.

**debug.log**

Se o código estiver se comportando de maneira estranha, dê uma olhada no arquivo debug.log no diretório de dados; mensagens de erro e de depuração são escritas neste arquivo.

A opção de linha de comando -debug=... controla a depuração; executando apenas -debug ou -debug=1 irá interferir em todas as categorias (e te dará um arquivo debug.log muito grande).

O código Qt direciona a saída qDebug() para o debug.log sob a categoria "qt": execute com -debug=qt para verificar.

**Modos testnet e regtest**

Execute com a opção -testnet para executar com "play criptoreais" na rede de teste, se você está testando o código multi-máquina, isto precisa ser feito através da internet.

Se você está testando algo que pode ser executando em uma máquina, execute com a opção -regtest.
No modo de teste de regressão, os blocos podem ser criados sob demanda; veja qa/rpc-tests/ para testes que executem o modo -regtest.

**DEBUG_LOCKORDER**

Criptoreal Core é um aplicativo multiprocessado, os deadlocks ou outros tipos de erros podem ser muito difíceis de serem rastreados. Compiland com o -DDEBUG_LOCKORDER (configurar CXXFLAGS="-DDEBUG_LOCKORDER -g") insere verificações de tempo de execução para acompanhar quais os bloqueios são mantidos, e adiciona avisos no arquivo debug.log caso inconsistências sejam detectadas.

Notas de uso de Bloqueio/mutex 
-------------------------

O codigo tem várias linhas, e usa mutexes e as macros LOCK/TRY_LOCK para proteger a estrutura de dados.

Devido ao pedido de bloqueio inconsistente dos deadlocks (o tópico 1 trava cs_main e então cs_wallet, enquanto o tópico 2 trava na ordem contrária: resultado, o impasse como cada um espera com que o outro libere o seu bloqueio) são um problema. Compile com -DDEBUG_LOCKORDER para obter as inconsistências de ordem de bloqueio relatadas no arquivo debug.log.

Re-arquitetar o código do núcleo para que haja interfaces melhor definidas entre os vários componentes é o objetivo, com qualquer bloqueio necessário feito pelos componentes (por exemplo, veja a classe CKeyStore autônoma e seu bloqueio cs_KeyStore).

Threads
-------

- ThreadScriptCheck : Verifies block scripts.

- ThreadImport : Loads blocks from blk*.dat files or bootstrap.dat.

- StartNode : Starts other threads.

- ThreadDNSAddressSeed : Loads addresses of peers from the DNS.

- ThreadMapPort : Universal plug-and-play startup/shutdown

- ThreadSocketHandler : Sends/Receives data from peers on port 5526.

- ThreadOpenAddedConnections : Opens network connections to added nodes.

- ThreadOpenConnections : Initiates new connections to peers.

- ThreadMessageHandler : Higher-level message handling (sending and receiving).

- DumpAddresses : Dumps IP addresses of nodes to peers.dat.

- ThreadFlushWalletDB : Close the wallet.dat file if it hasn't been used in 500ms.

- ThreadRPCServer : Remote procedure call handler, listens on port 5527 for connections and services them.

- BitcoinMiner : Generates criptoreals (if wallet is enabled).

- Shutdown : Does an orderly shutdown of everything.

Ignoring IDE/editor files
--------------------------

In closed-source environments in which everyone uses the same IDE it is common
to add temporary files it produces to the project-wide `.gitignore` file.

However, in open source software such as Criptoreal Core, where everyone uses
their own editors/IDE/tools, it is less common. Only you know what files your
editor produces and this may change from version to version. The canonical way
to do this is thus to create your local gitignore. Add this to `~/.gitconfig`:

```
[core]
        excludesfile = /home/.../.gitignore_global
```

(alternatively, type the command `git config --global core.excludesfile ~/.gitignore_global`
on a terminal)

Then put your favourite tool's temporary filenames in that file, e.g.
```
# NetBeans
nbproject/
```

Another option is to create a per-repository excludes file `.git/info/exclude`.
These are not committed but apply only to one repository.

If a set of tools is used by the build system or scripts the repository (for
example, lcov) it is perfectly acceptable to add its files to `.gitignore`
and commit them.

Development guidelines
============================

A few non-style-related recommendations for developers, as well as points to
pay attention to for reviewers of Criptoreal Core code.

General Criptoreal Core
----------------------

- New features should be exposed on RPC first, then can be made available in the GUI

  - *Rationale*: RPC allows for better automatic testing. The test suite for
    the GUI is very limited

- Make sure pull requests pass Travis CI before merging

  - *Rationale*: Makes sure that they pass thorough testing, and that the tester will keep passing
     on the master branch. Otherwise all new pull requests will start failing the tests, resulting in
     confusion and mayhem

  - *Explanation*: If the test suite is to be updated for a change, this has to
    be done first

Wallet
-------

- Make sure that no crashes happen with run-time option `-disablewallet`.

  - *Rationale*: In RPC code that conditionally uses the wallet (such as
    `validateaddress`) it is easy to forget that global pointer `pwalletMain`
    can be NULL. See `qa/rpc-tests/disablewallet.py` for functional tests
    exercising the API with `-disablewallet`

- Include `db_cxx.h` (BerkeleyDB header) only when `ENABLE_WALLET` is set

  - *Rationale*: Otherwise compilation of the disable-wallet build will fail in environments without BerkeleyDB

General C++
-------------

- Assertions should not have side-effects

  - *Rationale*: Even though the source code is set to to refuse to compile
    with assertions disabled, having side-effects in assertions is unexpected and
    makes the code harder to understand

- If you use the `.h`, you must link the `.cpp`

  - *Rationale*: Include files define the interface for the code in implementation files. Including one but
      not linking the other is confusing. Please avoid that. Moving functions from
      the `.h` to the `.cpp` should not result in build errors

- Use the RAII (Resource Acquisition Is Initialization) paradigm where possible. For example by using
  `unique_ptr` for allocations in a function.

  - *Rationale*: This avoids memory and resource leaks, and ensures exception safety

C++ data structures
--------------------

- Never use the `std::map []` syntax when reading from a map, but instead use `.find()`

  - *Rationale*: `[]` does an insert (of the default element) if the item doesn't
    exist in the map yet. This has resulted in memory leaks in the past, as well as
    race conditions (expecting read-read behavior). Using `[]` is fine for *writing* to a map

- Do not compare an iterator from one data structure with an iterator of
  another data structure (even if of the same type)

  - *Rationale*: Behavior is undefined. In C++ parlor this means "may reformat
    the universe", in practice this has resulted in at least one hard-to-debug crash bug

- Watch out for out-of-bounds vector access. `&vch[vch.size()]` is illegal,
  including `&vch[0]` for an empty vector. Use `vch.data()` and `vch.data() +
  vch.size()` instead.

- Vector bounds checking is only enabled in debug mode. Do not rely on it

- Make sure that constructors initialize all fields. If this is skipped for a
  good reason (i.e., optimization on the critical path), add an explicit
  comment about this

  - *Rationale*: Ensure determinism by avoiding accidental use of uninitialized
    values. Also, static analyzers balk about this.

- Use explicitly signed or unsigned `char`s, or even better `uint8_t` and
  `int8_t`. Do not use bare `char` unless it is to pass to a third-party API.
  This type can be signed or unsigned depending on the architecture, which can
  lead to interoperability problems or dangerous conditions such as
  out-of-bounds array accesses

- Prefer explicit constructions over implicit ones that rely on 'magical' C++ behavior

  - *Rationale*: Easier to understand what is happening, thus easier to spot mistakes, even for those
  that are not language lawyers

Strings and formatting
------------------------

- Be careful of `LogPrint` versus `LogPrintf`. `LogPrint` takes a `category` argument, `LogPrintf` does not.

  - *Rationale*: Confusion of these can result in runtime exceptions due to
    formatting mismatch, and it is easy to get wrong because of subtly similar naming

- Use `std::string`, avoid C string manipulation functions

  - *Rationale*: C++ string handling is marginally safer, less scope for
    buffer overflows and surprises with `\0` characters. Also some C string manipulations
    tend to act differently depending on platform, or even the user locale

- Use `ParseInt32`, `ParseInt64`, `ParseUInt32`, `ParseUInt64`, `ParseDouble` from `utilstrencodings.h` for number parsing

  - *Rationale*: These functions do overflow checking, and avoid pesky locale issues

- For `strprintf`, `LogPrint`, `LogPrintf` formatting characters don't need size specifiers

  - *Rationale*: Criptoreal Core uses tinyformat, which is type safe. Leave them out to avoid confusion

Variable names
--------------

The shadowing warning (`-Wshadow`) is enabled by default. It prevents issues rising
from using a different variable with the same name.

Please name variables so that their names do not shadow variables defined in the source code.

E.g. in member initializers, prepend `_` to the argument name shadowing the
member name:

```c++
class AddressBookPage
{
    Mode mode;
}

AddressBookPage::AddressBookPage(Mode _mode) :
      mode(_mode)
...
```

When using nested cycles, do not name the inner cycle variable the same as in
upper cycle etc.


Threads and synchronization
----------------------------

- Build and run tests with `-DDEBUG_LOCKORDER` to verify that no potential
  deadlocks are introduced. As of 0.12, this is defined by default when
  configuring with `--enable-debug`

- When using `LOCK`/`TRY_LOCK` be aware that the lock exists in the context of
  the current scope, so surround the statement and the code that needs the lock
  with braces

  OK:

```c++
{
    TRY_LOCK(cs_vNodes, lockNodes);
    ...
}
```

  Wrong:

```c++
TRY_LOCK(cs_vNodes, lockNodes);
{
    ...
}
```

Source code organization
--------------------------

- Implementation code should go into the `.cpp` file and not the `.h`, unless necessary due to template usage or
  when performance due to inlining is critical

  - *Rationale*: Shorter and simpler header files are easier to read, and reduce compile time

- Don't import anything into the global namespace (`using namespace ...`). Use
  fully specified types such as `std::string`.

  - *Rationale*: Avoids symbol conflicts

GUI
-----

- Do not display or manipulate dialogs in model code (classes `*Model`)

  - *Rationale*: Model classes pass through events and data from the core, they
    should not interact with the user. That's where View classes come in. The converse also
    holds: try to not directly access core data structures from Views.

Subtrees
----------

Several parts of the repository are subtrees of software maintained elsewhere.

Some of these are maintained by active developers of Criptoreal Core, in which case changes should probably go
directly upstream without being PRed directly against the project.  They will be merged back in the next
subtree merge.

Others are external projects without a tight relationship with our project.  Changes to these should also
be sent upstream but bugfixes may also be prudent to PR against Criptoreal Core so that they can be integrated
quickly.  Cosmetic changes should be purely taken upstream.

There is a tool in contrib/devtools/git-subtree-check.sh to check a subtree directory for consistency with
its upstream repository.

Current subtrees include:

- src/leveldb
  - Upstream at https://github.com/google/leveldb ; Maintained by Google, but open important PRs to Core to avoid delay

- src/libsecp256k1
  - Upstream at https://github.com/bitcoin-core/secp256k1/ ; actively maintaned by Core contributors.

- src/crypto/ctaes
  - Upstream at https://github.com/bitcoin-core/ctaes ; actively maintained by Core contributors.

- src/univalue
  - Upstream at https://github.com/jgarzik/univalue ; report important PRs to Core to avoid delay.


Git and GitHub tips
---------------------

- For resolving merge/rebase conflicts, it can be useful to enable diff3 style using
  `git config merge.conflictstyle diff3`. Instead of

        <<<
        yours
        ===
        theirs
        >>>

  you will see

        <<<
        yours
        |||
        original
        ===
        theirs
        >>>

  This may make it much clearer what caused the conflict. In this style, you can often just look
  at what changed between *original* and *theirs*, and mechanically apply that to *yours* (or the other way around).

- When reviewing patches which change indentation in C++ files, use `git diff -w` and `git show -w`. This makes
  the diff algorithm ignore whitespace changes. This feature is also available on github.com, by adding `?w=1`
  at the end of any URL which shows a diff.

- When reviewing patches that change symbol names in many places, use `git diff --word-diff`. This will instead
  of showing the patch as deleted/added *lines*, show deleted/added *words*.

- When reviewing patches that move code around, try using
  `git diff --patience commit~:old/file.cpp commit:new/file/name.cpp`, and ignoring everything except the
  moved body of code which should show up as neither `+` or `-` lines. In case it was not a pure move, this may
  even work when combined with the `-w` or `--word-diff` options described above.

- When looking at other's pull requests, it may make sense to add the following section to your `.git/config`
  file:

        [remote "upstream-pull"]
                fetch = +refs/pull/*:refs/remotes/upstream-pull/*
                url = git@github.com:criptoreal/criptoreal.git

  This will add an `upstream-pull` remote to your git repository, which can be fetched using `git fetch --all`
  or `git fetch upstream-pull`. Afterwards, you can use `upstream-pull/NUMBER/head` in arguments to `git show`,
  `git checkout` and anywhere a commit id would be acceptable to see the changes from pull request NUMBER.
