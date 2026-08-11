# 📦 Escopo Fechado do MVP — HEMOVIA

> **Nome do projeto:** HEMOVIA
> **Produto:** Plataforma web para gestão inteligente da distribuição de hemocomponentes
> **Versão:** MVP — 2026.2
> **Público:** Hemocentros e unidades hospitalares, em cenário simulado
> **Dados:** Exclusivamente sintéticos

---

## 1. 🎯 Objetivo do MVP

O MVP do **HEMOVIA** tem como objetivo simular e gerenciar o processo de atendimento de uma requisição hospitalar de hemocomponentes, desde a solicitação até a definição da distribuição.

O sistema deverá garantir, dentro do cenário didático do projeto:

**Componente correto → Compatibilidade correta → Validade adequada → Rota adequada → Monitoramento do transporte**

A plataforma centralizará informações de estoque, requisições, distribuição, rotas, telemetria e indicadores em uma única aplicação web.

---

## 2. 👥 Usuários do MVP

O MVP contará com dois perfis funcionais.

### 🏥 Operador do Hemocentro

Responsável por:

* Visualizar o estoque;
* Cadastrar bolsas;
* Acompanhar requisições;
* Processar solicitações;
* Acompanhar distribuições;
* Visualizar rotas;
* Monitorar temperatura;
* Consultar indicadores.

### 🏨 Unidade Hospitalar

Responsável por:

* Criar requisições;
* Consultar suas solicitações;
* Acompanhar o status das requisições;
* Visualizar previsão de entrega.

> **Nota:** Não haverá autenticação real no MVP. Os perfis serão simulados para fins acadêmicos.

---

# 3. 🩸 Funcionalidades Obrigatórias

## F01 — Dashboard

A página inicial apresentará uma visão geral da operação.

### Indicadores

* Total de bolsas em estoque;
* Bolsas por tipo sanguíneo;
* Bolsas próximas do vencimento;
* Requisições pendentes;
* Requisições atendidas;
* Requisições em transporte;
* Taxa de descarte;
* Indicador de desabastecimento.

---

## F02 — Gestão de Estoque

O sistema permitirá gerenciar as bolsas disponíveis.

### Cadastro de bolsa

Cada bolsa deverá possuir:

* ID da bolsa;
* Tipo sanguíneo;
* Fator Rh;
* Componente;
* Data de coleta;
* Data de validade;
* Status.

### Consulta de estoque

Será possível filtrar por:

* Tipo sanguíneo;
* Componente;
* Validade;
* Status.

### Status possíveis

```text
DISPONÍVEL
RESERVADA
EM_TRANSPORTE
ENTREGUE
VENCIDA
DESCARTADA
```

---

## F03 — Gestão de Hospitais

Cada hospital possuirá:

* ID;
* Nome;
* Cidade;
* Latitude;
* Longitude;
* Prioridade.

O MVP utilizará uma quantidade limitada de hospitais fictícios para composição do grafo de rotas.

### Exemplos

```text
Hospital Central
Hospital Santa Maria
Hospital São Lucas
Hospital da Esperança
```

> Todos os dados utilizados serão sintéticos.

---

## F04 — Requisições Hospitalares

O hospital poderá criar uma requisição contendo:

* Hospital;
* Componente;
* Tipo sanguíneo;
* Fator Rh;
* Quantidade;
* Prioridade;
* Prazo de entrega.

### Status da requisição

```text
PENDENTE
EM_ANALISE
ALOCADA
EM_TRANSPORTE
ENTREGUE
CANCELADA
```

---

# 4. 🧬 Compatibilidade ABO/Rh

Ao receber uma requisição, o sistema deverá verificar quais bolsas disponíveis são compatíveis com a solicitação.

### Exemplo

```text
REQUISIÇÃO

Tipo sanguíneo: O+
Componente: Concentrado de Hemácias
Quantidade: 3
```

O sistema deverá consultar as bolsas disponíveis e identificar aquelas compatíveis.

### Fluxo

```text
Requisição
     ↓
Consultar estoque
     ↓
Verificar ABO/Rh
     ↓
Filtrar bolsas compatíveis
     ↓
Enviar para FEFO
```

> **Importante:** A compatibilidade ABO/Rh será utilizada exclusivamente para fins didáticos e não deverá ser interpretada como protocolo clínico real.

---

# 5. ⏳ FEFO — First Expire, First Out

Após identificar as bolsas compatíveis, o sistema deverá priorizar aquelas com menor prazo de validade.

### Exemplo

| Bolsa | Validade |
| ----- | -------- |
| H001  | 12/08    |
| H005  | 14/08    |
| H003  | 19/08    |
| H009  | 25/08    |

Para uma solicitação de duas bolsas:

```text
1º → H001
2º → H005
```

A implementação deverá utilizar uma **fila de prioridade**, conforme a proposta da disciplina de Algoritmos e Estruturas de Dados.

---

# 6. 🗺️ Roteirização

Após a seleção das bolsas, o sistema deverá calcular a rota de distribuição.

O algoritmo utilizado será o **Dijkstra**, aplicado sobre um grafo limitado da rede de transporte.

### Exemplo

```text
Hemocentro
     ↓
Centro Norte
     ↓
Hospital Central
```

### Resultado

```text
Origem:
Hemocentro Recife

Destino:
Hospital Central

Distância:
8,4 km

Tempo estimado:
22 minutos

Rota:
Hemocentro
   ↓
Centro Norte
   ↓
Hospital Central
```

> O grafo será propositalmente limitado para manter o escopo compatível com o semestre.

---

# 7. 🌡️ Monitoramento da Cadeia Fria

O sistema deverá simular a telemetria dos veículos responsáveis pelo transporte.

Cada registro poderá conter:

```json
{
  "veiculo": "V001",
  "temperatura": 4.3,
  "latitude": -8.0476,
  "longitude": -34.8770,
  "timestamp": "2026-08-10T21:00:00"
}
```

### Informações monitoradas

* Veículo;
* Temperatura;
* Latitude;
* Longitude;
* Data e hora.

### Exemplo de situação normal

```text
Veículo: V001

Temperatura: 4,3 °C

Status: NORMAL
```

### Exemplo de alerta

```text
⚠️ ALERTA DE TEMPERATURA

Veículo: V001
Temperatura: 9,2 °C
Status: FORA DA FAIXA
```

> A telemetria de temperatura e GPS será totalmente simulada.

---

# 8. 🚚 Central de Distribuição

A **Central de Distribuição** será a principal funcionalidade do MVP.

Ela deverá integrar todo o fluxo operacional:

```text
REQUISIÇÃO
     ↓
COMPATIBILIDADE ABO/Rh
     ↓
FEFO
     ↓
ROTEIRIZAÇÃO
     ↓
TRANSPORTE
     ↓
MONITORAMENTO
     ↓
ENTREGA
```

### Exemplo de fluxo

```text
Requisição #REQ-001

Hospital:
Hospital Central

Componente:
Concentrado de Hemácias

Tipo:
O+

Quantidade:
3
```

Após o processamento:

```text
✓ Requisição recebida
✓ Compatibilidade verificada
✓ Bolsas selecionadas por FEFO
✓ Bolsas reservadas
✓ Rota calculada
✓ Transporte iniciado
✓ Telemetria ativa
✓ Cadeia fria monitorada
```

---

# 9. 📊 Estatística e Indicadores

O MVP deverá utilizar os dados gerados pelo próprio sistema para produzir indicadores relacionados ao domínio.

## Estoque

* Quantidade total de bolsas;
* Estoque por tipo sanguíneo;
* Estoque por componente.

## Demanda

* Requisições por hospital;
* Requisições por componente;
* Requisições por tipo sanguíneo.

## Operação

* Tempo médio de atendimento;
* Tempo mínimo;
* Tempo máximo;
* Medidas de dispersão.

## Perdas

* Quantidade de bolsas vencidas;
* Taxa de descarte por vencimento.

## Risco

* Probabilidade de desabastecimento.

---

# 10. ⚙️ Processamento Concorrente

O MVP contará com pelo menos um processo real utilizando concorrência.

### Caso definido

**Ingestão de telemetria.**

Enquanto o sistema processa requisições, poderá receber simultaneamente dados de temperatura e localização.

```text
                 BACKEND
                    │
          ┌─────────┴─────────┐
          │                   │
      Thread 1            Thread 2
          │                   │
    Requisições          Telemetria
          │                   │
          └─────────┬─────────┘
                    ↓
                 Sistema
```

A implementação deverá demonstrar ganho ou necessidade de processamento concorrente e evitar condições de corrida.

---

# 11. 🌐 Comunicação

A arquitetura de comunicação do MVP será baseada em:

```text
Frontend
    ↓
HTTPS
    ↓
REST API
    ↓
Spring Boot
    ↓
Banco de Dados
```

### Exemplo de endpoint de telemetria

```http
POST /api/telemetria
```

### Exemplo de payload

```json
{
  "veiculo": "V001",
  "temperatura": 4.3,
  "latitude": -8.0476,
  "longitude": -34.8770
}
```

O projeto deverá documentar os protocolos, APIs e fluxo de comunicação utilizados.

---

# 12. 🚀 CI/CD e Deploy

O MVP deverá possuir um pipeline de integração e entrega contínuas.

### Fluxo

```text
Git Push
   ↓
GitHub Actions
   ↓
Build
   ↓
Testes
   ↓
Deploy
   ↓
Aplicação disponível
```

O deploy deverá ser reproduzível e demonstrável.

---

# 13. 🧪 O que NÃO estará no MVP

Para manter o escopo fechado, as seguintes funcionalidades estão explicitamente fora do MVP:

* ❌ Dados reais de pacientes;
* ❌ Dados reais de doadores;
* ❌ Integração com hospitais reais;
* ❌ Integração com sistemas do SUS;
* ❌ Integração com a Hemorrede real;
* ❌ Aplicativo mobile;
* ❌ Autenticação real;
* ❌ Pagamentos;
* ❌ Inteligência Artificial;
* ❌ Machine Learning;
* ❌ Previsão avançada de demanda;
* ❌ GPS real;
* ❌ Sensores físicos reais;
* ❌ IoT real;
* ❌ Roteirização em larga escala;
* ❌ Integração com Google Maps;
* ❌ Protocolos clínicos reais;
* ❌ Prontuário médico;
* ❌ Cadastro de pacientes;
* ❌ Cadastro de doadores;
* ❌ Integrações externas não previstas no projeto.

---

# 14. 🎬 Cenário Principal de Demonstração

O MVP deverá conseguir executar o seguinte cenário de ponta a ponta:

### Cenário

Um hospital solicita:

> **4 bolsas de Concentrado de Hemácias O+**

### Fluxo

```text
1. Hospital cria requisição
              ↓
2. Sistema consulta estoque
              ↓
3. Sistema verifica compatibilidade ABO/Rh
              ↓
4. Sistema encontra bolsas compatíveis
              ↓
5. Sistema aplica FEFO
              ↓
6. Sistema reserva as quatro bolsas
              ↓
7. Sistema calcula rota com Dijkstra
              ↓
8. Transporte é iniciado
              ↓
9. Telemetria começa a ser recebida
              ↓
10. Temperatura é monitorada
              ↓
11. Indicadores são atualizados
              ↓
12. Requisição é finalizada como entregue
```

### Resultado esperado

Ao final do fluxo, o sistema deverá conseguir demonstrar:

```text
✓ Componente correto
✓ Compatibilidade verificada
✓ Bolsas selecionadas por validade
✓ Rota calculada
✓ Transporte registrado
✓ Temperatura monitorada
✓ Indicadores atualizados
```

---

# 15. 📋 Matriz de Funcionalidades do MVP

| Módulo          | Funcionalidade                    | MVP |
| --------------- | --------------------------------- | :-: |
| Dashboard       | Indicadores gerais                |  []  |
| Hospitais       | Cadastro e consulta               |  []  |
| Bolsas          | Cadastro e consulta               |  []  |
| Estoque         | Controle de estoque               |  []  |
| Requisições     | Criar e acompanhar                |  []  |
| Compatibilidade | ABO/Rh                            |  []  |
| Algoritmos      | FEFO                              |  []  |
| Algoritmos      | Hash de estoque                   |  []  |
| Algoritmos      | Dijkstra                          |  []  |
| Rotas           | Visualização                      |  []  |
| Distribuição    | Central de distribuição           |  []  |
| Telemetria      | Temperatura simulada              |  []  |
| Telemetria      | GPS simulado                      |  []  |
| Estatística     | Indicadores                       |  []  |
| Estatística     | Probabilidade de desabastecimento |  []  |
| Estatística     | Taxa de descarte                  |  []  |
| SO              | Threads                           |  []  |
| Redes           | REST API                          |  []  |
| Redes           | Monitoramento                     |  []  |
| Infraestrutura  | CI/CD                             |  []  |
| Infraestrutura  | Deploy                            |  []  |
| Dados reais     | Pacientes/doadores                |  []  |
| IA              | Machine Learning                  |  []  |
| Mobile          | Aplicativo nativo                 |  []  |
| Integrações     | Sistemas externos                 |  []  |

---

# 16. 🎯 Definição do MVP

> **O HEMOVIA é uma plataforma web acadêmica para simulação da gestão e distribuição inteligente de hemocomponentes. O MVP integra estoque, requisições hospitalares, compatibilidade ABO/Rh, priorização FEFO, roteirização por Dijkstra, estatística, processamento concorrente, comunicação via API e monitoramento de telemetria, utilizando exclusivamente dados sintéticos.**

## Critério de sucesso

O MVP será considerado funcional quando for capaz de **receber uma requisição hospitalar, identificar bolsas compatíveis, priorizá-las por validade, calcular uma rota de distribuição, iniciar o transporte simulado, monitorar sua telemetria e atualizar os indicadores do sistema em um fluxo integrado.**
