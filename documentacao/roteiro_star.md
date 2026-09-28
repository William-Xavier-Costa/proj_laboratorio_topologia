# Roteiro de Testes: Topologia em Estrela (Star) e a Análise do SPOF (Single Point of Failure / Ponto Único de Falha)

## 1. Visão Geral do Cenário

A topologia em estrela conecta todos os dispositivos finais (PCs, Laptops, Impressoras) a um único dispositivo central, geralmente um Switch ou Hub. Embora ofereça excelente **escalabilidade** (adicionar um novo computador é muito simples e não afeta os outros), ela apresenta uma vulnerabilidade crítica: o **Ponto Único de Falha (SPOF)**. Se o nó central falhar, toda a rede colapsa instantaneamente.

## 2. Configuração Física no Packet Tracer

* **Ativo Central (Core):** 1 Switch (ex: Cisco Catalyst 2960).
* **Hosts (Dispositivos Finais):** 4 Computadores (PC-A, PC-B, PC-C e PC-D).
* **Cabeamento:** Cabos diretos (Copper Straight-Through) conectando a placa de rede de cada PC a uma porta FastEthernet do Switch Central.

## 3. Estado Inicial da Rede (Análise Lógica)

1. Abra o arquivo `cenarios/03-star.pkt`.
2. Garanta que todos os cabos exibam leds **verdes** em ambas as pontas.
3. Certifique-se de que os IPs dos computadores estejam configurados na mesma sub-rede (ex: `192.168.1.0/24`).
4. **Análise de Isolamento:** Observe que, se o PC-A quebrar ou o cabo dele for desconectado, os computadores PC-B, PC-C e PC-D continuam se comunicando normalmente. *Esta é a grande vantagem da Estrela sobre o Barramento (Bus).*

## 4. Passo a Passo do Teste de Estresse (O Colapso do Core)

### Passo 01: Estabelecer Fluxos Simultâneos de Comunicação

Para provar o impacto global da falha, vamos abrir duas frentes de teste:
1.No **PC-A**, abra o *Command Prompt* e inicie um ping contínuo para o **PC-B**:

```shell
      PC-A> ping [IP_DO_PC-B] -t
```

2.No **PC-C**, abra o *Command Prompt* e inicie um ping contínuo para o **PC-D**:

```shell
   PC-C> ping [IP_DO_PC-D] -t
```

*Confirme se ambos os pings estão respondendo perfeitamente de forma simultânea.*

### Passo 02: Simulação do Ponto Único de Falha (SPOF)

Existem duas formas de simular isso no Packet Tracer. Escolha a que achar mais visual para o seu GitHub:

* **Método A (Falha de Energia/Desligamento):**
  1. Clique sobre o **Switch Central**.
  2. Vá até a aba **Physical**.
  3. Localize o botão de energia (Power Switch) e clique nele para **desligar** o equipamento (ou remova o módulo de energia se for um switch modular).
* **Método B (Destruição do Hardware):**
  1. Selecione a ferramenta **Delete (tecla Del ou 'X' vermelho)** no menu do simulador.
  2. Clique diretamente no **Switch Central** para excluí-lo da topologia.

### Passo 03: Monitoramento do Impacto na Disponibilidade

1. Volte imediatamente para as telas de terminal do **PC-A** e do **PC-C**.
2. **Resultado Esperado:** Ambos os pings irão falhar no mesmo segundo, exibindo `Request timed out` ou `Destination host unreachable`.
3. Note que nenhuma máquina da rede consegue conversar com mais ninguém, provando o colapso total do sistema.

## 5. Análise dos Resultados para o Relatório do GitHub

Para enriquecer a documentação do seu projeto, responda a estas questões no fechamento do roteiro:
**Isolamento de Falhas:** O que acontece se o cabo apenas do PC-A for cortado? O resto da rede para?
**Análise de Risco (SPOF):** Explique com suas palavras por que centralizar toda a inteligência da rede em um único switch sem redundância é um risco crítico de segurança e disponibilidade para uma empresa.
**Mitigação de Engenharia:** Como um Engenheiro de Redes resolve o problema do SPOF na topologia em estrela? *(Dica: Introduzindo um segundo switch redundante e utilizando empilhamento/VSS ou protocolos de Alta Disponibilidade como o HSRP/VRRP no gateway).*
