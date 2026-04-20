# Projeto TAI - Sistema de Supervisão e Controle de Célula FMS

Este repositório contém o desenvolvimento da camada de supervisão e lógica de controle baseada em **Node-RED** para uma célula de manufatura flexível (FMS).

**Desenvolvido por:** Luiz Henrique Monteiro

---

## 📌 Visão Geral do Projeto

O objetivo deste trabalho foi estabelecer a comunicação e o processamento de dados entre o nível de campo (sensores/atuadores, simulados ou reais) e uma interface de supervisão (Dashboard). Embora o sistema de controle de campo tenha sido desenvolvido no ambiente **Elipse E3** (não incluído neste repositório), toda a inteligência de processamento de mensagens, decodificação de dados e interface homem-máquina (HMI) foi implementada utilizando o **Node-RED**.

A comunicação entre o Elipse e o Node-RED é realizada através do protocolo **MQTT**, utilizando um *Broker* central como intermediário.

## 🛠️ Detalhes Técnicos (Implementação no Node-RED)

Esta seção descreve a lógica implementada nos fluxos do Node-RED para gerenciar os dados da célula.

### 1. Estrutura de Comunicação MQTT (Tópicos)

Para otimizar a recepção de dados e organizar a hierarquia de variáveis, utilizou-se um **Namespace (Prefixo de Tópico)** unificado: `sorting_fms`.

A subscrição (assinatura) no nó de entrada MQTT (`mqtt in`) foi configurada utilizando o *wildcard* multi-nível (`#`), garantindo a recepção dinâmica de todas as subvariáveis do processo sem a necessidade de configurar nós individuais para cada sensor.

* **Tópico Assinado:** `sorting_fms/#`

### 2. Roteamento e Separação de Dados

A primeira etapa do processamento é realizada por um nó **Function (Topic Separator)**. Este nó analisa o sufixo do tópico da mensagem recebida (`msg.topic`) e utiliza desvios condicionais (`if/includes`) para direcionar a mensagem para a saída correta, correspondente à variável específica (ex: memórias gerais, contadores de peças, tempos de ciclo).

Esta abordagem centralizada reduz a carga de processamento ao segmentar o fluxo de dados apenas para os nós interessados.

### 3. Decodificação Bit-a-Bit (Parsing)

Para variáveis que transportam múltiplos estados (como alarmes ou status de sensores/atuadores compactados em um único Byte), foram implementados nós **Function de Decodificação Bit-a-Bit**.

Esta lógica utiliza **máscaras binárias** e operadores de deslocamento (`&`, `|`) para extrair o estado booleano (verdadeiro/falso) de cada bit individual dentro do byte recebido do campo (via Elipse).

**Exemplo de Mapeamento de Bits (Ilustrativo):**
* `Bit 2`: Comando de RESET (Ativo)
* `Bit 4`: Status da Linha Ocupada
* `Bit 5`: Detecção de Peça Metálica
* `Bit 6`: Detecção de Peça Vermelha
* `Bit 7`: Detecção de Peça Preta

## 📂 Estrutura do Repositório

* `/02_NodeRED_Flows`: Contém o arquivo JSON completo com todos os fluxos, nós e configurações do projeto Node-RED.
* `README.md`: Este arquivo com a documentação do projeto.

## 🚀 Como Utilizar

Para reproduzir este ambiente:

1.  Certifique-se de ter o Node-RED instalado e rodando.
2.  Instale as paletas necessárias (ex: `node-red-dashboard`, `node-red-contrib-mqtt-broker` se estiver usando um broker local).
3.  No menu do Node-RED (canto superior direito), vá em **Import -> Clipboard**.
4.  Selecione o arquivo `.json` dentro da pasta `02_NodeRED_Flows`.
5.  Clique em **Import** e, em seguida, em **Deploy**.

---
*Este trabalho foi desenvolvido como parte dos requisitos da disciplina de (TAI).*