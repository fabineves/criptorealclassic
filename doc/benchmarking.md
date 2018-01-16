Benchmarking
============

O Criptoreal Core possui uma estrutura de benchmarking interna, com benchmarks para algoritmos criptográficos como SHA1, SHA256, SHA512 e RIPEMD160. Assim como o filtro rolling bloom.

Após compilar o criptoreal-core, os benchmarks podem ser executados com:
`src/bench/bench_criptoreal`

O resultado será semelhante ao:
```
#Benchmark,count,min,max,average
RIPEMD160,448,0.001245033173334,0.002638196945190,0.002461894814457
RollingBloom-refresh,1,0.000635000000000,0.000635000000000,0.000635000000000
RollingBloom-refresh,1,0.000108000000000,0.000108000000000,0.000108000000000
RollingBloom-refresh,1,0.000107000000000,0.000107000000000,0.000107000000000
RollingBloom-refresh,1,0.000204000000000,0.000204000000000,0.000204000000000
SHA1,640,0.000909024336207,0.001938136418660,0.001843086257577
SHA256,256,0.002209486499909,0.008500099182129,0.004300644621253
SHA512,384,0.001319904176016,0.002813005447388,0.002615700786312
Sleep100ms,10,0.205592155456543,0.210056066513062,0.104166316986084
Trig,67108864,0.000000014997003,0.000000015448112,0.000000015188842
```

Mais benchmarks são necessários para, em nenhuma ordem em particular:
- Validação de Script
- Cache de CCoinDBView 
- Banco de dados de moedas
- Pool de memória
- Seleção da moeda da carteira
