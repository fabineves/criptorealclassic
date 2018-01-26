Criptoreal Core 0.14.2
======================

Configuração
---------------------
Criptoreal Core é o cliente original de Criptoreal e constrói a espinha dorsal da rede. Entretanto, faz o download e armazena o histórico de todas as transações feitas em Criptoreal (que atualmente são vários GBs); dependendo da velocidade do seu computador e da sua conexão, o processo de sincronização pode ser feito em qualquer lugar levando desde algumas horas até um dia ou mais.

Para fazer o download do Criptoreal Core, visite [criptoreal.org](https://criptoreal.org).

Execução
---------------------
Abaixo estão algumas notas úteis para executar o Criptoreal Core na sua plataforma nativa.

### Unix

Extraia os arquivos em uma pasta e execute:

- `bin/criptoreal-qt` (GUI) ou
- `bin/criptoreald` (headless)

### Windows

Extraia os arquivos para uma pasta, depois execute o arquivo criptoreal-qt.exe.

### OS X

Arraste o arquivo Criptoreal-Core para sua pasta aplicativos, depois execute Criptoreal-Core.

### Precisa de ajuda?

* Veja a documentação na [Criptoreal Wiki](https://criptoreal.info/)
para ajuda e mais informações.
* Peça ajuda no canal [#criptoreal](http://webchat.freenode.net?channels=criptoreal) no Freenode. Se você não tem um cliente de IRC use [o webchat](http://webchat.freenode.net?channels=criptoreal).
* Peça ajuda nos fóruns [CriptorealTalk](https://criptorealtalk.io/).

Compilação
---------------------
Abaixo estão as notas dos desenvolvedores para compilar Criptoreal na sua plataforma nativa. Não são guias completos, mas incluem as notas mais importantes sobre as bibliotecas, compilação, etc.

- [Notas de compilação para OS X](build-osx.md)
- [Notas de compilação para Unix](build-unix.md)
- [Notas de compilação para Windows](build-windows.md)
- [Notas de compilação para OpenBSD](build-openbsd.md)
- [Guia de compilação Gitian](gitian-building.md)

Desenvolvimento
---------------------
O repositório [raiz README](/README.md) contém informação relevante no processo de desenvolvimento e testes automatizados.

- [Notas do desenvolvedor](developer-notes.md)
- [Notas de lançamento](release-notes.md)
- [Processo de lançamento](release-process.md)
- [Documentação do código fonte (link externo)](https://dev.visucore.com/criptoreal/doxygen/)
- [Processo de tradução](translation_process.md)
- [Política de tradução de strings](translation_strings_policy.md)
- [Travis CI](travis-ci.md)
- [Interface REST sem autenticação](REST-interface.md)
- [Bibliotecas compartilhadas](shared-libraries.md)
- [BIPS](bips.md)
- [Políticas Dnsseed](dnsseed-policy.md)
- [Benchmarking](benchmarking.md)

### Recursos
* Discuta no fórum [CriptorealTalk](https://criptorealtalk.io/).
* Discussão geral sobre o desenvolvimento de Criptoreal no canal #criptoreal-dev no Freenode. Se você não tem um cliente de IRC use o webchat [aqui](http://webchat.freenode.net/?channels=criptoreal-dev).

### Diversos
- [Atribuição de ativos](assets-attribution.md)
- [Arquivos](files.md)
- [Fuzz-testing](fuzzing.md)
- [Reduzir tráfego](reduce-traffic.md)
- [Suporte Tor](tor.md)
- [Scripts Init (systemd/upstart/openrc)](init.md)
- [ZMQ](zmq.md)

Licença
---------------------
Distribuido sob [a licença de software MIT](/COPYING).
Este produto inclui software desenvolvido pela OpenSSL Project para uso no [OpenSSL Toolkit](https://www.openssl.org/). Este produto inclui software criptografado escrito por Eric Young ([eay@cryptsoft.com](mailto:eay@cryptsoft.com)), e software UPnP escrito por Thomas Bernard.
