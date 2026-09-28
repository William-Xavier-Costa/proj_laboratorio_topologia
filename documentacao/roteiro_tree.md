# Roteiro de Testes: Topologia em Árvore (Tree) e o Design Hierárquico de Redes

## 1. Visão Geral do Cenário

A topologia em árvore estrutura a rede em uma hierarquia de nós. Em ambientes corporativos modernos, isso se traduz no modelo de três camadas da Cisco: **Core (Núcleo), Distribuição e Acesso**. O objetivo deste teste é compreender como a **escalabilidade** se torna extremamente eficiente nesta topologia e analisar o impacto de falhas em cascata: quanto mais alto o nível do dispositivo que falha na árvore, maior é a ramificação de rede afetada.

## 2. Configuração Física no Packet Tracer

Para simular a hierarquia, organize os switches verticalmente na tela do simulador:

* **Camada de Core (Raiz):** 1 Switch Central (ex: Cisco Catalyst 3650 ou 2960 de alta performance).
* **Camada de Distribuição (Galhos Principais):** 2 Switches (Switch-Dist-Esq e Switch-Dist-Dir) conectados diretamente ao Switch de Core.
* **Camada de Acesso (Folhas):** 4 Switches conectados aos pares sob os switches de distribuição:
  * `Switch-Acesso-1` e `Switch-Acesso-2` conectados ao `Switch-Dist-Esq`.
  * `Switch-Acesso-3` e `Switch-Acesso-4` conectados ao `Switch-Dist-Dir`.
* **Hosts:** 4 Computadores (PC-A no Acesso 1, PC-B no Acesso 2, PC-C no Acesso 3 e PC-D no Acesso 4).
* **Cabeamento:** Cabos diretos para os computadores e cabos cruzados (ou automáticos) para interconectar as camadas dos switches de forma hierárquica.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/05-tree.pkt`.
2. Aguarde os leds convergirem para a cor **verde**.
3. *Nota de Engenharia:* Como a árvore se ramifica sem fechar circuitos (não há caminhos alternativos entre os galhos nesta configuração básica), o **STP** não precisará bloquear nenhuma porta tronco de interconexão.
4. Execute um ping de teste entre o **PC-A** (extrema esquerda) e o **PC-D** (extrema direita) para confirmar a conectividade atravessando toda a árvore.

## 4. Passo a Passo do Teste de Estresse (Falhas Hierárquicas em Cascata)

### Passo 01: Estabelecer Fluxos Simultâneos de Dados

Abra os terminais de prompt de comando dos computadores abaixo e inicie pings contínuos para criar tráfego cruzado entre as ramificações:
1.No **PC-A**, inicie o ping para o **PC-B** (mesmo switch de distribuição, galhos vizinhos):

   ```shell
   PC-A> ping [IP_DO_PC-B] -t
   ```

2.No **PC-C**, inicie o ping para o **PC-D** (mesmo switch de distribuição, galhos vizinhos):

   ```shell
   PC-C> ping [IP_DO_PC-D] -t
   ```

3.No **PC-A**, abra uma nova aba e inicie também um ping para o **PC-C** (ramificações opostas, exige passar pelo Core):

   ```shell
   PC-A> ping [IP_DO_PC-C] -t
   ```

### Passo 02: Simulação de Falha na Camada de Distribuição (Corte de um Galho)

1. Selecione a ferramenta **Delete (tecla Del ou 'X' vermelho)**.
2. Corte o cabo que liga o `Switch-Dist-Esq` ao `Switch de Core`.
3. Volte rapidamente para os terminais e observe:
   * **PC-A para PC-C:** O ping **falhou** instantaneamente. A ramificação esquerda perdeu a comunicação com a raiz (Core) e, consequentemente, não alcança o lado direito.
   * **PC-C para PC-D:** O ping **continua funcionando perfeitamente**. A falha em um galho da árvore não afetou a integridade e a disponibilidade das sub-ramificações do lado direito.
   * **PC-A para PC-B:** O ping **continua funcionando**. Como eles compartilham o mesmo switch de distribuição, o tráfego local não precisa subir até o Core.

### Passo 03: Simulação de Falha na Camada de Core (Queda da Raiz)

1. Dê um `Ctrl + Z` para restaurar o cabo anterior e espere a rede reestabelecer (leds verdes).
2. Agora, utilize a ferramenta **Delete** para excluir diretamente o **Switch de Core (Raiz da árvore)**.
3. Analise o impacto global:
   * O fluxo de **PC-A para PC-C** cai definitivamente. Sem a raiz, nenhuma ramificação principal se comunica com a outra.
   * Os fluxos locais (**PC-A para PC-B** e **PC-C para PC-D**) **sobrevivem**. Isso prova que a topologia em árvore consegue isolar o impacto total em níveis locais.

## 5. Análise dos Resultados para o Relatório do GitHub

Enriqueça sua postagem respondendo a estes critérios no seu `README`:

* **Escalabilidade:** Por que a estrutura em árvore facilita a expansão da rede (ex: adicionar um novo prédio de escritórios inteiro) sem a necessidade de reestruturar o miolo central?
* **Tolerância a Falhas:** Qual camada representa o maior risco de disponibilidade se falhar?
* **Mitigação com Redundância:** Como solucionar o problema da perda de um switch de Distribuição ou Core em redes reais? *(Menção obrigatória no portfólio: Criação de links redundantes em malha conectando as camadas e ativação de protocolos como o Rapid Spanning Tree - RSTP para evitar loops).*
