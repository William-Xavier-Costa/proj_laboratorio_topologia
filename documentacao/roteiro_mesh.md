# Roteiro de Testes: Topologia em Malha (Mesh) e Redundância Máxima com STP/RSTP

## 1. Visão Geral do Cenário

A topologia em malha (Mesh) é caracterizada pela interconexão redundante entre os nós da rede. Em uma malha total (*Full Mesh*), cada dispositivo se conecta a todos os outros; em uma malha parcial (*Partial Mesh*), os nós estratégicos possuem múltiplos caminhos entre si. O objetivo deste teste é explorar o nível máximo de **tolerância a falhas** e **disponibilidade**, observando como a rede converge instantaneamente e mantém o tráfego ativo mesmo com a destruição física de links principais.

## 2. Configuração Física no Packet Tracer

* **Ativos de Rede:** 4 Switches de Core/Distribuição (ex: Cisco Catalyst 2960) posicionados em formato de quadrado/losango:
  * `Switch-1` (Superior), `Switch-2` (Esquerda), `Switch-3` (Direita), `Switch-4` (Inferior).
* **Interconexões (A Malha):**
  * Conecte os switches das extremidades formando o quadrado: 1 com 2, 2 com 4, 4 com 3, 3 com 1.
  * Crie os caminhos redundantes cruzados (as diagonais): Conecte o 1 com 4, e o 2 com 3.
* **Hosts:** 2 Computadores (PC-A conectado ao Switch-2; PC-B conectado ao Switch-3).
* **Cabeamento:** Cabos diretos (Copper Straight-Through) para os computadores e cabos cruzados (ou automáticos) para a malha de switches.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/04-mesh.pkt`.
2. Como há múltiplos loops físicos intencionais, aguarde até 50 segundos para o **STP** bloquear os caminhos redundantes.
3. **Análise Visual:** Você notará que vários links ficarão com leds **laranja (Amber)**. O STP calculou o caminho mais curto e bloqueou as "diagonais" ou caminhos sobressalentes para evitar tempestades de broadcast.
4. Abra o terminal do **PC-A** e certifique-se de que ele consegue pingar o **PC-B** normalmente.

## 4. Passo a Passo do Teste de Estresse (Sobrevivência a Múltiplas Falhas)

### Passo 01: Iniciar Monitoramento Crítico

No **PC-A**, abra o *Command Prompt* e inicie um ping contínuo direcionado ao IP do **PC-B**:

```shell
PC-A> ping [IP_DO_PC-B] -t
```

*Monitore a latência inicial e certifique-se de que o fluxo está ativo.*

### Passo 02: O Primeiro Ataque – Cortando o Caminho Principal

1. Identifique visualmente por quais switches o ping está passando (aqueles cujos links estão totalmente **verdes** entre o Switch-2 e o Switch-3).
2. Selecione a ferramenta **Delete (tecla Del ou 'X' vermelho)**.
3. Corte o cabo principal ativo que une esses dois switches.
4. Volte imediatamente para o terminal do **PC-A**:

   * **Resultado Esperado:** Você verá pouquíssimas linhas de `Request timed out` (se o Packet Tracer estiver usando o RSTP, a queda pode ser de apenas 1 a 2 segundos).
   * Rapidamente, os links que estavam em laranja mudam para verde. O tráfego foi redirecionado automaticamente por outra rota da malha.

### Passo 03: O Segundo Ataque – Estressando a Redundância

1. Com o ping ainda rodando e já estabilizado no novo caminho, selecione novamente a ferramenta **Delete**.
2. Corte **mais um** cabo que esteja verde no novo trajeto que os pacotes estão utilizando.
3. Monitore o terminal do **PC-A**:
   * **Resultado Esperado:** Mais uma vez, o protocolo de Camada 2 encontra o terceiro caminho disponível na malha. O ping volta a responder. A rede sobreviveu a duas falhas consecutivas de infraestrutura de cabos.

## 5. Análise dos Resultados para o Relatório do GitHub

Adicione este fechamento crítico à sua documentação para demonstrar sua maturidade técnica:

* **Disponibilidade e Tolerância a Falhas:** Explique por que a topologia Mesh é considerada o padrão ouro para ambientes críticos (como Data Centers, backbones de Provedores de Internet - ISPs e Redes de Hospitais).
* **O Custo da Resiliência:** Qual é a principal desvantagem de se projetar uma topologia em Malha Completa em larga escala? *(Foco em orçamento: custo elevado com cabeamento e necessidade de switches com grande densidade de portas).*
* **Complexidade Administrativa:** À medida que adicionamos nós na malha, o que acontece com a tabela de roteamento/comutação e a complexidade de gerenciar essa infraestrutura?
