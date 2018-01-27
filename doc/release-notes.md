Bitcoin Core versão 0.14.2 já está disponível em:

  <https://bitcoin.org/bin/bitcoin-core-0.14.2/>

Esta é uma versão menor, incluindo várias correções de bugs e melhorias de performance, assim como traduções atualizadas.

Por favor, informe os bugs usando o rastreador de problemas do github:

  <https://github.com/bitcoin/bitcoin/issues>

Para receber notificações sobre segurança e atualizaçãoes, cadastre-se em:

  <https://bitcoincore.org/en/list/announcements/join/>

Compatibilidade
===============

O Bitcoin Core é amplamente testado em vários sistemas operacionais usando o kernel do Linux, macOS 10.8+, Windows Vista e posterior.

Microsoft encerrou o suporte para Windows XP em [08 de Abril de 2014](https://www.microsoft.com/en-us/WindowsForBusiness/end-of-xp-support), Não há problema em tentar instalar o software no Windows XP, você pode fazê-lo por sua conta e risco, mas esteja ciente dos problemas e instabilidades conhecidos que irão ocorrer. Por favor, não reporte problemas sobre o Windows XP no rastreador de problemas.

Bitcoin Core também deve funcionar na maioria dos outros sistemas que são semelhantes a Unix, mas não existem testes frequentes neles.

Mudanças Notáveis
=================

miniupnp CVE-2018-8798
----------------------------

O miniupnpc foi atualizado para 2.0.20170509. Isto corrige um erro de assinatura de número inteiro (presente no MiniUPnPc v1.4.20101221 até a  v2.0) que permite que ataques remotos (dentro da LAN) causem uma negação de serviço ou possivelmente tenham outro impacto não especificado.

Isto afeta apenas usuários que ativaram explicitamente o UPnP através da configuração da GUI ou através da opção `-upnp`, uma vez que a última vulnerabilidade UPnP (no Bitcoin Core 0.10.3) foi desabilitada por padrão.

Se você usa esta opção, é recomendado atualizar para esta versão o mais rapidamente possível.

Bugs conhecidos
===============

Desde a versão 0.14.0, a taxa de transação aproximada mostrada no Bitcoin-Qt ao usar o controle de moeda e a estimativa de taxa inteligente não reflete qualquer alteração no alvo do controle de taxa inteligente. Apenas apresentará uma taxa aproximada calculada com o objetivo padrão. A taxa calculada usando o alvo correto ainda é aplicada a transação e mostrada na caixa de diálogo de confirmação do envio final.

0.14.2 Change log
=================

Seguem notas detalhadas das versões. Esta visão geral inclui mudanças que afetam o comportamento, não movimentos de código, refatores e updates de strings. Por conveniência em localizar as mudanças de código e a discussão que acompanha, tanto o pull request quanto o commit de fusão de git são mencionados.

### RPC e outras APIs
- #10410 `321419b` Corrigir o erro de redefinição do recurso importwallet (ryanofsky)

### P2P protocolo e código de rede
- #10424 `37a8fc5` Preenche serviços em GetLocalAddress (morcos)
- #10441 `9e3ad50` Apenas impõe serviços esperados para metade das conexões de saída (theuni)

### Compilação do Sistema
- #10414 `ffb0c4b` miniupnpc 2.0.20170509 (fanquake)
- #10228 `ae479bc` Regenera bitcoin-config.h conforme necessário (theuni)

### Diversos
- #10245 `44a17f2` Pequena correção na documentação de compilação para o FreeBSD 11 (shigeya)
- #10215 `0aee4a1` Verifica a interruptNet durante as pesquisas de dnsseed (TheBlueMatt)

### GUI
- #10231 `1e936d7` Reduz um congelamento significativo de bloqueio cs_main lock (jonasschnelli)

### Carteira
- #10294 `1847642` Desliga a posição de mudança quando não há alterações (instagibbs)

Créditos
=======

Obrigado a todos que contribuíram diretamente para o lançamento desta versão:

- Alex Morcos
- Cory Fields
- fanquake
- Gregory Sanders
- Jonas Schnelli
- Matt Corallo
- Russell Yanofsky
- Shigeya Suzuki
- Wladimir J. van der Laan

Além de todos que ajudaram a traduzir em [Transifex](https://www.transifex.com/projects/p/bitcoin/).

