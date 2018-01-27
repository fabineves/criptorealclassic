Bibliotecas Compartilhadas
================

## consenso do bitcoin

O propósito desta biblioteca é fazer com que a verificação da funcionalidade que é crítica para o consenso do Bitcoin, fique disponível para outras aplicações, e.x. para ligações de idiomas.

### API

A interface é definida no cabeçalho C `bitcoinconsensus.h` localizado em  `src/script/bitcoinconsensus.h`.

#### Versão

`bitcoinconsensus_version` retorna um `unsigned int` com a versão da API *(atualmente em um experimento `0`)*.

#### Validação do Script

`bitcoinconsensus_verify_script` retorna um `int` com o status da verificação. Será `1` se o script de entrada gastar a saída anterior `scriptPubKey`.

##### Parâmetros
- `const unsigned char *scriptPubKey` - O script de saída anterior que não consegue ser gasto.
- `unsigned int scriptPubKeyLen` - O número de bytes para o `scriptPubKey`.
- `const unsigned char *txTo` - A transação com a entrada que está gastando a saída anterior.
- `unsigned int txToLen` - O número de bytes para o `txTo`.
- `unsigned int nIn` - O índice de entrada em `txTo` que gasta o `scriptPubKey`.
- `unsigned int flags` - Os sinalizadores de validação do script *(veja abaixo)*.
- `bitcoinconsensus_error* err` - Terá o código de erro/sucesso para a operação *(veja abaixo)*.

##### Script Flags (Sinalizadores de Script)
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_NONE`
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_P2SH` - Avalia os subscritos P2SH ([BIP16](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki)) 
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_DERSIG` - Reforça rigorosamente a conformidade DER ([BIP66](https://github.com/bitcoin/bips/blob/master/bip-0066.mediawiki)) 
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_NULLDUMMY` - Reforça NULLDUMMY ([BIP147](https://github.com/bitcoin/bips/blob/master/bip-0147.mediawiki))
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_CHECKLOCKTIMEVERIFY` - Habilita CHECKLOCKTIMEVERIFY ([BIP65](https://github.com/bitcoin/bips/blob/master/bip-0065.mediawiki))
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_CHECKSEQUENCEVERIFY` - Habilita CHECKSEQUENCEVERIFY ([BIP112](https://github.com/bitcoin/bips/blob/master/bip-0112.mediawiki))
- `bitcoinconsensus_SCRIPT_FLAGS_VERIFY_WITNESS` - Habilita WITNESS ([BIP141](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki))

##### Erros
- `bitcoinconsensus_ERR_OK` - Nenhum erro com os parâmetros de entrada *(veja retorno do valor de `bitcoinconsensus_verify_script` para a verificação do status)*
- `bitcoinconsensus_ERR_TX_INDEX` - Um índice inválido para `txTo`
- `bitcoinconsensus_ERR_TX_SIZE_MISMATCH` - `txToLen` não coincidiu com o tamanho de `txTo`
- `bitcoinconsensus_ERR_DESERIALIZE` - Um erro de desserialização `txTo`
- `bitcoinconsensus_ERR_AMOUNT_REQUIRED` - O valor de entrada é necessário se WITNESS é usado

### Implementações de exemplo
- [NBitcoin](https://github.com/NicolasDorier/NBitcoin/blob/master/NBitcoin/Script.cs#L814) (Ligações .NET)
- [node-libbitcoinconsensus](https://github.com/bitpay/node-libbitcoinconsensus) (Ligações Node.js)
- [java-libbitcoinconsensus](https://github.com/dexX7/java-libbitcoinconsensus) (Ligações Java)
- [bitcoinconsensus-php](https://github.com/Bit-Wasp/bitcoinconsensus-php) (Ligações PHP)
