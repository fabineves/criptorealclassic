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
Como Doxygen reconhece os comentários pelos delimitadores (`/**` e `*/` neste caso), você não
*precisa* informar qualquer comando para um que um comentário seja válido; apenas um texto de descrição é suficiente.

Para descrever uma classe use a mesma construção acima da definição da classe:
```c++
/**
 * Alertas são para notificações de versões antigas caso elas se tornem obsoletas
 * e precisem do upgrade. Esta mensagem é mostrada na barra de status.
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

Tópicos
-------

- ThreadScriptCheck : Verifica scripts de bloco.

- ThreadImport : Carrega blocos de arquivos blk*.dat files ou bootstrap.dat.

- StartNode : Inicia outros tópicos.

- ThreadDNSAddressSeed : Carrega endereços dos peers do DNS.

- ThreadMapPort : Inicialização/Desligamento universal plug-and-play 

- ThreadSocketHandler : Envia/Recebe dados dos peers na porta 5526.

- ThreadOpenAddedConnections : Abre conexões de rede para os nós adicionados.

- ThreadOpenConnections : Inicia novas conexões para os peers.

- ThreadMessageHandler : Manipulação de mensagens de nível superior (envio e recebimento).

- DumpAddresses : Descarrega endereços IP dos nodes para o arquivo peers.dat.

- ThreadFlushWalletDB : Fecha o arquivo wallet.dat caso não tenha sido usado em 500ms.

- ThreadRPCServer : Controlador de chamadas de procedimento remoto, escuta na porta 5527 para conexões e prestação de serviços.

- BitcoinMiner : Gera criptoreais (caso a carteira esteja habilitada).

- Shutdown : Realiza uma parada ordenada de tudo.

Ignorando aquivos IDE/editor 
--------------------------

Em ambientes fechados onde todos usam o mesmo IDE, é comum adicionar os arquivos temporários que ele produz no arquivo `.gitignore` em todo o projeto.

Entretanto, em software de código aberto como o Criptoreal Core, onde todos usam seus próprios editores/IDE/Ferramentas, isto é menos comum. Somente você sabe quais os arquivos que seu editor produz e isto pode mudar de versão para versão. A forma canônica de fazer isto é criar o seu gitignore local. Adicione isso a `~/.gitconfig`:

```
[core]
        excludesfile = /home/.../.gitignore_global
```

(em alternativa, digite o comando `git config --global core.excludesfile ~/.gitignore_global`
no terminal)

Então coloque os nomes dos arquivos temporários da sua ferramenta favorita neste arquivo, e.g.
```
# NetBeans
nbproject/
```

Outra opção é criar um repositório excluindo o arquivo `.git/info/exclude`. Estes não são comprometidos, mas se aplicam a apenas um repositório.

Se um conjunto de ferramentas é usado pelo sistema de compilação ou de scripts, o repositório (por exemplo, lcov) é aceitável adicionar seus arquivos ao `.gitignore` e comprometê-los.

Diretrizes de Desenvolvimento
============================

Algumas recomendações não relacionadas ao estilo para desenvolvedores, assim como os pontos para prestar atenção aos revisores do código do Criptoreal Core.

Núcleo Criptoreal Core
----------------------

- Novas funcionalidades devem ser expostas no RPC primeiramente, depois podem ser disponibilizadas na GUI

  - *Razão*: RPC permite um melhor teste automático. O conjunto de testes para GUI é muito limitado.

- Certifique-se que de todos os pedidos de pool passem por Travis CI antes da fusão

  - *Razão*: Certifique-se de que eles passem em testes completos, e que o testador continue passando no ramo mestre. Caso contrário, todos os novos pedidos de pull começarão a falhar nos testes, resultando em confusão e caos.
  - *Explanation*: Se o conjunto de testes foi atualizado para uma mudança, isto deve ser feito primeiro.

Carteira
-------

- Certifique-se de que nenhuma falha ocorra com a opção run-time `-disablewallet`.

  - *Razão*: No código RPC que condicionalmente usa a carteira (como o `validateaddress`) é fácil esquecer que o ponteiro global `pwalletMain` pode ser NULL. Veja `qa/rpc-tests/disablewallet.py` para testes funcionais que exercem a API com`-disablewallet`

- Inclui `db_cxx.h` (cabeçalho BerkeleyDB) somente quando `ENABLE_WALLET` está ativo

  - *Razão*: Caso contrário a compilação disable-wallet irá falhar em ambientes sem BerkeleyDB

C++
-------------

- Asserções não devem ter efeitos colaterais

  - *Razão*: Embora o código fonte seja configurado para se recusar a compilar com asserções disabilitadas, ter efeitos colaterais nas asserções é inesperado e torna o código ainda mais difícil de ser entendido

- Se você usar o `.h`, você deve veicular o `.cpp`

  - *Razão*: Incluir arquivos definem a interface para o código nos arquivos de implementação. Incluir um mas não ligar o outro é confuso. Por favor evite isto. Mover funções do `.h` para `.cpp` não deve resultar em erros de compilação.

- Use o paradigma RAII (Aquisição de Recursos na inicialização) quando possível. Por exemplo, usando `unique_ptr` para alocações em uma função.

  - *Razão*: Isto evita a perda de memória e recursos, e garante segurança da exceção

Estrutura de dados C++ 
--------------------

- Nunca use a sintaxe `std::map []` ao ler a partir de um mapa, ao invés use o `.find()`

  - *Razão*: `[]` faz uma inserção (do elemento padrão) se o item ainda não existe em um mapa. Isto resultou em perda de memória no passado, assim como as condições (esperando um comportamento de leitura). Usar `[]` é bom para  *escrever* para um mapa

- Não compare um interador de uma estrutura de dados com um interador de outra estrutura de dados (mesmo que sejam do mesmo tipo)

  - *Razão*: O comportamento é indefinido. Em C++ isto significa "pode reformatar o universo", na prática isto resultou em pelo menos um erro difícil de depurar

- Cuidado com excesso de acesso vetorial fora dos limites. `&vch[vch.size()]` é ilegal, incluindo `&vch[0]` para um vetor vazio. Use `vch.data()` e `vch.data() + vch.size()` em vez disso.

- A verificação dos limites de vetores só é ativada no modo de depuração. Não confie nisso

- Verifique se os construtores inicializam todos os campos. Se isto é ignorado por uma boa razão (i.e., otimização no caominho crítico), adicione um comentário explícito sobre isto

  - *Razão*: Assegurar o determinismo evitando o uso acidental de valores não inicializados. Além disso, os analisadores estáticos se recusam a isso.

- Use `char`s, ou ainda `uint8_t` e  `int8_t` específicos assinados ou não. Não use `char` ao menos que seja para passar para um API de terceiros. Este tipo pode ser assinado ou não dependendo da arquitetura, que pode levar a problemas de interoperabilidade ou condições perigosas, como acessos de matriz fora dos limites

- Prefira construções explícitas ao invés de implícitas que dependem do comportamento 'mágico' do C++

  - *Razão*: Mais fácil de entender o que está acontecendo, mais fácil detectar erros, até para aqueles que tem um pouco mais de dificuldade

Strings e formatação
------------------------

- Cuidado com `LogPrint` versus `LogPrintf`. `LogPrint` toma um argumento `category`, `LogPrintf` não.

  - *Razão*: A confusão destes pode resultar em exceções de runtime devido à incompatibilidade de formatação, e é mais fácil de errar por causa de nomes sutilmente semelhantes

- Use `std::string`, evitando as funções de manipulação de sequência C

  - *Razão*: A sequência de caracteres C++ geralmente é pouco segura, menor alcance para buffer overflows e surpresas com caracteres `\0` . Além disso, algumas manipulações de sequência C tendem a atuar de forma diferente dependendo da plataforma ou até da localização do usuário

- Use `ParseInt32`, `ParseInt64`, `ParseUInt32`, `ParseUInt64`, `ParseDouble` do `utilstrencodings.h` para análise de número

  - *Razão*: Estas funções fazem a verificação do overflow, e evitam problemas locais que incomodam

- Para `strprintf`, `LogPrint`, `LogPrintf` a formatação de caracteres não precisa de tamanho específico

  - *Razão*: Criptoreal Core usa tinyformat, Que é um tipo seguro. Deixe-as de fora para evitar confusão

Nomes variáveis
--------------

O aviso de sombreamento (`-Wshadow`) está ativado por padrão. Ele evita que problemas surjam ao usar uma variável diferente com o mesmo nome.

Por favor nomeie variáveis para que seus nomes não obstruam as variáveis definidas em código fonte.

Por exemplo, nos inicializadores dos membros, prefira o `_` para o nome do argumento, que acompanha o nome do membro:

```c++
class AddressBookPage
{
    Mode mode;
}

AddressBookPage::AddressBookPage(Mode _mode) :
      mode(_mode)
...
```

Ao usar ciclos aninhados, não nomeie a variável do ciclo interno igual ao do ciclo superior, etc.

Tópicos e sincronização
----------------------------

- Compile e execute testes com `-DDEBUG_LOCKORDER` para verificar que nenhum deadlock foi introduzido. A partir da 0.12, isto é definido por padrão ao configurar com `--enable-debug`

- Ao usar `LOCK`/`TRY_LOCK` esteja ciente de que o bloqueio existe no contexto do escopo atual, então execute a instrução e o código que precisa do bloqueio com chaves

  OK:

```c++
{
    TRY_LOCK(cs_vNodes, lockNodes);
    ...
}
```

  Errado:

```c++
TRY_LOCK(cs_vNodes, lockNodes);
{
    ...
}
```

Organização do código fonte
--------------------------

- O código de implementação deve entrar no arquivo `.cpp` e não no `.h`, a menos que seja necessário devido ao uso de um modelo ou quando o desempenho devido ao lançamento for crítico

  - *Razão*: Arquivos de cabeçalho menores e mais simples são mais fáceis de ler e reduzem o tempo de compilação

- Não importe nada no namespace global (`using namespace ...`). Use tipos totalmente especificados, como `std::string`.

  - *Razão*: Evita conflitos de símbolos

GUI
-----

- Não exiba ou manipule diálogos no modelo de código (classes `*Model`)

  - *Razão*: As classes de modelo passam por eventos e dados do núcleo, elas não devem interagir como usuário. Assim que entram as classes View. O inverso também acontece: tente não acessar diretamente as estruturas de dados das Views.

Subtrees
----------

Várias partes do repositório são subtrees do software mantidos em outros lugares.

Alguns deles são mantidos por desenvolvedores ativos do Criptoreal Core, e neste caso, mudanças devem ir diretamente para cima sem PRed diretamente contra o projeto. Eles serão mesclados de volta na próxima subtree.

Outros são projetos externos sem um relacionamento próximo com nosso projeto. Mudanças também devem ser enviadas mas as correções de bug também podem ser prudentes para PR contra a Criptoreal Core, para que possam ser integradas rapidamente.  Mudanças cosméticas devem apenas ser enviadas.

Existe uma ferramenta em contrib/devtools/git-subtree-check.sh para verificar um diretório de subtree para consistência com seu repositório.

As subtrees atuais incluem:

- src/leveldb
  - Upstream em https://github.com/google/leveldb ; Mantido pelo Google, mas abre PRs importantes para o Core evitar atrasos

- src/libsecp256k1
  - Upstream em https://github.com/bitcoin-core/secp256k1/ ; ativamente mantida pelos colaboradores principais.

- src/crypto/ctaes
  - Upstream em https://github.com/bitcoin-core/ctaes ; ativamente mantida pelos colaboradores principais

- src/univalue
  - Upstream em https://github.com/jgarzik/univalue ; relata PRs importantes para evitar atrasos.


Dicas Git and GitHub
---------------------

- Para resolver conflitos de mesclagem/rebase, pode ser útil habilitar o estilo diff3 usando `git config merge.conflictstyle diff3`. 
Ao invés de

        <<<
        seu
        ===
        deles
        >>>

  você vai ver

        <<<
       seu
        |||
        original
        ===
        deles
        >>>

 Isto pode tornar mais claro o que causou o conflito. Neste estilo, muitas vezes você só pode ver o que mudou entro *original* e *deles*, e aplicar mecanicamente para *seu* (ou vice-versa).

- Ao revisar os patches que alteram o recuo nos arquivos C++, use `git diff -w` e `git show -w`. Isto faz com que o algoritmo diff ignore mudanças em espaços em branco. Este recurso também está disponível no github.com, adicionando `?w=1`  no final de qualquer URL que mostre uma diff.

- Ao revisar os patches que mudam os nomes dos símbolos em muitos lugares, use `git diff --word-diff`. Isto irá em vez de mostrar o patch como *linhas* excluídas/adicionadas, mostra as *palavras* deletadas/adicionadas.

- Ao revisar os patches que movem o código, tente usar `git diff --patience commit~:old/file.cpp commit:new/file/name.cpp`, e ignorando tudo, exceto o corpo de código movido, que deve aparecer com nenhuma linha `+` ou `-`. No caso de não ser um movimento puro, isto pode até funcionar quando combinado com as opções `-w` ou `--word-diff` descritas acima.

- Ao olhar para as pull requests de outros, pode fazer sentido adicionar a seguinte seção ao seu arquivo `.git/config`:

        [remote "upstream-pull"]
                fetch = +refs/pull/*:refs/remotes/upstream-pull/*
                url = git@github.com:criptoreal/criptoreal.git

Isto irá adicionar um `upstream-pull` remoto ao seu repositório git, que pode ser obtido usando `git fetch --all` ou `git fetch upstream-pull`. Posteriormente, você pode usar `upstream-pull/NUMBER/head` em argumentos `git show`, `git checkout` e em qualquer lugar um id de confirmação seria aceitável para ver as alterações do NUMBER do pull request.
