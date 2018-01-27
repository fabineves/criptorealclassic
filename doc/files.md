
* banlist.dat: armazena os IPs/Subnets de nós banidos
* criptoreal.conf: contém configurações para criptoreald ou criptoreal-qt
* criptoreald.pid: armazena o id do processo do criptoreald durante a execução
* blocks/blk000??.dat: dados de bloco (personalizado, 128 MiB por arquivo); desde 0.8.0
* blocks/rev000??.dat; desfaz o bloquear desfazer dados (personalizado); desde 0.8.0 (formato alterado desde pre-0.8)
* blocks/index/*; índice de blocos (LevelDB); since 0.8.0
* chainstate/*; bloqueia a base de dados da cadeia (LevelDB); desde 0.8.0
* database/*: Ambiente de banco de dados BDB; usado somente para carteira desde 0.8.0
* db.log: arquivo de registro do banco de dados da carteira
* debug.log: contém informações de depuração e registro geral gerado pelo criptoreald ou criptoreal-qt
* fee_estimates.dat: armazena estatísticas utilizadas para estimar as taxas mínimas de transação e as prioridades necessárias para a confirmação; desde 0.10.0
* mempool.dat: despejo das transações do mempool; desde 0.14.0.
* peers.dat: banco de dados do endereço IP (formato personalizado); since 0.7.0
* wallet.dat: carteira pessoal (BDB) com chaves e transações
* .cookie: cookie de autenticação da sessão RPC (escrito no início quanto a autenticação de cookies é usada, excluída no desligamento): desde 0.12.0
* onion_private_key: chave secreta do serviço oculto Tor para `-listenonion`: desde 0.12.0

Usado somente no pre-0.8.0
---------------------
* blktree/*; índice da cadeia de blocos (LevelDB); desde pre-0.8, substituído por blocks/index/* in 0.8.0
* coins/*; banco de dados de saídas não gastas (LevelDB); desde pre-0.8, substituído por chainstate/* in 0.8.0

Usado somente antes de 0.8.0
---------------------
* blkindex.dat: banco de dados da cadeia de blocos (BDB); substituído por {chainstate/*,blocks/index/*,blocks/rev000??.dat} em 0.8.0
* blk000?.dat: dados de blocos (personalizado, 2 GiB por arquivo); substituído por blocks/blk000??.dat em 0.8.0

Usado somente antes de 0.7.0
---------------------
* addr.dat: banco de dados de endereço IP (BDB); substitído por peers.dat in 0.7.0
