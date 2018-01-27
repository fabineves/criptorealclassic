Travis CI
==========

O suporte para uso do travis-ci foi adicionado para automatizar o pull-testing.
Veja [travis-ci.org](https://travis-ci.org/) para mais informações

Este procedimento é diferente do pull-tester anterior em algumas formas.

Não há nada para administrar. Esta é uma característica importante pois assim as compilações não têm estado local. Como não há como se conectar aos pacotes de instalação dos compiladores (ferramentas, dependencias, etc), todo o processo de compilação deve ser controlado por um script declarativo `.travis.yml`.
Este script declara cada configuração da compilação, cria máquinas virtuais conforme a necessidade, compila e depois descarta estas máquinas virtuais.

Uma matriz de compilação é construída para testar uma vasta gama de configurações, ao invés de um único teste passou/falha. Isto ajuda a  encontrar falhas e erros lógicos de compilação que aparecem nas plataformas além daquelas que o autor já tenha testado. Esta matriz é definida no script de compilação e pode ser alterada a qualquer momento.

Todos os compiladores usam o gerador de dependência no [depends dir](/depends), ao invés de usar o apt-get para instalar as dependências de compilação. Isto garante que o testador está usado as mesmas versões do Gitian, assim os resultados da compilação são quase idênticos aos que seriam encontrados em uma versão final. Entretanto, isto também significa que as as novas compilações irão falhar se novas dependências forem introduzidas sem serem adicionadas no gerador de dependências.

Para evitar a reconstrução de todas as dependências para cada compilação, os binários são armazenados em cache e reusados quando possível. Mudanças no gerador de dependências podem desencadear invalidação do cache e fazer com que recompilações sejam necessárias.

Estes caches podem ser manualmente removidos se necessário. Esta é uma das poucas operações manuais que são permitidas com o Travis, e podem ser feitas pelo commiter do Criptoreal Core artavés da interface web do Travis.

Em alguns casos, strings seguras podem ser necessárias para esconder informações confidenciais como chaves privadas ou URLs. O cliente do Travis pode ser usado para criar estas strings:
http://docs.travis-ci.com/user/encryption-keys/

Para detalhes sobre a descrição da compilação, veja a documentação oficial:
http://docs.travis-ci.com/user/build-configuration/
