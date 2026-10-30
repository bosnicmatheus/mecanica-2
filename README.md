# mecanica-2
# Entrega 2 — Modelo Lógico, Implementação SQL e Apresentação Final

### Modelagem de um sistema de gestão de informações para uma oficina mecânica (Scopino Auto Club)

---

## 1. Relatório Técnico Final

### Integrantes

| Nome completo do aluno | RGM | Usuário do GitHub |
| :---- | :---- | :---- |
| Ryan Nunes | [47391171] | [usuario_ryan] |
| Matheus Bosnic | [47646454] | [usuario_matheus] |

&nbsp;

### 1.1 Revisão do Modelo Conceitual

#### Caracterização da Organização

- **Nome e natureza da organização:** Scopino Auto Club, empresa do setor automotivo (CNPJ/CNAE 45.20-0-01) voltada à manutenção preventiva e corretiva de veículos automotores.
- **Contexto e porte:** Empresa de pequeno a médio porte, com atendimento médio de cerca de **35 a 50 veículos por semana** (aproximadamente 150 a 200 ordens de serviço por mês). Conta com uma equipe fixa de **6 mecânicos/atendentes**, além do proprietário/gestor. O catálogo possui mais de **800 itens e peças em estoque** e um histórico cadastral com mais de **1.200 clientes**.
- **Problemas e necessidades identificados:**
  - **P1 — Ruptura e falta de controle de estoque de peças:** Peças essenciais faltam durante a execução dos serviços por falta de controle centralizado entre a Ordem de Serviço (OS) e a baixa em estoque.
  - **P2 — Histórico descentralizado e perda de garantia/rastreabilidade:** Dificuldade em consultar rapidamente o histórico de manutenções de um veículo específico e peças trocadas anteriormente.
  - **P3 — Falta de visibilidade do faturamento real por serviço/mecânico:** Inexistência de relatórios detalhados que mostrem quais serviços geram mais margem e o desempenho individual da equipe técnica.
- **Escopo do sistema:** O sistema abrange todo o ciclo operacional e financeiro da oficina: cadastro de clientes, veículos, equipe técnica, catálogo de serviços, controle de peças/estoque, emissão e status de Ordens de Serviço (OS), itens executados, formas de pagamento e acompanhamento de faturamento.
- **Justificativa da escolha:** A organização possui processos operacionais bem definidos e dinâmicos, que exigem integração forte entre atendimento, execução técnica e estoque, sendo um excelente cenário para modelagem relacional completa.
- **Evidências da organização:** 
  - **Endereço:** R Herwis, 160 / R Santa Eudóxia, 508 - Vila Ema / Casa Verde - São Paulo/SP.
  - **Google Business Profile:** [Scopino Auto Club no Google Maps](https://maps.google.com)
  - **Fotos do local/visita:** Disponíveis na pasta `docs/imagens/` (`foto_oficina1.jpg`, `foto_oficina2.jpg`, `foto_entrevista.jpg`).
- **Responsável pela organização:** Alberto Scopino (Proprietário/Gestor) — Telefone: (11) 99999-9999.

---

#### Relato da Entrevista

- **Quem:** Alberto Scopino (Proprietário/Gestor). Entrevistadores: Ryan Nunes e Matheus Bosnic.
- **Quando e onde:** Realizada presencialmente no ambiente da oficina (Rua Herwis, 160).
- **Observação presencial:** Durante a visita, observou-se que o agendamento e o diagnóstico inicial ainda utilizavam anotações físicas em blocos de papel. As peças eram retiradas do almoxarifado sem registro imediato na OS, gerando divergências no inventário ao fim do mês.
- **Comprovação:** Foto da entrevista salva em `docs/imagens/foto_entrevista.jpg`.
- **O que foi levantado:** O relatório detalhado da entrevista e mapeamento de campo está disponível em [`levantamento/levantamento.md`](levantamento/levantamento.md).

---

#### Processos de Negócio

- **Processo 1 — Cadastro de Cliente e Veículo:** Registro dos dados do proprietário e vinculação de um ou mais veículos à sua conta.
- **Processo 2 — Abertura de Ordem de Serviço e Diagnóstico:** Entrada do veículo, registro das queixas e elaboração do diagnóstico pelo mecânico responsável.
- **Processo 3 — Orçamento e Aprovação:** Definição de peças necessárias e serviços; envio do valor total para aprovação do cliente.
- **Processo 4 — Execução e Baixa de Estoque:** Realização dos serviços pelo mecânico associado e dedução automática das peças do estoque.
- **Processo 5 — Encerramento e Pagamento:** Cálculo do valor total, registro da forma de pagamento (Pix, Cartão, Dinheiro) e finalização da OS.

---

#### Requisitos do Sistema

##### Requisitos Funcionais
- **RF01:** Registrar cliente com nome completo, CPF/CNPJ, telefone, e-mail e endereço.
- **RF02:** Cadastrar veículo vinculado a um cliente com placa, marca, modelo, ano e quilometragem.
- **RF03:** Abrir Ordem de Serviço (OS) vinculada a um veículo, mecânico e data de abertura.
- **RF04:** Associar serviços e peças executadas a uma OS com quantidade e preço praticado.
- **RF05:** Atualizar e controlar o saldo de estoque de peças após a inclusão na OS.
- **RF06:** Registrar pagamento de OS indicando valor, data e forma de pagamento.
- **RF07:** Consultar o histórico completo de manutenções de um veículo por placa.

##### Requisitos Não Funcionais
- **RNF01 (Integridade):** Não permitir a criação de uma OS sem veículo e cliente válidos.
- **RNF02 (Desempenho):** Consultas ao histórico do veículo devem responder em menos de 2 segundos.
- **RNF03 (Segurança):** O acesso aos dados e alterações de estoque deve exigir autenticação de usuário.

---

#### Regras de Negócio

- **RN01:** Um cliente pode possuir um ou mais veículos cadastrados.
- **RN02:** Cada veículo pertence a exatamente um cliente no cadastro.
- **RN03:** Uma OS deve obrigatoriamente estar vinculada a um único veículo.
- **RN04:** Uma OS só pode ser encerrada se houver pelo menos um serviço ou peça associada.
- **RN05:** A baixa de estoque de uma peça ocorre apenas após a aprovação/inclusão na OS.
- **RN06:** Uma peça não pode ser adicionada a uma OS se a quantidade requisitada for maior que o saldo em estoque.
- **RN07:** O valor total da OS é o somatório do (preço x quantidade) de todos os serviços e peças vinculados.

---

#### Revisão do DER Conceitual (Caso A)

software utilizado: **brModelo**  
Arquivo de imagem do DER: [`docs/diagramas/DER_conceitual_revisado.png`](docs/diagramas/DER_conceitual_revisado.png)

| Apontamento da correção da E1 | O que mudou no trabalho | Por quê |
| :---- | :---- | :---- |
| Relacionamento N:N entre OS e PEÇA sem quantidade | Criada entidade associativa `OS_PECA` com atributos `quantidade` e `preco_praticado`. | Permitir saber a quantidade exata de peças utilizadas em cada OS e registrar o preço da peça no momento da execução. |
| Atributos de FK desenhados no DER | Removidas todas as FKs explícitas do modelo conceitual. | Chaves estrangeiras são detalhe de implementação do Modelo Lógico. |

---

### 1.2 Conversão do Modelo Conceitual para o Modelo Lógico

1. **Entidades para Tabelas:**
   - `CLIENTE` $\rightarrow$ Tabela `CLIENTE`
   - `VEICULO` $\rightarrow$ Tabela `VEICULO`
   - `MECANICO` $\rightarrow$ Tabela `MECANICO`
   - `ORDEM_SERVICO` $\rightarrow$ Tabela `ORDEM_SERVICO`
   - `SERVICO` $\rightarrow$ Tabela `SERVICO`
   - `PECA` $\rightarrow$ Tabela `PECA`
2. **Relacionamentos e Chaves:**
   - **CLIENTE - VEICULO (1:N):** Adicionada `id_cliente` em `VEICULO` como FK.
   - **VEICULO - ORDEM_SERVICO (1:N):** Adicionada `id_veiculo` em `ORDEM_SERVICO` como FK.
   - **MECANICO - ORDEM_SERVICO (1:N):** Adicionada `id_mecanico` em `ORDEM_SERVICO` como FK.
   - **ORDEM_SERVICO - SERVICO (N:N):** Mapeado na tabela associativa `OS_SERVICO` (`id_os`, `id_servico`).
   - **ORDEM_SERVICO - PECA (N:N):** Mapeado na tabela associativa `OS_PECA` (`id_os`, `id_peca`).

---

### 1.3 Definição de Chaves Primárias e Estrangeiras

- `CLIENTE`: PK (`id_cliente`)
- `VEICULO`: PK (`id_veiculo`), FK (`id_cliente` $\rightarrow$ `CLIENTE.id_cliente`)
- `MECANICO`: PK (`id_mecanico`)
- `ORDEM_SERVICO`: PK (`id_os`), FK (`id_veiculo` $\rightarrow$ `VEICULO.id_veiculo`), FK (`id_mecanico` $\rightarrow$ `MECANICO.id_mecanico`)
- `SERVICO`: PK (`id_servico`)
- `PECA`: PK (`id_peca`)
- `OS_SERVICO`: PK composta (`id_os`, `id_servico`), FK (`id_os`), FK (`id_servico`)
- `OS_PECA`: PK composta (`id_os`, `id_peca`), FK (`id_os`), FK (`id_peca`)
- `PAGAMENTO`: PK (`id_pagamento`), FK (`id_os` $\rightarrow$ `ORDEM_SERVICO.id_os`)

---

### 1.4 Processo de Normalização (1FN, 2FN e 3FN)

- **1FN (Primeira Forma Normal):** Garantido que todos os atributos são atômicos. Telefones múltiplos ou multivalorados foram tratados ou estruturados. Atributos compostos (como endereço) foram decompostos em `logradouro`, `numero`, `bairro`, `cep`, `cidade`.
- **2FN (Segunda Forma Normal):** Eliminadas dependências parciais. Em tabelas de ligação (`OS_PECA`), atributos como `quantidade` e `preco_praticado` dependem inteiramente da chave primária composta (`id_os` + `id_peca`).
- **3FN (Terceira Forma Normal):** Eliminadas dependências transitivas. O preço atual da peça fica cadastrado em `PECA`, mas o valor cobrado na OS é mantido em `OS_PECA` para garantir imutabilidade do histórico financeiro.

---

### 1.5 Justificativas Técnicas das Decisões

- **Preço Praticado em OS_PECA e OS_SERVICO:** Garante estabilidade histórica. Se o valor do serviço ou da peça mudar no catálogo geral no futuro, o histórico das Ordens de Serviço antigas permanece intacto.
- **Uso de Chaves Primárias Auto-Incrementais (IDs):** Facilita a manutenção, garante melhor desempenho em JOINs e previne gargalos ao alterar dados cadastrais (ex.: alteração de CPF ou placa).
- **Restrições UNIQUE em Placa e CPF:** Atende às regras **RN02** e de validação legal para evitar duplicidade de cadastros.

---

### 1.6 Proposta de Arquitetura Analítica para BI e IA

- **Informações Estratégicas:** Identificação dos serviços mais lucrativos, tempo médio de permanência dos veículos na oficina, previsão de demanda de estoque de peças sazonais.
- **KPIs Propostos:**
  - **KPI 01 — Ticket Médio por Ordem de Serviço:** Valor médio gasto pelos clientes por visita.
  - **KPI 02 — Taxa de Peças com Estoque Crítico:** Percentual de peças do catálogo com quantidade atual abaixo do estoque mínimo.
  - **KPI 03 — Faturamento por Mecânico:** Produtividade e receita gerada por cada profissional técnico.

#### Tabela de Rastreabilidade

| Problema ou KPI | Tabelas envolvidas | Consulta no script SQL |
| :---- | :---- | :---- |
| **P1 — Ruptura de estoque de peças** | `PECA` | `G01` |
| **P2 — Histórico descentralizado do veículo** | `VEICULO`, `ORDEM_SERVICO`, `OS_SERVICO`, `OS_PECA` | `G02` |
| **P3 — Faturamento por mecânico** | `MECANICO`, `ORDEM_SERVICO` | `G03` |
| **KPI 01 — Ticket médio das OS** | `ORDEM_SERVICO` | `G04` |
| **KPI 02 — Peças em estoque crítico** | `PECA` | `G01` |

---

## 2. Modelo Lógico Relacional

O Diagrama do Modelo Lógico Relacional foi desenvolvido no software **brModelo** e está disponível no repositório no caminho:
[`docs/diagramas/modelo_logico.png`](docs/diagramas/modelo_logico.png)

---

## 3. Script SQL Completo

O script completo de criação das tabelas, inserção da massa de dados, constraints e consultas está localizado em:
[`docs/script.sql`](docs/script.sql)

---

## 4. Massa de Dados

A massa de dados foi construída mantendo **absolutamente a privacidade** de pessoas reais, utilizando dados fictícios totalmente coerentes com o volume semanal de uma oficina de médio porte.

- **Arquivo:** [`docs/massa_de_dados.sql`](docs/massa_de_dados.sql) (também inclusa no script principal).
- **Volume:** Contém 20 clientes, 25 veículos, 5 mecânicos, 30 peças, 15 serviços cadastrados, 40 ordens de serviço e seus respectivos itens e pagamentos.

---

## 5. Repositório GitHub

Estrutura de pastas do repositório:

```text
├── README.md
├── docs/
│   ├── script.sql
│   ├── massa_de_dados.sql
│   ├── dicionario_de_dados.html
│   ├── diagramas/
│   │   ├── DER_conceitual_revisado.png
│   │   └── modelo_logico.png
│   └── imagens/
│       ├── foto_oficina1.jpg
│       ├── foto_oficina2.jpg
│       └── foto_entrevista.jpg
└── levantamento/
    └── levantamento.md
```

---

## 6. Uso de Inteligência Artificial

| Item | O que registrar |
| :---- | :---- |
| **Ferramenta e etapa** | ChatGPT / Gemini na geração da massa de dados fictícia e apoio no modelo de script de consultas SQL. |
| **Motivação** | Agilizar o preenchimento de nomes fictícios, placas válidas e cálculo de agregações complexas em SQL. |
| **Prompt(s) utilizados** | *"Gere 30 comandos INSERT em SQL para a tabela PECA com nomes reais de autopeças, preços coerentes entre R$ 20 e R$ 800 e quantidades em estoque."* |
| **Resposta recebida** | Script SQL populado com registros plausíveis. |
| **Fontes consultadas** | Documentação Oficial do MySQL 8.0. |
| **Trechos corrigidos** | Ajustados os nomes de colunas e FKs que a IA havia sugerido fora do padrão definido no Modelo Lógico. |
| **Reflexão crítica** | A IA acelera tarefas repetitivas, mas exige revisão rigorosa de sintaxe e relacionamentos de chave estrangeira. |

---

## 7. Apresentação Corporativa Final

Os slides da apresentação do Pitch Corporativo (8 minutos) estão disponíveis na pasta de documentação do grupo:
[`docs/apresentacao_oficina_scopino.pdf`](docs/apresentacao_oficina_scopino.pdf)
