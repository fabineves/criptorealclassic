# Transmissão de Blocos e Transações com ZeroMQ

[ZeroMQ](http://zeromq.org/) é um ivólucro em torno de conexões TCP, comunicação entre processos, e memória compartilhada, fornecendo várias semânticas orientadas para mensagens como publicar/assinar, requerer/responder, e push/pull.

O daemon do Criptoreal Core pode ser configurado para atuar como um "roteador de borda" confiável, implementando o protocolo do criptoreal, tomando decisões de consenso, mantendo o local do banco de dados da blockchain, transmitindo transações geradas localmente para a rede, e fornecendo uma interface RPC questionável para interagir em uma base de pesquisa para solicitar dados relacionados a blockchain. Entretanto, existe apenas um serviço limitado para notificar software externo sobre eventos como a chegada de novos blocos ou transações.

A instalação do ZeroMQ implementa uma interface de notificação através de um conjunto de notificadores específicos. Atualmente, existem notificadores que publicam blocos e transações. Esta facilidade que é somente leitura requer apenas a conexão de uma porta ZeroMQ correspondente na recepção do software; não é autenticado nem há envolvimento de protocolo bidirecional. Portanto, assinantes devem validar os dados recebidos pois estes podem estar desatualizados, incompletos e até inválidos.

Os soquetes do ZeroMQ são auto-conectados; ou seja, conexões feitas entre dois pontos serão automaticamente restauradas após uma interrupção, e qualquer extremidade pode ser livremente iniciada ou interrompida em qualquer ordem.

Como o ZeroMQ é orientado para mensagens, os assinantes recebem transações e bloqueiam tudo de uma vez e não há necessidade de implementar qualquer tipo de armazenamento ou remodelação.

## Pré-requisitos

O recurso ZeroMQ no Criptoreal Core requer a API ZeroMQ versão 4.x ou mais recente. Normalmente, é distribuído em distribuições como *libzmq3-dev*. The wrapper do C++ para o ZeroMQ  *não* é necessário.

Para executar o exemplo de scripts do cliente Python em contrib/ deve instalar também *python3-zmq*, embora isso não seja necessário para a operação daemon.

## Habilitando

Por padrão, o recurso ZeroMQ é compilado automaticamente se os pré-requisitos necessários forem encontrados. Para desabilitar, use --disable-zmq durante a etapa de *configuração* da compilação do criptoreald:

    $ ./configure --disable-zmq (outras opções)

Para ativar esta operação, é preciso definir as opções apropriadas na linha de comando ou no arquivo de configuração.

## Uso

Atualmente, as seguintes notificações são suportadas:

    -zmqpubhashtx=address
    -zmqpubhashblock=address
    -zmqpubrawblock=address
    -zmqpubrawtx=address

O tipo de soquete é PUB e o endereço deve ser um endereço de soquete ZeroMQ válido. O mesmo endereço pode ser usado em mais de uma notificação.

Por Exemplo:

    $ criptoreald -zmqpubhashtx=tcp://127.0.0.1:25527 \
               -zmqpubrawtx=ipc:///tmp/criptoreald.tx.raw

Cada notificação PUB tem um tópico e um corpo, onde o cabeçalho corresponde ao tipo de notificação. Por exemplo, para a notificação `-zmqpubhashtx` o tópico é `hashtx` (sem terminador nulo) e o corpo é o hash de transação hexadecimal (32 bytes).

Estas opções também podem ser fornecidas no criptoreal.conf.

Os especificadores de ponto de extremidade ZeroMQ para TCP (e outros) estão documentados em [ZeroMQ API](http://api.zeromq.org/4-0:_start).

O client, então, o soquete do assinante ZeroMQ deve ter a opção ZMQ_SUBSCRIBE definida para um ou qualquer destes prefixos (por exemplo, apenas `hash`); sem fazê-lo, fará com que nenhuma mensagem chegue. Veja `contrib/zmq/zmq_sub.py` para ver um exemplo funcional.

## Observações

Do ponto de vista do criptoreald, o soquete ZeroMQ é apenas de gravação; soquetes PUB sequer tem função de leitura. Assim, não há nenhum estado introduzido diretamente no criptoreald. Além disso, nenhuma informação é transmitida que não tenha sido recebida da rede pública P2P.

Nenhuma autenticação ou autorização é feita na conexão de clientes; assume-se que a porta ZeroMQ é exposta somente para entidades confiáveis, usando outros meios como firewall.

Observe que quando a ponta da blockchain muda, pode ocorrer uma reorganização e apenas a tip será notificada. Cabe ao assinante recuperar a cadeia do último bloco conhecido para a nova tip.

Existem várias possibilidades onde a notificação do ZMQ pode se perder durante a transmissão dependendo do tipo de comunicação que você está usando. Criptoreald anexa um número de sequência de contagem para cada notificação que permite aos ouvintes detectar notificações perdidas. 
