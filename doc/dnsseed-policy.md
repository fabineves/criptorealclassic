Expectativas para operadores de DNS Seed
========================================

O Criptoreal Core tenta minimizar o nível de confiança nos DNS seeds, mas os DNS seeds ainda representam uma pequena quantidade de risco para a rede. Como tal, os DNS seeds devem ser geridas por entidades que possuem algum nível mínimo de confiança na comunidade Criptoreal.

Outras implementações do software Criptoreal também podem usar as mesmas sementes e podem estar ainda mais expostas. Este documento estabelece algumas expectativas básicas para a operação de dnsseeds.

0. Uma organização ou uma pessoa operando o DNS seed deve seguir boas práticas de segurança, manter o controle de infra-estrutura aplicável,e não vender ou transferir o controle de nenhuma DNS seed. Todos os serviços de hospedagem contratados pelo operador devem manter estas expectativas.

1. Os resultados da DNS seed devem consistir exclusivamente em nodes Criptoreal selecionados da rede pública para o melhor entendimento e capacidade do operador.

2. Para evitar dúvidas, os resultados podem ser randômicos, mas não devem separar nenhum grupo para receber resultados diferentes, a menos que tenha havido uma necessidade técnica urgente.

3. Os resultados podem não ser atendidos com um DNS TTL de menos de um minuto.

4. Qualquer registro de consulta DNS deve ser apenas o necessário para a operação do serviço ou para a saúde urgente da rede Criptoreal e não deve durar mais que o necessário nem divulgado a terceiros.

5. As informações obtidas como resultado dos operadores node-spidering (não de consultadas DNS) podem ser livremente publicadas ou então retidas, mas somente se estes dados não foram obtidos através conectividade biasing node (uma violação de expectativas (1)).

6. Operadores são encorajados, mas não obrigados, a documentar publicamente os detalhes das suas práticas operacionais.

7. Um endereço de email acessível deve ser publicado para consultas relacionadas a operações de DNS Seed.

Se o operador não estiver de acordo com estas regras, ele deve interromper a prestação de serviços e entrar em contato com o time de desenvolvedores do Criptoreal Core assim como publicar no [criptoreal-dev](https://groups.google.com/forum/#!forum/criptoreal-dev).

O comportamento fora destas expectativas pode ser aceito em algumas situações, mas deve ser discutida em público e com antecedência.

Veja também
----------
- [criptoreal-seeder](https://github.com/pooler/criptoreal-seeder) é uma referência de implementação de um DNS seed.
