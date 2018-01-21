Política de tradução de strings
===========================

Este documento fornece diretrizes para a internacionalização do software Bitcoin Core.

Como traduzir?
------------------

Para marcar uma mensagem como traduzível

- No código fonte GUI (sob `src/qt`): use `tr("...")`

- No código fonte não GUI (sob `src`): use `_("...")`

Nenhuma internacionalização é usada para scripts de desenvolvedor fora do `src`.

Strings para serem traduzidas
-------------------------

Em um nível alto, estas strings devem ser traduzidas:

- Strings GUI, qualquer coisa que apareça em uma janela ou caixa de diálogo

- Documentação da opção da linha de comando

### Strings GUI

Qualquer coisa que apareça para o usuário na GUI deve ser traduzida. Isto inclui rótulos, itens do menu, textos dos botões, dicas de ferramentas e títulos de janelas. Isto inclui mensagens transmitidas para a GUI através da interface UI através de `InitMessage`, `ThreadSafeMessageBox` ou `ShowProgress`.

### Opções de linha de comando

A documentação para as opções de linha de comando na saída de `--help` também deve ser traduzida.

Certifique-se de que os valores padrão não terminam na string, mas use a formatação da string como `strprintf(_("Limite para desconectar peers com mal comportamento (default: %u)"), 100)`. Colocar valores padrão nas strings resultou em traduções acidentais no passado, e força a string a ser retransmitida sempre que o valor muda.

Não traduza mensagens que são mostrada somente para os desenvolvedores, como aquelas que aparecem apenas quando `--help-debug` é usado.

Recomendações Gerais
------------------------

### Evite traduções desnecessárias de strings

Tente não sobrecarregar os tradutores com traduções de mensagens que são, por exemplo, pequenas variações de outras mensagens. Na GUI, evite o uso de texto onde pode ser usado um ícone ou símbolo. Verifique se o texto dos formulários não termina na lista de strings a serem traduzidas (use `<string notr="true">`).

### Faça com que as strings traduzidas sejam compreensíveis

Tente escrever as strings de uma forma compreensível, tanto para o usuário quanto para o tradutor. Evite mensagens excessivamente técnicas ou detalhadas

### Não traduza erros internos

Não faça a tradução de erros internos, mensagens de log, ou mensagens que aparecem na interface RPC. Se um erro for mostrado ao usuário, use uma mensagem genérica que seja traduzível, então registre a mensagem detalhada no log. Por exemplo: Ocorreu um erro interno, veja debug.log para detalhes". Isto ajuda na resolução de problemas, se o erro for o mesmo para todos, a probabilidade é aumentada caso possa ser encontrada utilizando um mecanismo de pesquisa.

### Evite fragmentos

Evite dividir uma mensagem em fragmentos. Os tradutores vêem cada string separadamente, por isso mal entendemos o contexto se as mensagens não forem autônomas.

### Evite o HTML nas strings traduzidas

Houve dificuldades com o uso de HTML ao traduzir strings; tradutores não podem afetar acidentalmente a formatação de mensagens. Isto às vezes pode estar em conflito com a recomendação anterior.

### Plurais

Os plurais podem ser complexos em algumas línguas. Uma citação da documentação do gettext:

    Em polonês usamos e.g. plik (arquivo) desta forma:
    1 plik,
    2,3,4 pliki,
    5-21 pliko'w,
    22-24 pliki,
    25-31 pliko'w
    e por aí vai

No código Qt use o terceiro argumento do tr para a pluralidade opcional. Por exemplo:

    tr("%n hour(s)","",secs/HOUR_IN_SECONDS);
    tr("%n day(s)","",secs/DAY_IN_SECONDS);
    tr("%n week(s)","",secs/WEEK_IN_SECONDS);

Isto adciona `<numerusform>` no respectivo arquivo `.ts`, que pode ser traduzido separadamente dependendo do idioma. Em inglês, isto é simples:

    <message numerus="yes">
        <source>%n active connection(s) to Bitcoin network</source>
        <translation>
            <numerusform>%n active connection to Bitcoin network</numerusform>
            <numerusform>%n active connections to Bitcoin network</numerusform>
        </translation>
    </message>

Onde for possível, tente evitar a incorporação de números no fluxo da string. Por exemplo:

    ATENÇÃO: verifique sua conexão, %d blocos recebidos nas últimas %d horas (%d esperados)

versus

    ATENÇÃO: verifique sua conexão, menos blocos (%d) foram recebidos nas últimas %n horas do que o esperado (%d).

O segundo exemplo reduz o número de palavras pluralizadas que os tradutores têm de lidar de três para um, sem nenhum custo para a compreensã da sentença.

### Congelamento de strings

Durante um congelamento de strings (muitas vezes antes de um grande lançamento), nenhuma tradução de string deve ser adicionada, modificada ou removida.

Isto pode ser verificado executando `make translate` no diretório `src`, depeois verificando que `bitcoin_en.ts` continua inalterado.
