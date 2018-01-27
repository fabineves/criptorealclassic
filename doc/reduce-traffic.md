Reduzir Tráfego
===============

Alguns operadores de nó precisam lidar com limites de largura de banda impostos pelos seus ISPs.

Por padrão, criptoreal-core permite até 125 conexões para pares diferentes, 8 dos quais são de saída. Por conseguinte, é possível ter no máximo 117 ligações de entrada.

As configurações padrão podem resultar em um consumo de tráfego relativamente significativo.

Formas de reduzir o tráfego:

## 1. Use `-maxuploadtarget=<MiB per day>`

Um componente importante do tráfego é causado pela manutenção de blocos históricos para outros nós durante a fase de download de blocos iniciais (sincronizando um novo nó). Esta opção pode ser especificada em MiB por dia e é desativada por padrão. Isto é *não* é um limite difícil; é apenas para minimizar o tráfego de saída. Quando o limite está prestes a ser alcançado, os dados carregados são cortados por deixarem de servir blocos históricos (blocos com mais de uma semana). Tenha em mente que novos nós exigem outros nós que estão dispostos a servir blocos históricos.

Os pares que estão em 'Whitelisted' nunca serão desconectados, embora o tráfego cubra para calcular o alvo.

## 2. Desabilitar o "listening" (`-listen=0`)

Desabilitar o listening resultará em menos nós conectados (lembre-se do máximo de 8 pares de saída). Menos nós resultarão em menos uso de tráfego, pois você está transmitindo blocos e transações para menos nós.

## 3. Reduzir as conexões máximas (`-maxconnections=<num>`)

Reduzir o máximo de nós conectados ao mínimo poderia ser desejável, se os limites de tráfego forem pequenos. Tenha em mente que o modelo confiável do criptoreal funciona melhor se você estiver conectado a um punhado de nós.
