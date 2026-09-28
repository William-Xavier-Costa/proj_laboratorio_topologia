# proj_laboratório_topologias
Laboratório packet - tracer para analisar de forma empírica a topologia física e lógica de uma infraestrutura de redes. 
# Análise de Topologias de Rede: Resiliência, Escalabilidade e Tolerância a Falhas no Cisco Packet Tracer

## 1. Identificação do Projeto

* **Curso:** Tecnologia em Redes de Computadores
* **Disciplina:** Arquitetura e Topologias de Redes / Laboratório de Redes
* **Autor:** William Xavier da Costa
* **Objetivo:** Analisar de forma prática e comparativa o impacto de seis topologias de rede distintos (**Bus, Ring, Star, Mesh, Tree e Hybrid**) frente a quatro pilares fundamentais da infraestrutura de TI: **Redundância, Disponibilidade, Escalabilidade e Tolerância a Falhas**.

---

## 2. Orientação Técnica E Estrutura dos Laboratórios

Este repositório contém os arquivos `.pkt` correspondentes a cada cenário testado no Cisco Packet Tracer (versão 8.2 ou superior recomendada).

### Cenários Projetados

Para fins de padronização, cada topologia deve interconectar um mínimo de **5 a 8 nós (hosts/switches)**, permitindo os testes de estresse.

#### A. Topologia em Barramento (Bus)

* **Implementação:** Conexão linear utilizando switches em cascata ou hubs simulando o meio compartilhado.
* **Foco do Teste:** Simular a quebra do "cabo principal" (mídia de transmissão) e observar a partição da rede.

#### B. Topologia em Anel (Ring)

* **Implementação:** Conexão circular entre switches.
* **Foco do Teste:** Configuração e comportamento do protocolo
* **STP (Spanning Tree Protocol)**. O que acontece quando o link principal é cortado? Quanto tempo leva a convergência?

#### C. Topologia em Estrela (Star)

* **Implementação:** Dispositivos finais conectados a um Switch Central (Core).
* **Foco do Teste:** Simular a falha do Switch Central (**Ponto Único de Falha / Single Point of Failure - SPOF**).

#### D. Topologia em Malha (Mesh)

* **Implementação:** Configuração de Malha Total (Full Mesh) ou Parcial (Partial Mesh) entre os ativos de rede.
* **Foco do Teste:** Análise de múltiplos caminhos ativos. Derrubar dois links simultâneos e verificar se o tráfego de pacotes (ICMP/Ping) continua fluindo.

#### E. Topologia em Árvore (Tree)

* **Implementação:** Estrutura hierárquica dividida em camadas (Core, Distribuição e Acesso).
* **Foco do Teste:** Rompimento de um switch de Distribuição afetando todo o segmento de Acesso abaixo dele.

#### F. Topologia Híbrida (Hybrid)

* **Implementação:** Combinação da topologia Estrela (Acesso local) com Malha Parcial (interconexão dos Cores).
* **Foco do Teste:** Demonstrar o equilíbrio entre custo de implementação e alta disponibilidade.

---

## 3. Matriz Comparativa de Engenharia de Redes

Durante os testes de Ping Contínuo (`ping -t`), os seguintes critérios serão avaliados e consolidados na matriz abaixo:

| Topologia | Redundância | Disponibilidade | Escalabilidade | Tolerância a Falhas | Complexidade de Custo |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Bus** | Nenhuma | Baixa | Difícil | Baixíssima | Muito Baixo |
| **Ring** | Mínima (1 link) | Média | Média | Média (depende do STP) | Baixo |
| **Star** | Nenhuma (no Core) | Alta (p/ nós isolados) | Excelente | Baixa (SPOF no centro) | Moderado |
| **Mesh** | Máxima | Altíssima | Complexa | Altíssima | Muito Alto |
| **Tree** | Alta (se duplicada) | Alta | Excelente | Média (falha na raiz afeta ramos) | Alto |
| **Hybrid** | Customizável | Alta | Excelente | Alta | Alto |

---

## 4. Como Executar os Testes

1. Baixar os arquivos `.pkt` da pasta `/cenarios`.
2. Abrir o cenário desejado no **Cisco Packet Tracer**.
3. Abrir o terminal do `PC-A` e inicie um ping contínuo para o `PC-B`: `ping [IP_ALVO] -t`.
4. Utilizar a ferramenta de **Exclusão de Link (Delete / Red Cross)** do Packet Tracer para cortar um cabo de comunicação estratégico.
5. Monitorar no terminal o tempo de queda e recuperação (mensagens de *Request timed out* vs. *Reply from...*).
6. Alternar para o modo **Simulation** para inspecionar o comportamento dos pacotes STP e tabelas MAC dos switches.
