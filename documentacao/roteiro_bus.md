# Roteiro de Testes: Topologia em Barramento (Bus) e a Partição do Meio Compartilhado

## 1. Visão Geral do Cenário

A topologia em barramento (Bus) utiliza um único cabo central (tronco) ao qual todos os nós são conectados diretamente ou por meio de derivações. Historicamente implementada com cabos coaxiais e terminadores resistivos, hoje a simulamos no Packet Tracer conectando switches em cascata linear ou usando Hubs. O objetivo deste teste é compreender a fragilidade do meio compartilhado e o efeito de **partição da rede** quando ocorre uma falha no cabo central.

## 2. Configuração Física no Packet Tracer

* **Ativos de Rede:** 4 Switches (ex: Cisco Catalyst 2960) conectados de forma linear (Switch1 --- Switch2 --- Switch3 --- Switch4).
* **Hosts:** 4 Computadores distribuídos pelas extremidades:
  * **PC-A** conectado ao Switch 1
  * **PC-B** conectado ao Switch 2
  * **PC-C** conectado ao Switch 3
  * **PC-D** conectado ao Switch 4
* **Cabeamento:** Cabos diretos (Copper Straight-Through) para os PCs e cabos cruzados (Copper Cross-Over) para interconectar os switches em linha reta.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/01-bus.pkt`.
2. Aguarde até que todos os links fiquem **verdes**.
3. *Nota teórica:* Diferente da topologia em anel, aqui o **STP (Spanning Tree Protocol)** não bloqueará nenhuma porta entre os switches, pois não existe um circuito fechado (loop físico).
4. Verifique a conectividade global realizando um teste de ping de ponta a ponta: do **PC-A** (extrema esquerda) para o **PC-D** (extrema direita).

## 4. Passo a Passo do Teste de Estresse (O Rompimento do Tronco)

### Passo 01: Iniciar Monitoramento de Tráfego Cruzado

Para visualizar o impacto da divisão física da rede, abra dois terminais simultaneamente:
1.No **PC-A**, abra o *Command Prompt* e inicie um ping contínuo para o **PC-D**:

```shell
   PC-A> ping [IP_DO_PC-D] -t
```

2.No **PC-B**, abra o *Command Prompt* e inicie um ping contínuo para o **PC-C**:

```shell
   PC-B> ping [IP_DO_PC-C] -t
```

*Certifique-se de que ambos os fluxos de pacotes estão funcionando normalmente.*

### Passo 02: Simulação de Rompimento do Cabo Central

1. Selecione a ferramenta **Delete (tecla Del ou 'X' vermelho)** no menu superior do Packet Tracer.
2. Corte o cabo que interconecta o **Switch 2** ao **Switch 3** (este cabo representa o centro do barramento principal).

### Passo 03: Análise do Efeito de Partição (Segmentação)

Volte imediatamente aos terminais dos computadores e observe o comportamento assimétrico da rede:

* **Resultado no PC-A (tentando falar com PC-D):**  O ping começará a falhar continuamente (`Request timed out`). Como o barramento foi partido, a rede foi dividida em dois "sub-barramentos" isolados. O PC-A ficou no segmento da esquerda e o PC-D no segmento da direita.  

* **Resultado no PC-B (tentando falar com PC-C):** Também falhará imediatamente pelo mesmo motivo de isolamento geográfico dos segmentos.

### Passo 04: Teste de Resiliência Local (O que sobreviveu?)

No **PC-A**, pare o ping atual (`Ctrl + C`) e tente pingar o **PC-B** (que está no mesmo segmento remanescente da esquerda):

```shell
PC-A> ping [IP_DO_PC-B]
```

* **Resultado Esperado:** O ping responderá com **sucesso**. Isso prova que, apesar da falha grave no tronco, a comunicação dentro do mesmo segmento sobrevivente ainda é possível.

## 5.Análise dos Resultados para o Relatório do GitHub

Adicione essas reflexões técnicas na documentação do seu repositório:

* **Tolerância a Falhas:** Qual é o impacto de uma quebra de cabo na extremidade (ex: cabo do PC-A) comparado a uma quebra no meio do tronco (ex: entre Switch 2 e 3)?

* **Disponibilidade:** Por que a topologia em Barramento caiu em desuso em redes corporativas modernas?

* **Comparativo com a Estrela:** No Barramento, o rompimento de um cabo central isola metade da rede da outra metade. Na Estrela, o rompimento de um cabo de host isola apenas aquele host. Qual das duas arquiteturas mitiga melhor o impacto de falhas de cabeamento?
