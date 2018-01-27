Traduções
=========

O projeto Bitcoin-Core foi designado para suportar múltiplas localizações. Isto torna a adição de frases e linguagens totalmente possível. Para gerenciar todas as traduções de aplicativos, o Bitcoin-Core faz uso da ferramente de gerenciamento de tradução online Transifex.

### Ajudando a traduzir (usando Transifex)
Transifex é configurado para monitorar o repositório GitHub procurando atualizações, e quando o código encontra novas traduções, Transifex irá processar qualquer alteração. Pode levar várias horas depois que um pull request for mesclado, para aparecer na interface web da Transifex.

O suporte a várias línguas é fundamental para ajudar a adoção global e crescimento do Bitcoin. Uma das maiores forças do Bitcoin, é realizar as transferências de dinheiro entre fronteiras. Qualquer ajuda para tornar isso mais fácil é sempre bem-vinda.

Veja [Projeto Transifex Bitcoin](https://www.transifex.com/projects/p/bitcoin/) para ter ajuda nas traduções. Você também deve participar da lista de discussão de tradução para saber as novidades - veja detalhes abaixo.

### Escrevendo código com traduções

Usamos scripts automatizados para ajudar a extrair traduções nos arquivos de origem Qt, e não Qt. Raramente é necessário editar manualmente os arquivos em `src/qt/locale/`. Os arquivos de origem da tradução devem aderir ao seguinte formato:
`bitcoin_xx_YY.ts ou bitcoin_xx.ts`

`src/qt/locale/bitcoin_en.ts` é tratado de uma maneira especial. É usado como fonte para todas as outras traduções. Sempre que uma string no código fonte é alterada, este arquivo deve ser atualizado para refletir estas alterações. Um script personalizado é usado para extrair cordas das partes não Qt. Este script faz uso do `gettext`, certifique-se que este utilitário está instalado (ex, `apt-get install gettext` no Ubuntu/Debian). Uma vez que isso foi atualizado, `lupdate` (incluído no Qt SDK) é usado para atualizar `bitcoin_en.ts`.

Para regenerar automaticamente o arquivo `bitcoin_en.ts`, execute o seguinte comando:
```sh
cd src/
make translate
```

`contrib/bitcoin-qt.pro` cuida da geração de arquivos `.qm` (compilado binário) a partir de arquivos `.ts` (arquivos fonte). É principalmente automatizado e você não precisa se preocupar com isso.

**Exemplo de tradução Qt**
```cpp
QToolBar *toolbar = addToolBar(tr("Tabs toolbar"));
```

### Criando uma pull-request
Para PRs gerais, você não deve incluir atualizações dos arquivos de origem da tradução. Eles serão atualizados periodicamente, principalmente em torno de pré-lançamentos, permitindo que o tempo para que novas frases sejam traduzidas antes dos lançamentos públicos. Isto também é importante para evitar conflitos de fusão relacionados à tradução.

Quando um arquivo de origem atualizado é incorporado no repositório do GitHub, a Transifex irá detectá-lo automaticamente (embora possa demorar várias horas). Uma vez processadas, as novas strings aparecerão como "Restantes" na interface web Transifex e estão prontas para os tradutores.

Para criar uma pull-request, use os seguintes comandos:
```
git add src/qt/bitcoinstrings.cpp src/qt/locale/bitcoin_en.ts
git commit
```

### Criando uma nova conta Transifex
Visite a página [Transifex](https://www.transifex.com/signup/) para criar uma conta. Guarde seu username e senha, uma vez que eles serão necessários para configurar a linha de comando.

Você pode encontrar o projeto de tradução Bitcoin em [https://www.transifex.com/projects/p/bitcoin/](https://www.transifex.com/projects/p/bitcoin/).

### Instalando a ferramenta de linha de comando do Transifex
O programa é utilizado para buscar traduções atualizadas. Se você estiver tendo problemas ou precisar de mais informações, veja [http://docs.transifex.com/developer/client/setup](http://docs.transifex.com/developer/client/setup)

**Para Linux e Mac**

`pip install transifex-client`

Configure o transifex da seguinte maneira. Por favor, *ignore o campo token*.

```ini
nano ~/.transifexrc

[https://www.transifex.com]
hostname = https://www.transifex.com
password = SENHA
token =
username = USERNAME
```

**Para Windows**

Por favor, veja [http://docs.transifex.com/developer/client/setup#windows](http://docs.transifex.com/developer/client/setup#windows) para detalhes na instalação.

O arquivo de configuração do projeto Transifex Bitcoin está incluído como parte do repositório. Pode ser encontrado em `.tx/config`. No entanto você não precisa fazer nenhuma alteração.

### Sincronizando traduções
Para ajudar na atualização de traduções, criamos um script para ajudar.

1. `python contrib/devtools/update-translations.py`
2. Atualizar `src/qt/bitcoin_locale.qrc` manualmente ou via
   `ls src/qt/locale/*ts|xargs -n1 basename|sed 's/\(bitcoin_\(.*\)\).ts/<file alias="\2">locale\/\1.qm<\/file>/'`
3. Atualizar `src/Makefile.qt.include` manualmente ou via
   `ls src/qt/locale/*ts|xargs -n1 basename|sed 's/\(bitcoin_\(.*\)\).ts/  qt\/locale\/\1.ts \\/'`
4. `git add` novas traduções de `src/qt/locale/`

**Não faça download direto das traduções** uma por uma do site Transifex, como fazemos algumas etapas pós-processamento antes de cometer as traduções.

### Manuseando Plurais (nos arquivos de origem)

Quando novos plurais são adicionados ao arquivo de origem, é importante fazer as seguintes etapas:

1. Abra `bitcoin_en.ts` no Qt Linguist (incluído no Qt SDK)
2. Procure por `%n`, que irá mostrar as partes da tradução que usam plurais
3. Procure por campos vazios `English Translation (Singular)` e `English Translation (Plural)` 
4. Adicione as sequências de caracteres apropriadas para a forma singular e plural da string base
5. Marque o item como feito (através do símbolo da seta verde na barra de ferramentas)
6. Repita a partir do passo 2, até que todos os singulares e plurais estejam no arquivo fonte
7. Salve o arquivo fonte

### Translating a new language

Para criar um novo modelo de idioma, você precisará editar o arquivo de manifesto de idiomas `src/qt/bitcoin_locale.qrc` e adicionar uma nova entrada. Abaixo está um exemplo da entrada na língua inglesa.

```xml
<qresource prefix="/translations">
    <file alias="en">locale/bitcoin_en.qm</file>
    ...
</qresource>
```

**Nota:** o arquivo de tradução do idioma **deve terminar em `.qm`** (a extensão compilada) e não `.ts`.

### Perguntas e assistência em geral

Os mantenedores da tradução Bitcoin-Core incluem *tcatm, seone, Diapolo, wumpus and luke-jr*. Você pode encontrá-los e também outros, na sala de chat IRC Freenode - `irc.freenode.net #bitcoin-core-dev`.

Se você é um tradutor, você também deve se inscrever na lista de email, https://groups.google.com/forum/#!forum/bitcoin-translators. Anúncios serão postados durante os pré-lançamentos do aplicativo para notificar os tradutores e verificar atualizações.
