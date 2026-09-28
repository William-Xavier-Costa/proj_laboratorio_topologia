# Roteiro de Testes: Topologia em Anel (Ring) e o Protocolo STP

## 1. Visão Geral do Cenário

A topologia em anel conecta os switches em um circuito fechado. Fisicamente, isso cria um **loop de Camada 2**. Sem um protocolo de prevenção, a rede sofreria uma *tempestade de broadcast* (Broadcast Storm), travando os ativos. O objetivo deste teste é observar como o **STP (Spanning Tree Protocol)** gerencia esse anel para fornecer **redundância** sem derrubar a rede.

## 2. Configuração Física no Packet Tracer

* **Ativos de Rede:** 4 Switches (ex: Cisco Catalyst 2960).
* **Hosts:** 2 Computadores (PC-A conectado ao Switch 1; PC-B conectado ao Switch 3).
* **Cabeamento:** Cabos diretos (Copper Straight-Through) para os PCs e cabos cruzados (Copper Cross-Over) ou automáticos para interconectar os switches formando um quadrado/anel.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/02-ring.pkt`.
2. Aguarde cerca de 50 segundos até que todos os links parem de piscar em laranja.
3. **Observação Visual:** Note que um dos links entre os switches terá uma das pontas com um led **laranja (Amber)**. Isso indica que o STP colocou aquela porta no estado de **Blocking (Bloqueio)** para romper o loop.
4. Entre na CLI do switch que possui a porta laranja e execute:

   ```shell
   Switch# show spanning-tree
   ```

   *Identifique qual switch foi eleito o **Root Bridge** (todas as portas dele estarão como `DESG / FWD`) e qual porta está como `ALTN / BLK` (Bloqueada).*

## 4. Passo a Passo do Teste de Estresse (Tolerância a Falhas)

### Passo 01: Teste de Conectividade em Par de Linhas Saudável

No **PC-A**, abra o *Command Prompt* e inicie um ping contínuo para o IP do **PC-B**:

```shell
PC-A> ping [IP_DO_PC-B] -t
```

*Certifique-se de que os pacotes estão recebendo respostas estáveis e com baixa latência.*

### Passo 02: Simulação de Falha no Link Ativo

1. Com o ping ainda rodando, selecione a ferramenta **Delete (tecla Del ou o ícone de 'X' vermelho)** no menu superior do Packet Tracer.
2. Corte um dos cabos que está com os leds **verdes** (o caminho principal que o tráfego está utilizando).
3. **Atenção:** Não corte o cabo que já estava com o led laranja.

### Passo 03: Monitoramento da Convergência

1. Volte imediatamente para a tela do *Command Prompt* do **PC-A**.
2. Você observará que o ping parou de responder, exibindo mensagens de `Request timed out`.
3. Olhe para o link que antes estava bloqueado (laranja). Ele começará a piscar em laranja (transitando pelos estados de *Listening* e *Learning* do STP) até ficar **verde (Forwarding)**.
4. Assim que o led ficar verde, as mensagens de `Reply from...` voltarão a aparecer no terminal do PC-A.

## 5. Análise dos Resultados para o Relatório

* **Quantos pacotes foram perdidos** durante a queda do link? (Conte o número de linhas de *Request timed out*).
* Como o tempo padrão do protocolo STP clássico (cerca de 30 a 50 segundos) impacta a **disponibilidade** de uma rede em produção?
* **Conclusão técnica:** A topologia em anel oferece redundância? Sim. Ela possui tolerância a falhas imediata? Não, ela depende do tempo de convergência do protocolo de Camada 2 para restabelecer o fluxo.
