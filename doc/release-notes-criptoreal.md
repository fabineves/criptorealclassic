O Criptoreal Core versão 0.14.2 está disponível em:

  <https://criptoreal.org/>

Esta é uma nova versão, incluindo novos recursos, várias correções de bugs e melhorias de desempenho, assim como traduções atualizadas.

Por favor, reporte bugs usando o rastreador de erros do github:

  <https://github.com/criptoreal/criptoreal/issues>

Compatibilidade
===============

Criptoreal Core é amplamente testado em vários sistemas operacionais usando o kernel do Linux, macOS 10.8+, Windows Vista e posterior.

A Microsoft encerrou o suporte para Windows XP em [8 de abril de 2014](https://www.microsoft.com/en-us/WindowsForBusiness/end-of-xp-support), nenhuma tentativa é feita para evitar a instalação ou a execução do software no Windows XP, você ainda pode fazê-lo por sua conta e risco mas fique ciente que existem instabilidades e problemas já conhecidos. Por favor não reporte problemas com o Windows XP no  rastreador de erros.

Criptoreal Core também deve funcionar na maioria dos sistemas semelhantes ao Unix, mas estes não são frequentemente testados.

Mudanças notáveis
=================

Novo prefixo de endereço Multisig
---------------------------------

O Criptoreal Core agora suporta endereços P2SH começando com P na mainnet e com p na testnet. Endereços P2SH começando com 3 no mainnet e m ou n no testnet continuarão a ser válidos. Os endereços antigos e novos podem ser usados de forma intercambiável.

miniupnp CVE-2018-8798
----------------------

O  miniupnpc foi atualizado para 2.0.20170509. Isto corrige um erro de assinatura (presente no MiniUPnPc v1.4.20101221 até v2.0) que permite ataques remotos (dentro da LAN) que causam um "denial of service" ou outro impacto não especificado.

Isto afeta apenas usuários que habilitaram explicitamente o UPnP através da configuração da GUI ou através da opção -upnp , já que a última vulnerabilidade UPnP (no Criptoreal Core 0.10.4) foi desabilitada por padrão.

Se você usar esta opção, é recomendado fazer o upgrade para esta versão o quanto antes.

Resetar Testnet
-------------

Testnet3 foi descontinuado e substituído pelo Testnet4. A porta do servidor foi alterada para 15526 entretanto a porta RPC permanece a mesma (15527).

Desenvolvedores que exigem os novos parâmetros da blockchain podem encontrá-los [aqui](https://github.com/criptoreal/criptoreal/blob/master-0.14/src/chainparams.cpp#L220).

Melhorias de performance
------------------------

A velocidade de validação e o desempenho da propagação da rede melhoraram bastante, levando a redução do tempo de download de sincronização e bloqueio.

- O script de cache da assinatura foi reimplementado como "cuckoo cache", permitindo que mais assinaturas sejam pesquisadas em cache de maneira mais rápida.
- Foram introduzidos blocos válidos que permitem que a validação de scripts seja ignorada por antecessores de blocos conhecidos, sem modificar o modelo de segurança. Veja abaixo para mais detalhes.
- Em alguns casos, blocos compactados são agora retransmitidos antes de serem totalmente validados conforme o BIP152.
- A rede P2P foi refaturada com foco na concorrência e na taxa de transferência. As operações de rede não ficam bloqueadas por conta da validação. Como resultado, a recuperação de blocos é muito mais rápida que versões anteriores em muitos casos.
- O cache UTXO agora reinvindica a memória mempool não utilizada. Isto acelera o download do bloco inicial, pois as pesquisas UTXO ainda são um grande gargalo e ainda não há nenhum uso para o mempool neste estágio.


Poda Manual
--------------

O Criptoreal Core suporta poda automática do blockchain desde 0.13.2. A poda do blockchain permite economias significativas de espaço de armazenamento, pois a grande maioria dos dados baixados pode ser descartada após o processamento e poucos dados acabam permanecendo no disco.

A poda de bloco manual pode ser ativada pela configuração `-prune=1`. Uma vez que isto está configurado, o comando RPC `pruneblockchain` pode ser usado para podar a blockchain para uma altura ou timestamp específica.

`getinfo` Descontinuada
--------------------

O comando RPC `getinfo` foi descontinuado. Cada campo na chamada RPC foi movido para a saída de outro comando com este mesmo comando dando informações adicionais que `getinfo` não forneceu. A tabela a seguir mostra para onde cada campo foi movido:

| campo `getinfo`  | Movido para                             |
|------------------|-------------------------------------------|
`"version"`	   | `getnetworkinfo()["version"]`
`"protocolversion"`| `getnetworkinfo()["protocolversion"]`
`"walletversion"`  | `getwalletinfo()["walletversion"]`
`"balance"`	   | `getwalletinfo()["balance"]`
`"blocks"`	   | `getblockchaininfo()["blocks"]`
`"timeoffset"`	   | `getnetworkinfo()["timeoffset"]`
`"connections"`	   | `getnetworkinfo()["connections"]`
`"proxy"`	   | `getnetworkinfo()["networks"][0]["proxy"]`
`"difficulty"`	   | `getblockchaininfo()["difficulty"]`
`"testnet"`	   | `getblockchaininfo()["chain"] == "test"`
`"keypoololdest"`  | `getwalletinfo()["keypoololdest"]`
`"keypoolsize"`	   | `getwalletinfo()["keypoolsize"]`
`"unlocked_until"` | `getwalletinfo()["unlocked_until"]`
`"paytxfee"`	   | `getwalletinfo()["paytxfee"]`
`"relayfee"`	   | `getnetworkinfo()["relayfee"]`
`"errors"`	   | `getnetworkinfo()["warnings"]`

ZMQ no Windows
--------------

Anteriormente, o sistema de notificação ZeroMQ não estava disponível no Windows devido a vários problemas com o ZMQ. Estes problemas foram corrigidos e agora o ZMQ pode ser usado no Windows. Veja [este documento](https://github.com/criptoreal/criptoreal/blob/master-0.14/doc/zmq.md) para ajuda sobre o uso do ZMQ em geral.

Comandos aninhados RPC no Console de Depuração
----------------------------------------------

A capacidade de aninhar comandos RPC foi adicionada ao console de depuração.  Isto permite aos usuários que a saída de um comando se torne a entrada de outro comando sem executar estes comandos separadamente.

Os comandos aninhados RPC usam a sintaxe com colchetes (i.e. `getwalletinfo()`) e podem ser aninhadas (i.e. `getblock(getblockhash(1))`). Consultas simples podem ser feitas com colchetes onde os valores do objeto são acessados com um índice de matriz ou uma string não cotada (i.e. `listunspent()[0][txid]`). Ambas as vírgulas e os espaços podem ser usados para separar os parâmetros tanto na sintaxe de colchetes e na sintaxe normal de comandos RPC.

Atividade de rede Alternada
-----------------------

Um comando RPC e uma alternância GUI foram adicionados para ativar ou desativar toda a atividade da rede p2p. O ícone de status da rede no canto inferior direito é agora o alternar GUI. Clicando no ícone, habilitará ou desativará toda a atividade da rede P2P. Se a atividade de rede estiver desativada, o ícone ficará com um X em cima dele.

Além disso, o comando RPC `setnetworkactive` foi adicionado, o que faz o mesmo que o ícone GUI. O comando leva um parâmetro booleano, `true` que permite ativar a rede e `false` que faz desativar.

Camada de informação Modal sem sincronia
----------------------------------------

Quando o Criptoreal Core está fora de sincronia na inicialização, uma camada de informação semi-transparente será mostrada na parte superior da exibição normal. Esta camada contém detalhes sobre o progresso de sincronização atual e estima a quantidade de tempo para terminar a sincronização. Esta camada também pode ser escondida e consequentemente não encontrada, clicando na barra de progresso na parte inferior da janela.

Suporte para argumentos JSON-RPC 
------------------------------------

Comandos enviados através da interface JSON-RPC e através do binário `criptoreal-cli` podem usar argumentos nomeados. Isto segue a [especificação JSON-RPC](http://www.jsonrpc.org/specification) para a passagem de parâmetros by-name com um objeto.

`criptoreal-cli` foi atualizado para suportar isso, analisando os argumentos `name=value` quando a opção `-named` é dada.

Alguns exemplos:

    src/criptoreal-cli -named help command="help"
    src/criptoreal-cli -named getblockhash height=0
    src/criptoreal-cli -named getblock blockhash=000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
    src/criptoreal-cli -named sendtoaddress address="(snip)" amount="1.0" subtractfeefromamount=true

A ordem dos argumentos não importa neste caso. Os argumentos nomeados também são úteis para deixar de lado argumentos que devem permanecer em seu valor padrão. Os argumentos raramente usados `comment` e `comment_to` para `sendtoaddress`, por exemplo, podem ser excluídos. Entretanto, isto ainda não foi implementado para muitas chamadas RPC, isto é esperado para chegar em uma versão posterior.

O servidor RPC permanece totalmente compatível com argumentos posicionais.

Dados sensíveis não estão armazenados no histórico do console de depuração
--------------------------------------------------------------------------

O console de depuração mantém um histórico de comandos previamente inseridos que podem ser acessados pressionando a tecla de seta para cima, para que os usuários possam facilmente reutilizar comandos previamente inseridos. Os comandos que possuem informações confidenciais, com senhas e chaves privadas, terão agora `(...)` no lugar dos parâmetros quando acessados através do histórico.

Retenção do Mempool durante as reinicializações
-------------------------------------

O mempool será salvo no diretório de dados antes do desligamento em um arquivo `mempool.dat`. Este arquivo preserva o mempool para que, quando o node reinicie, o mempool possa ser preenchido com as transações sem esperar que novas transações sejam criadas. Isto também preservará as alterações feitas em uma transação através de comandos como `prioritisetransaction` para que estas alterações não sejam perdidas.

Alterações na GUI
-----------------

 - Após redefinir as opções, clicando no botão `Reset Options` na caixa de diálogo de opções ou com a opção `-resetguioptions` na inicialização, o usuário deverá escolher o diretório de dados novamente. Isto é para garantir que os diretórios de dados personalizados serão mantidos após a reinicialização da opção, que limpa o conjunto de diretório de dados personalizados através da caixa de diálogo datadir.

 - Múltiplos pares agora podem ser selecionados na lista de pares na janela de depuração. Isto permite aos usuários proibir ou desconectar vários pares ao mesmo tempo ao invés de proibi-los um de cada vez.

 -  Um indicador foi adicionado ao canto direito inferior da janela principal para indicar se a carteira que está sendo usada é uma carteira HD. Este ícone ficará acinzentado com um X em cima dele se a carteira não for uma carteira HD.

Alterações RPC de baixo nível
-----------------------------

 - `importprunedfunds` aceita apenas dois argumentos obrigatórios. Algumas versões aceitam um terceiro argumento opcional, que sempre foi ignorado. Certifique-se de nunca passar mais do que dois argumentos.

 - O primeiro argumento booleano para `getaddednodeinfo` foi removido. Esta é uma mudança incompatível.

 - O comando RPC `getmininginfo` perde o campo "testnet" em favor de uma "cadeia" mais genérica (que está presente há anos).

 - Foi adicionado um novo comando RPC `preciousblock` que marca o bloco como precioso. Um bloco precioso será tratado como se fosse recebido antes de um bloco concorrente.

 - Um novo comando RPC `importmulti` foi adicionado onde recebe uma matriz de objetos JSON que representam a intenção de importar uma chave pública, uma chave privada, um endereço e um script/p2sh

 - O uso de `getrawtransaction` para recuperar transações confirmadas com saídas não gastas está obsoleto. Por enquanto, ainda funcionará, mas no futuro ele pode mudar apenas para poder recuperar informações sobre transações no mempool se `txindex` estiver habilitado.

 - Um novo comando RPC `getmemoryinfo` foi adicionado, o que retornará informações sobre o uso da memória do Criptoreal Core. Isto foi adicionado em conjunto com outras otimizações para gerenciamento de memória. Consulte [Pull #8753](https://github.com/bitcoin/bitcoin/pull/8753) para mais informações.

 - Um novo comando RPC `bumpfee` foi adicionado e permite a substituição de uma transação de carteira não confirmada que sinalizou RBF (veja a opção de inicialização `-walletrbf` acima) com uma nova transação que paga uma taxa maior e tem maior chance de ser confirmada rapidamente.

 - O primeiro argumento de posição `createrawtransaction` foi renomeado de `transactions` para `inputs`.

 - O argumento de `disconnectnode` foi renomeado de `node` para `address`.

O software de cliete que utiliza estas chamadas com argumentos nomeados precisa ser atualizado.

Mudanças HTTP REST 
-----------------

 - As respostas da chamada UTXO (`GET /rest/getutxos/<checkmempool>/<txid>-<n>/<txid>-<n> /.../<txid>-<n>.<bin|hex|json>`) foram alteradas para retornar o código de status `HTTP_BAD_REQUEST` (400) ao invés de `HTTP_INTERNAL_SERVER_ERROR` (500) quando as solicitações contém parâmetros inválidos.

Políticas de Taxa Mínima
-------------------------

Uma vez que as alterações em 0.13 para limitar automaticamente o tamanho do mempool e melhorar o desempenho da criação de blocos no código de mineração, não for importante para nodes de retransmissão ou mineiros, definir `-minrelaytxfee`. Com esta versão, os seguintes conceitos que estavam vinculados a esta opção foram separados:

- cálculo do limite para uma saída "dust". (efetivamente 3 * 1000 satoshis/kB)
- taxa mínima de um pacote de transações para ser incluída em um bloco criado pelo código de mineração. Caso os mineradores desejarem definir este mínimo, eles podem usar a nova opção `-blockmintxfee`.  (padrão para 1000 satoshis/kB)

A opção `-minrelaytxfee` continua a existir, mas é recomendável que fique desativada.

Mudança na estimativa de taxas
------------------------------

- Uma vez que a estimativa de taxa 0.13.2 para um alvo de confirmação de 1 bloco foi desativada, o controle deslizante de taxa não poderá mais escolher um alvo de 1 bloco. Esta é apenas uma alteração de comportamento menor, pois, de qualquer forma, muitas vezes não foram suficientes dados para este objetivo. `estimatefee 1` agora sempre retornará -1 e `estimatesmartfee 1` começará a procurar um alvo de 2.

- O alvo padrão para estimativa de taxa é alterado para 6 blocos na GUI (anteriormente 25) e para chamadas RPC (anteriormente 2).

Remoção da estimativa de prioridade
-----------------------------------

- A estimativa de "prioridade" necessária para uma transação a ser incluída dentro de um número alvo de blocos foi removida. As chamadas RPC estão obsoletas e retornarão -1 ou 1e24 aproximadamente. O formato para `fee_estimates.dat` também mudou para não salvar estas estimativas prioritárias. Ele será automaticamente convertido para o novo formato que não é legível por versões anteriores do software.

- O suporte para a classificação de transações "prioritárias" (coin age) para mineração é considerado obsoleto no Core e será removido na próxima grande atualização. Isto não deve ser confundido com o RPC `prioritisetransaction` que permanecerá suportado pelo Core para adicionar deltas às transações.

Gerenciamento de conexão P2P 
----------------------------

- Os pares adicionados manualmente através da opção `-addnode` ou `addnode` RPC agora têm seu próprio limite de oito conexões que não competem com outros usos de conexão de entrada ou saída e não estão sujeitos à limitação imposta pela opção `-maxconnections`.

- Novas conexões para pares adicionados manualmente são realizadas com mais rapidez.

Introdução de blocos supostamente válidos
-----------------------------------------

- Uma parcela significativa do tempo de download do bloco inicial é gasto na verificação de scripts/assinaturas.  Embora a verificação deva passar para garantir a segurança do sistema, nenhum outro resultado dessa verificação é necessário: Se o nó soubesse que o histórico de um determinado bloco era válido, poderia ignorar a verificação de scripts para seus antecessores.

- Uma nova opção de configuração 'assumevalid' é fornecida para expressar este conhecimento para o software. Ao contrário dos 'checkpoints' no passado, essa configuração não força o uso de uma determinada cadeia: as cadeias que são consistentes são processadas mais rapidamente, mas outras cadeias ainda são aceitas se elas forem escolhidas como melhores. Também, ao contrário dos 'checkpoints', o usuário pode configurar qual histórico de blocos é assumido como verdadeiro, isso significa que o software até mesmo estando desatualizado pode sincronizar mais rapidamente, se a configuração for atualizada pelo usuário.

- Como a validade de um histórico de cadeia é de fato um objetivo simples, é muito mais fácil revisar essa configuração. Como resultado, o software vem com um valor padrão ajustado para coincidir com a cadeia atual pouco antes do lançamento. O uso desse valor padrão pode ser desativado configurando -assumevalid=0

Alterar a reutilização de endereço Fundrawtransaction 
-----------------------------------------------------

- Antes de 0.14, `fundrawtransaction` era por padrão a carteira sem estado. Em quase todos os casos, `fundrawtransaction` adiciona uma mudança de saída aos resultados da transação financiada. Antes de 0.14, a chave keypool usada nunca foi marcada como chave de mudança de endereço e retornada diretamente ao keypool (levando a reutilização do endereço).  Antes de 0.14, chamar `getnewaddress` diretamente após `fundrawtransaction`, gerou o mesmo endereço que o endereço de mudança de saída.

- Desde 0.14, fundrawtransaction reserva a chave de mudança de saída do keypool por padrão (opcional configurando `reserveChangeKey`, padrão = `true`)

- Os usuários devem considerar usar `getrawchangeaddress()` em conjunto com a opção `changeAddress` do `fundrawtransaction`.

Memória mempool não utilizada que é usada pelo coincache
--------------------------------------------------------

- Antes de 0.14, a memória reservada para mempool (usando a opção `-maxmempool`) não foi utilizada durante o download inicial do bloco ou IBD. Em 0.14, o cache UTXO DB (controlado com a opção `-dbcache`) empresta memória do mempool quando há memória extra disponível. Isto pode resultar em um aumento no uso de memória durante o IBD para aqueles que dependiam anteriormente apenas da opção `-dbcache` para limitar a memória durante esse tempo.

Mineração
---------

Nas versões anteriores, getblocktemplate exigia o suporte a segmentos de clientes/mineiros uma vez que o recurso fosse ativado na rede. Nesta versão, agora existe o suporte a clientes não segmentados mesmo após a ativação, removendo todas as transações de segmentos do modelo de bloco retornado. Isto permite que os mineiros não segmentados continuem funcionando corretamente mesmo após a ativação do segwit.

Devido a limitações nas versões anteriores, o getblocktemplate também recomendou que clientes não segmentados não sentissem o bit de versão segwit. Uma vez que este não é mais um problema, getblocktemplate agora sempre recomenda sinalização  de segwit para todos os mineiros. Isto é seguro porque a capacidade de aplicar a regra é o único critério necessário para uma ativação segura, não produzindo verdadeiramente, blocos habilitados para segwit.

Contabilidade de memória UTXO
-----------------------------

O uso de memória para o cache UTXO está sendo calculado com mais precisão, de modo que o limite configurado (`-dbcache`) será respeitado quando o uso da memória alcance um pico durante o esvaziamento do cache.  A contabilidade de memória em versões anteriores é estimada apenas para metade da utilização real durante o pico.

O padrão `-dbcache` também foi alterado nesta versão para 450MiB. Usuários que atualmente definem `-dbcache` para um valor alto (por exemplo, para manter o UTXO completamente armazenados em cache na memória) devem considerar aumentar esta configuração para alcançar o mesmo desempenho de cache que em versões anteriores. Usuários em sistemas com baixa memória (como sistemas com 1GB ou menos) devem considerar especificar um valor menor para este parâmetro.

Informações adicionais sobre a execução em sistemas de baixa memória podem ser encontradas aqui, originalmente escrito para Bitcoin mas também pode ser usado para Criptoreal: [reducing-bitcoind-memory-usage.md](https://gist.github.com/laanwj/efe29c7661ce9b6620a7).

Créditos
========

Obrigado a todos os que contribuíram diretamente para esta versão:

- [Desenvolvedores Bitcoin Core](/doc/release-notes)
- Adrian Gallagher
- Charlie Lee
- Loshan T
- Shaolin Fry
- Xinxi Wang
