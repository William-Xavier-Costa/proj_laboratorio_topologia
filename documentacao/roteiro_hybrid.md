# Roteiro de Testes: Topologia Híbrida (Estrela-Malha) — O Padrão Corporativo

## 1. Visão Geral do Cenário

A topologia híbrida combina duas ou mais topologias puras para aproveitar as vantagens de cada uma e mitigar suas fraquezas. Neste laboratório, simulamos o modelo mais comum do mercado: **Estrela + Malha Parcial**. As estações de trabalho de departamentos diferentes são organizadas em Estrela (baixo custo e fácil expansão), enquanto os switches principais que interconectam esses departamentos formam uma Malha (alta redundância). O objetivo é demonstrar como o negócio mantém a operação mesmo se um departamento inteiro sofrer uma falha de link tronco.

## 2. Configuração Física no Packet Tracer

Para ilustrar a infraestrutura, dividiremos o cenário em duas partes na tela do simulador:

* **O Core Redundante (Malha):** 3 Switches de Core (ex: Catalyst 3650 ou 2960) conectados entre si em formato de triângulo (Switch-Core-A, Switch-Core-B, Switch-Core-C).
* **Os Departamentos (Estrelas):**
  * **Bloco Vendas:** 3 Computadores conectados individualmente em formato de estrela ao `Switch-Core-A`.
  * **Bloco Engenharia:** 3 Computadores conectados individualmente em formato de estrela ao `Switch-Core-B`.
* **Cabeamento:** Cabos diretos (Copper Straight-Through) para as estrelas dos computadores e cabos cruzados (ou automáticos) para fechar o triângulo da malha entre os Cores.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/06-hybrid.pkt`.
2. Aguarde os leds convergirem.
3. **Análise de Engenharia:** Observe que o STP/RSTP deixará uma das pernas do triângulo da malha (entre os switches de Core) em modo **Blocking (laranja)**. No entanto, todas as portas que descem para os computadores nos blocos de Vendas e Engenharia estarão **verdes**, já que a estrutura em estrela local não possui loops.
4. Teste o ping entre um computador de Vendas e um de Engenharia para validar a comunicação interdepartamental.

## 4. Passo a Passo do Teste de Estresse (O Equilíbrio da Alta Disponibilidade)

### Passo 01: Iniciar os Pings de Monitoramento Local e Remoto

Para entender o isolamento de falhas do modelo híbrido, abra dois terminais:
1.No **PC-Vendas-1**, inicie um ping contínuo para o **PC-Vendas-2** (Tráfego interno do departamento):

   ```shell
   PC-Vendas-1> ping [IP_DO_PC-VENDAS-2] -t
   ```

2.No **PC-Vendas-1**, abra outra aba e inicie um ping contínuo para o **PC-Engenharia-1** (Tráfego interdepartamental):

   ```shell
   PC-Vendas-1> ping [IP_DO_PC-ENGENHARIA-1] -t
   ```

### Passo 02: Simulação de Falha no Backbone (Links do Triângulo Core)

1. Selecione a ferramenta **Delete (tecla Del ou 'X' vermelho)** do Packet Tracer.
2. Corte o cabo principal ativo que liga o `Switch-Core-A` ao `Switch-Core-B`.
3. Verifique imediatamente o comportamento nos terminais de Vendas:
   * **Ping Local (Vendas-1 para Vendas-2):** Continua funcionando sem perder um único pacote. A falha na infraestrutura central não impacta o trabalho interno do departamento.
   * **Ping Remoto (Vendas-1 para Engenharia-1):** O tráfego sofrerá uma oscilação rápida (1 ou 2 pacotes perdidos), mas voltará a responder. O STP ativou o link reserva do triângulo, desviando o tráfego de Vendas pelo `Switch-Core-C` até chegar ao bloco de Engenharia.

### Passo 03: Simulação de Falha Isolada na Estrela

1. Recupere o link anterior (Ctrl + Z) e espere estabilizar.
2. Com os pings rodando, use a ferramenta **Delete** e corte o cabo de rede diretamente ligado à placa do **PC-Vendas-2**.
3. Veja o impacto: O ping para ele cai permanentemente, mas o tráfego entre o **PC-Vendas-1** e o bloco de Engenharia **permanece totalmente inalterado**.

## 5. Análise dos Resultados para o Relatório do GitHub

Conclua o último roteiro do seu trabalho analisando a viabilidade de negócios desta arquitetura:

* **Custo-Benefício:** Por que a topologia Híbrida é economicamente mais viável do que transformar a empresa inteira (incluindo todos os computadores de usuários) em uma Malha Completa (Full Mesh)?
* **Escalabilidade:** Se a empresa crescer e criar o departamento de "Recursos Humanos", como a topologia híbrida permite adicionar esse novo bloco sem mexer na estrutura dos blocos existentes?
* **Análise de Resiliência:** Resuma como a combinação de Estrela com Malha protegeu a empresa contra falhas locais e de backbone neste teste.
