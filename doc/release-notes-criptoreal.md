O Criptoreal Core versão 0.14.2 está disponível em:

  <https://criptoreal.org/>

Esta é uma nova versão, incluindo novos recursos, várias correções de bugs e melhorias de desempenho, assim como traduções atualizadas.

Por favor, reporte bugs usando o issue tracker do github:

  <https://github.com/criptoreal/criptoreal/issues>

Compatibilidade
==============

Criptoreal Core é amplamente testado em vários sistemas operacionais usando o kernel do Linux, macOS 10.8+, Windows Vista e posterior.

A Microsoft encerrou o suporte para Windows XP em [April 8th, 2014](https://www.microsoft.com/en-us/WindowsForBusiness/end-of-xp-support), nenhuma tentativa é feita para evitar a instalação ou a execução do software no Windows XP, você ainda pode faz^-lo por sua conta e risco mas fique ciente que existem instabilidades e problemas já conhecidos. Por favor não reporte problemas com o Windows XP no  issue tracker.

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

ZMQ On Windows
--------------

Previously the ZeroMQ notification system was unavailable on Windows
due to various issues with ZMQ. These have been fixed upstream and
now ZMQ can be used on Windows. Please see [this document](https://github.com/criptoreal/criptoreal/blob/master-0.14/doc/zmq.md) for
help with using ZMQ in general.

Nested RPC Commands in Debug Console
------------------------------------

The ability to nest RPC commands has been added to the debug console. This
allows users to have the output of a command become the input to another
command without running the commands separately.

The nested RPC commands use bracket syntax (i.e. `getwalletinfo()`) and can
be nested (i.e. `getblock(getblockhash(1))`). Simple queries can be
done with square brackets where object values are accessed with either an 
array index or a non-quoted string (i.e. `listunspent()[0][txid]`). Both
commas and spaces can be used to separate parameters in both the bracket syntax
and normal RPC command syntax.

Network Activity Toggle
-----------------------

A RPC command and GUI toggle have been added to enable or disable all p2p
network activity. The network status icon in the bottom right hand corner 
is now the GUI toggle. Clicking the icon will either enable or disable all
p2p network activity. If network activity is disabled, the icon will 
be grayed out with an X on top of it.

Additionally the `setnetworkactive` RPC command has been added which does
the same thing as the GUI icon. The command takes one boolean parameter,
`true` enables networking and `false` disables it.

Out-of-sync Modal Info Layer
----------------------------

When Criptoreal Core is out-of-sync on startup, a semi-transparent information
layer will be shown over top of the normal display. This layer contains
details about the current sync progress and estimates the amount of time
remaining to finish syncing. This layer can also be hidden and subsequently
unhidden by clicking on the progress bar at the bottom of the window.

Support for JSON-RPC Named Arguments
------------------------------------

Commands sent over the JSON-RPC interface and through the `criptoreal-cli` binary
can now use named arguments. This follows the [JSON-RPC specification](http://www.jsonrpc.org/specification)
for passing parameters by-name with an object.

`criptoreal-cli` has been updated to support this by parsing `name=value` arguments
when the `-named` option is given.

Some examples:

    src/criptoreal-cli -named help command="help"
    src/criptoreal-cli -named getblockhash height=0
    src/criptoreal-cli -named getblock blockhash=000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
    src/criptoreal-cli -named sendtoaddress address="(snip)" amount="1.0" subtractfeefromamount=true

The order of arguments doesn't matter in this case. Named arguments are also
useful to leave out arguments that should stay at their default value. The
rarely-used arguments `comment` and `comment_to` to `sendtoaddress`, for example, can
be left out. However, this is not yet implemented for many RPC calls, this is
expected to land in a later release.

The RPC server remains fully backwards compatible with positional arguments.

Sensitive Data Is No Longer Stored In Debug Console History
-----------------------------------------------------------

The debug console maintains a history of previously entered commands that can be
accessed by pressing the Up-arrow key so that users can easily reuse previously
entered commands. Commands which have sensitive information such as passphrases and
private keys will now have a `(...)` in place of the parameters when accessed through
the history.

Retaining the Mempool Across Restarts
-------------------------------------

The mempool will be saved to the data directory prior to shutdown
to a `mempool.dat` file. This file preserves the mempool so that when the node
restarts the mempool can be filled with transactions without waiting for new transactions
to be created. This will also preserve any changes made to a transaction through
commands such as `prioritisetransaction` so that those changes will not be lost.

GUI Changes
-----------

 - After resetting the options by clicking the `Reset Options` button 
   in the options dialog or with the `-resetguioptions` startup option, 
   the user will be prompted to choose the data directory again. This 
   is to ensure that custom data directories will be kept after the 
   option reset which clears the custom data directory set via the choose 
   datadir dialog.

 - Multiple peers can now be selected in the list of peers in the debug 
   window. This allows for users to ban or disconnect multiple peers 
   simultaneously instead of banning them one at a time.

 - An indicator has been added to the bottom right hand corner of the main
   window to indicate whether the wallet being used is a HD wallet. This
   icon will be grayed out with an X on top of it if the wallet is not a
   HD wallet.

Low-level RPC changes
----------------------

 - `importprunedfunds` only accepts two required arguments. Some versions accept
   an optional third arg, which was always ignored. Make sure to never pass more
   than two arguments.

 - The first boolean argument to `getaddednodeinfo` has been removed. This is 
   an incompatible change.

 - RPC command `getmininginfo` loses the "testnet" field in favor of the more
   generic "chain" (which has been present for years).

 - A new RPC command `preciousblock` has been added which marks a block as
   precious. A precious block will be treated as if it were received earlier
   than a competing block.

 - A new RPC command `importmulti` has been added which receives an array of 
   JSON objects representing the intention of importing a public key, a 
   private key, an address and script/p2sh

 - Use of `getrawtransaction` for retrieving confirmed transactions with unspent
   outputs has been deprecated. For now this will still work, but in the future
   it may change to only be able to retrieve information about transactions in
   the mempool or if `txindex` is enabled.

 - A new RPC command `getmemoryinfo` has been added which will return information
   about the memory usage of Criptoreal Core. This was added in conjunction with
   optimizations to memory management. See [Pull #8753](https://github.com/bitcoin/bitcoin/pull/8753)
   for more information.

 - A new RPC command `bumpfee` has been added which allows replacing an
   unconfirmed wallet transaction that signaled RBF (see the `-walletrbf`
   startup option above) with a new transaction that pays a higher fee, and
   should be more likely to get confirmed quickly.

 - The first positional argument of `createrawtransaction` was renamed from
  `transactions` to `inputs`.

 - The argument of `disconnectnode` was renamed from `node` to `address`.

Client software using these calls with named arguments needs to be updated.

HTTP REST Changes
-----------------

 - UTXO set query (`GET /rest/getutxos/<checkmempool>/<txid>-<n>/<txid>-<n>
   /.../<txid>-<n>.<bin|hex|json>`) responses were changed to return status 
   code `HTTP_BAD_REQUEST` (400) instead of `HTTP_INTERNAL_SERVER_ERROR` (500)
   when requests contain invalid parameters.

Minimum Fee Rate Policies
-------------------------

Since the changes in 0.13 to automatically limit the size of the mempool and improve the performance of block creation in mining code it has not been important for relay nodes or miners to set `-minrelaytxfee`. With this release the following concepts that were tied to this option have been separated out:
- calculation of threshold for a dust output. (effectively 3 * 1000 satoshis/kB)
- minimum fee rate of a package of transactions to be included in a block created by the mining code. If miners wish to set this minimum they can use the new `-blockmintxfee` option.  (defaults to 1000 satoshis/kB)

The `-minrelaytxfee` option continues to exist but is recommended to be left unset.

Fee Estimation Changes
----------------------

- Since 0.13.2 fee estimation for a confirmation target of 1 block has been
  disabled. The fee slider will no longer be able to choose a target of 1 block.
  This is only a minor behavior change as there was often insufficient
  data for this target anyway. `estimatefee 1` will now always return -1 and
  `estimatesmartfee 1` will start searching at a target of 2.

- The default target for fee estimation is changed to 6 blocks in both the GUI
  (previously 25) and for RPC calls (previously 2).

Removal of Priority Estimation
------------------------------

- Estimation of "priority" needed for a transaction to be included within a target
  number of blocks has been removed.  The RPC calls are deprecated and will either
  return -1 or 1e24 appropriately. The format for `fee_estimates.dat` has also
  changed to no longer save these priority estimates. It will automatically be
  converted to the new format which is not readable by prior versions of the
  software.

- Support for "priority" (coin age) transaction sorting for mining is
  considered deprecated in Core and will be removed in the next major version.
  This is not to be confused with the `prioritisetransaction` RPC which will remain
  supported by Core for adding fee deltas to transactions.

P2P connection management
--------------------------

- Peers manually added through the `-addnode` option or `addnode` RPC now have their own
  limit of eight connections which does not compete with other inbound or outbound
  connection usage and is not subject to the limitation imposed by the `-maxconnections`
  option.

- New connections to manually added peers are performed more quickly.

Introduction of assumed-valid blocks
-------------------------------------

- A significant portion of the initial block download time is spent verifying
  scripts/signatures.  Although the verification must pass to ensure the security
  of the system, no other result from this verification is needed: If the node
  knew the history of a given block were valid it could skip checking scripts
  for its ancestors.

- A new configuration option 'assumevalid' is provided to express this knowledge
  to the software.  Unlike the 'checkpoints' in the past this setting does not
  force the use of a particular chain: chains that are consistent with it are
  processed quicker, but other chains are still accepted if they'd otherwise
  be chosen as best. Also unlike 'checkpoints' the user can configure which
  block history is assumed true, this means that even outdated software can
  sync more quickly if the setting is updated by the user.

- Because the validity of a chain history is a simple objective fact it is much
  easier to review this setting.  As a result the software ships with a default
  value adjusted to match the current chain shortly before release.  The use
  of this default value can be disabled by setting -assumevalid=0

Fundrawtransaction change address reuse
----------------------------------------

- Before 0.14, `fundrawtransaction` was by default wallet stateless. In
  almost all cases `fundrawtransaction` does add a change-output to the
  outputs of the funded transaction. Before 0.14, the used keypool key was
  never marked as change-address key and directly returned to the keypool
  (leading to address reuse).  Before 0.14, calling `getnewaddress`
  directly after `fundrawtransaction` did generate the same address as
  the change-output address.

- Since 0.14, fundrawtransaction does reserve the change-output-key from
  the keypool by default (optional by setting  `reserveChangeKey`, default =
  `true`)

- Users should also consider using `getrawchangeaddress()` in conjunction
  with `fundrawtransaction`'s `changeAddress` option.

Unused mempool memory used by coincache
----------------------------------------

- Before 0.14, memory reserved for mempool (using the `-maxmempool` option)
  went unused during initial block download, or IBD. In 0.14, the UTXO DB cache
  (controlled with the `-dbcache` option) borrows memory from the mempool
  when there is extra memory available. This may result in an increase in
  memory usage during IBD for those previously relying on only the `-dbcache`
  option to limit memory during that time.

Mining
------

In previous versions, getblocktemplate required segwit support from downstream
clients/miners once the feature activated on the network. In this version, it
now supports non-segwit clients even after activation, by removing all segwit
transactions from the returned block template. This allows non-segwit miners to
continue functioning correctly even after segwit has activated.

Due to the limitations in previous versions, getblocktemplate also recommended
non-segwit clients to not signal for the segwit version-bit. Since this is no
longer an issue, getblocktemplate now always recommends signalling segwit for
all miners. This is safe because ability to enforce the rule is the only
required criteria for safe activation, not actually producing segwit-enabled
blocks.

UTXO memory accounting
----------------------

Memory usage for the UTXO cache is being calculated more accurately, so that
the configured limit (`-dbcache`) will be respected when memory usage peaks
during cache flushes.  The memory accounting in prior releases is estimated to
only account for half the actual peak utilization.

The default `-dbcache` has also been changed in this release to 450MiB.  Users
who currently set `-dbcache` to a high value (e.g. to keep the UTXO more fully
cached in memory) should consider increasing this setting in order to achieve
the same cache performance as prior releases.  Users on low-memory systems
(such as systems with 1GB or less) should consider specifying a lower value for
this parameter.

Additional information relating to running on low-memory systems can be found
here, originally written for Bitcoin but can also be used for Criptoreal:
[reducing-bitcoind-memory-usage.md](https://gist.github.com/laanwj/efe29c7661ce9b6620a7).

Credits
=======

Thanks to everyone who directly contributed to this release:

- [The Bitcoin Core Developers](/doc/release-notes)
- Adrian Gallagher
- Charlie Lee
- Loshan T
- Shaolin Fry
- Xinxi Wang
