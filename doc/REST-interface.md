Interface REST sem autenticação
==============================

A API REST pode ser ativada com a opção `-rest`.

A interface roda na mesma porta que a interface JSON-RPC, por padrão é designada a porta 8332 para mainnet e a porta 18332 para testnet.

API Suportada
-------------

####Transações
`GET /rest/tx/<TX-HASH>.<bin|hex|json>`

Dada uma transação hash: retorna uma transação binária, binária codificada como hexadecimal ou em JSON.

Para uma consulta TX, deve habilitar o índice da transação via "txindex=1" linha de comando / opções de configuração.

####Blocos
`GET /rest/block/<BLOCK-HASH>.<bin|hex|json>`
`GET /rest/block/notxdetails/<BLOCK-HASH>.<bin|hex|json>`

Dado um bloco hash: retorna um bloco binário, binário hexadecimal sem codificação ou em JSON.

Tanto a chamada HTTP e a resposta são tratadas totalmente na memória, com o uso máximo de pelo menos 2.66MB (1 MB no máximo por bloco, mais a codificação hexadecimal) por pedido.

Com a opção /notxdetails/ a resposta JSON irá conter somente a transação hash ao invés de todos os detalhes da transação. Esta opção afeta somente a resposta JSON.

####Blockheaders
`GET /rest/headers/<COUNT>/<BLOCK-HASH>.<bin|hex|json>`

Dado um bloco hash: retorna <COUNT> uma quantidade de cabeças de bloco em uma direção ascendente.

####Chaininfos
`GET /rest/chaininfo.json`

Retorna várias informações do estado do processamento da cadeia de blocos.
Suporta somente JSON como formato de saída.
* chain : (string) nome atual da rede como definido em BIP70 (main, test, regtest)
* blocks : (numerico) número atual de blocos processados no servidor
* headers : (numerico) número atual do cabeçalho que foi validado anteriormente
* bestblockhash : (string) o hash do melhor bloco atual
* difficulty : (numerico) a dificuldade atual
* verificationprogress : (numerico) estimativa do progresso da verificação [0..1]
* chainwork : (string) quantidade total de trabalho na cadeia ativa, em hexadecimal
* pruned : (boolean) se os blocos estão sujeitos a cortes
* pruneheight : (numerico) bloco mais alto disponível
* softforks : (array) status de softforks em andamento

####Configuração UTXO da consulta
`GET /rest/getutxos/<checkmempool>/<txid>-<n>/<txid>-<n>/.../<txid>-<n>.<bin|hex|json>`

O comando getutxo permite consultar o conjunto UTXO dado uma série de outpoints.
Veja BIP64 para serialização de entrada e saída:
https://github.com/bitcoin/bips/blob/master/bip-0064.mediawiki

Exemplo:
```
$ curl localhost:15527/rest/getutxos/checkmempool/b2cdfd7b89def827ff8af7cd9bff7627ff72e5e8b0f71210f92ea7a4000c5d75-0.json 2>/dev/null | json_pp
{
   "chaintipHash" : "00000000fb01a7f3745a717f8caebee056c484e6e0bfe4a9591c235bb70506fb",
   "chainHeight" : 325347,
   "utxos" : [
      {
         "scriptPubKey" : {
            "addresses" : [
               "mi7as51dvLJsizWnTMurtRmrP8hG2m1XvD"
            ],
            "type" : "pubkeyhash",
            "hex" : "76a9141c7cebb529b86a04c683dfa87be49de35bcf589e88ac",
            "reqSigs" : 1,
            "asm" : "OP_DUP OP_HASH160 1c7cebb529b86a04c683dfa87be49de35bcf589e OP_EQUALVERIFY OP_CHECKSIG"
         },
         "value" : 8.8687,
         "height" : 2147483647,
         "txvers" : 1
      }
   ],
   "bitmap" : "1"
}
```

####Pool de memória
`GET /rest/mempool/info.json`

Retorna várias informações sobre o pool de memória TX.
Suporta somente JSON como formato de saída.
* size : (numeric) o número de transações no pool de memória TX 
* bytes : (numeric) tamanho do pool de memória TX mempool em bytes
* usage : (numeric) uso total da memória TX
`GET /rest/mempool/contents.json`

Retorna transações no pool de memória TX.
Suporta somente JSON como formato de saída.

Riscos
-------------
Executar um navegador web no mesmo node com um criptoreald habilitado para REST pode ser um risco. O acesso a sites XSS preparados, pode ler dados tx/block do seu nó, colocando links como `<script src="http://127.0.0.1:5527/rest/tx/1234567890.json">` que pode levar à quebra da privacidade dos nós.
