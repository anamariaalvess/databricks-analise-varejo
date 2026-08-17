# Análise de Varejo com Databricks

Projeto completo de engenharia e análise de dados desenvolvido no **Databricks**, utilizando arquitetura Lakehouse, Apache Spark, PySpark, Delta Lake, Unity Catalog, Databricks SQL e AI/BI Dashboards.

O projeto simula o ambiente de dados de uma distribuidora fictícia e demonstra a construção de uma solução analítica de ponta a ponta, desde a geração e ingestão dos dados até a disponibilização de indicadores executivos e recomendações para tomada de decisão.

> Todos os dados utilizados neste projeto são fictícios e foram gerados exclusivamente para fins educacionais e de portfólio.

---

## Objetivo

O objetivo do projeto é construir uma plataforma analítica capaz de consolidar diferentes áreas de uma empresa de distribuição e responder perguntas relacionadas a:

- vendas;
- clientes;
- produtos;
- estoque;
- compras;
- fornecedores;
- contas a receber;
- contas a pagar;
- inadimplência;
- desempenho da equipe comercial.

Além de análises descritivas, o projeto inclui análises comportamentais e recomendações baseadas em regras de negócio.

---

## Cenário de negócio

A **Distribuidora Horizonte** é uma empresa fictícia que comercializa produtos para estabelecimentos como:

- supermercados;
- mercearias;
- padarias;
- restaurantes;
- farmácias;
- lojas de conveniência;
- escritórios.

O catálogo é dividido em categorias como:

- Alimentos;
- Bebidas;
- Higiene;
- Limpeza;
- Papelaria;
- Utilidades.

A empresa precisa acompanhar seu desempenho comercial, conhecer melhor os clientes, controlar o estoque, reduzir rupturas e excessos, planejar compras e monitorar sua situação financeira.

---

# Arquitetura

O projeto utiliza uma arquitetura Lakehouse baseada no padrão **Medallion Architecture**.

```text
                         DADOS FICTÍCIOS
                               │
                               ▼
                         ┌───────────┐
                         │  LANDING  │
                         │    CSV    │
                         └─────┬─────┘
                               │
                               ▼
                         ┌───────────┐
                         │  BRONZE   │
                         │ Dados     │
                         │ brutos    │
                         └─────┬─────┘
                               │
                               ▼
                         ┌───────────┐
                         │  SILVER   │
                         │ Dados     │
                         │ tratados  │
                         └─────┬─────┘
                               │
                      ┌────────┴────────┐
                      │                 │
                      ▼                 ▼
               QUALIDADE          QUARENTENA
                      │
                      ▼
                 ┌───────────┐
                 │   GOLD    │
                 │ Data Marts│
                 └─────┬─────┘
                       │
                       ▼
                DATABRICKS SQL
                       │
                       ▼
                AI/BI DASHBOARD
                       │
                       ▼
                     GENIE
```

A arquitetura separa responsabilidades entre ingestão, tratamento, validação e consumo analítico.

---

## Camadas

### Landing

Área utilizada para armazenar os arquivos fictícios antes da ingestão.

As fontes simuladas incluem:

- clientes;
- produtos;
- vendedores;
- fornecedores;
- vendas;
- compras;
- estoque;
- contas a receber;
- contas a pagar;
- metas comerciais.

---

### Bronze

Camada responsável por preservar os dados em formato próximo ao recebido da origem.

Nesta etapa são mantidos propositalmente:

- registros duplicados;
- valores nulos;
- inconsistências textuais;
- problemas de qualidade presentes na origem.

Também são adicionados metadados de ingestão e origem dos arquivos.

---

### Silver

Camada responsável pela preparação dos dados para consumo analítico.

São realizadas operações como:

- tipagem das colunas;
- padronização de textos;
- tratamento de valores nulos;
- remoção de duplicidades;
- aplicação de regras de negócio;
- validação de registros;
- organização em dimensões e fatos.

Os registros que não atendem regras essenciais são enviados para estruturas de **quarentena**, preservando a rastreabilidade.

---

### Gold

A camada Gold contém Data Marts preparados para consumo analítico e construção de dashboards.

Entre as estruturas desenvolvidas estão:

#### Vendas

- `vendas_diarias`
- `vendas_mensais`
- `vendas_categoria_mensal`
- `desempenho_vendedores_mensal`

#### Clientes

- `clientes_rfm`
- `resumo_segmentos_rfm`

#### Produtos

- `produtos_curva_abc`
- `resumo_curva_abc`

#### Estoque e Compras

- `analise_estoque_atual`
- `resumo_estoque`
- `sugestao_compras`
- `resumo_sugestao_compras`

#### Financeiro

- `resumo_financeiro_atual`
- `aging_contas_receber`
- `aging_contas_pagar`
- `inadimplencia_clientes`
- `fluxo_caixa_mensal`

---

# Tecnologias utilizadas

| Tecnologia | Utilização |
|---|---|
| Databricks | Plataforma principal do projeto |
| Apache Spark | Processamento distribuído |
| PySpark | Transformação e análise dos dados |
| Delta Lake | Persistência das tabelas Lakehouse |
| Unity Catalog | Organização e governança dos objetos |
| Databricks SQL | Consultas e datasets analíticos |
| AI/BI Dashboards | Construção dos dashboards |
| Genie | Exploração dos dados em linguagem natural |
| Git | Versionamento |
| GitHub | Disponibilização do projeto |

---

# Dados fictícios

Os dados são gerados programaticamente utilizando uma semente fixa:

```python
SEMENTE = 42
```

Isso torna o processo reprodutível.

O período simulado compreende:

```text
01/01/2024
até
31/07/2026
```

A base contém aproximadamente:

| Entidade | Volume |
|---|---:|
| Clientes | 2.000 |
| Produtos | 500 |
| Vendedores | 20 |
| Fornecedores | 80 |
| Pedidos de venda | ~40.000 |
| Itens de venda | ~150.000 |
| Pedidos de compra | ~8.000 |
| Registros diários de estoque | ~470.000 |

Os volumes podem apresentar pequenas variações conforme as regras de geração.

---

# Qualidade dos dados

Foi implementada uma etapa específica de qualidade antes da construção da camada Gold.

São verificadas regras relacionadas a:

- unicidade;
- completude;
- integridade referencial;
- domínio;
- validade;
- consistência temporal;
- consistência financeira.

Exemplo:

```text
fato_vendas.id_cliente
             │
             ▼
        dim_cliente
```

Uma venda não pode referenciar um cliente inexistente.

Os resultados das verificações são persistidos na tabela:

```text
resultado_qualidade
```

Cada regra recebe um status:

```text
APROVADO
ou
REPROVADO
```

Também foi implementado um **Quality Gate**, impedindo o processamento da Gold quando existem testes críticos reprovados.

---

# Quarentena de dados

Os dados inválidos não são simplesmente excluídos.

Registros rejeitados são mantidos em tabelas específicas, por exemplo:

```text
quarentena_vendas
quarentena_contas_receber
```

Isso permite:

- auditoria;
- investigação da origem do problema;
- rastreabilidade;
- correção futura.

Durante o desenvolvimento, as validações detectaram inclusive títulos financeiros cuja venda correspondente havia sido integralmente rejeitada na camada Silver.

O problema foi identificado pela validação de integridade referencial e tratado por meio da quarentena.

---

# Análise de vendas

A análise comercial calcula indicadores como:

- faturamento;
- lucro bruto;
- margem;
- quantidade vendida;
- quantidade de pedidos;
- clientes ativos;
- produtos vendidos;
- ticket médio;
- percentual de desconto;
- crescimento mensal.

Também são analisadas:

- sazonalidade;
- categorias de produtos;
- regiões comerciais;
- desempenho dos vendedores;
- atingimento de metas.

---

# Segmentação RFM

Foi implementada uma análise **RFM — Recência, Frequência e Valor Monetário**.

Cada cliente é avaliado de acordo com:

### Recência

Número de dias desde sua última compra.

### Frequência

Quantidade de pedidos realizados.

### Valor Monetário

Faturamento acumulado do cliente.

Os clientes recebem scores entre:

```text
1 e 5
```

para cada dimensão.

Exemplo:

```text
R = 5
F = 5
M = 5

RFM = 555
```

A partir desses indicadores, os clientes são classificados em grupos como:

- Campeões;
- Leais;
- Potenciais leais;
- Novos clientes;
- Grandes clientes em risco;
- Em risco;
- Hibernando;
- Regulares;
- Sem compra.

Importante: os perfis utilizados internamente durante a geração dos dados **não são utilizados na classificação RFM**.

A segmentação é derivada exclusivamente do comportamento observado nas transações.

---

# Curva ABC

Os produtos são classificados de acordo com sua participação no faturamento dos últimos 12 meses.

A classificação utilizada é:

```text
A → primeiros 80% do faturamento acumulado

B → entre 80% e 95%

C → acima de 95%
```

Produtos sem vendas recentes são classificados separadamente.

A análise permite identificar quais itens possuem maior impacto financeiro para a empresa.

---

# Análise de estoque

A Curva ABC é combinada com a posição atual do estoque.

São calculados indicadores como:

```text
dias de cobertura =
estoque atual
──────────────
venda média diária
```

Os produtos podem receber classificações como:

- Ruptura;
- Estoque crítico;
- Estoque baixo;
- Estoque adequado;
- Excesso de estoque;
- Sem demanda recente.

A combinação da classificação comercial e operacional permite definir prioridades.

Exemplo:

```text
Classe A
+
Ruptura
=
Prioridade crítica
```

Outro exemplo:

```text
Classe C
+
Excesso de estoque
=
Revisar compras
```

---

# Sugestão de compras

O projeto também possui uma camada prescritiva baseada em regras transparentes de reposição.

A recomendação utiliza:

- venda média diária;
- classificação ABC;
- estoque atual;
- estoque mínimo e máximo;
- estoque de segurança;
- prazo médio do fornecedor;
- ponto de reposição;
- compras ainda não recebidas;
- cobertura-alvo.

O ponto de reposição é calculado conceitualmente por:

```text
Ponto de reposição
=
Demanda durante o prazo de entrega
+
Estoque de segurança
```

A quantidade sugerida considera também mercadorias já em trânsito.

O resultado disponibiliza:

- necessidade de reposição;
- quantidade recomendada;
- fornecedor;
- investimento estimado;
- prioridade da compra.

A recomendação não representa uma ordem automática de compra.

---

# Análise financeira

A análise financeira consolida informações de contas a receber e contas a pagar.

São calculados indicadores como:

- saldo a receber;
- saldo a pagar;
- valor vencido;
- taxa de inadimplência;
- prazo médio de recebimento;
- entradas previstas;
- saídas previstas;
- saldo projetado.

A taxa de inadimplência utilizada corresponde a:

```text
Valor vencido
───────────────
Carteira aberta
```

Também foi implementada análise de **Aging**, com faixas:

- A vencer;
- Vencido de 1 a 30 dias;
- Vencido de 31 a 60 dias;
- Vencido de 61 a 90 dias;
- Vencido acima de 90 dias.

---

# AI/BI Dashboard

Foi desenvolvido o dashboard:

# Análise de Varejo 360

O dashboard é composto por seis páginas.

## 1. Visão Executiva

Indicadores gerais de:

- faturamento;
- lucro;
- margem;
- pedidos;
- clientes ativos;
- ticket médio;
- evolução das vendas;
- categorias.

![Visão Executiva](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/01_visao_geral.png)

---

## 2. Clientes

Apresenta:

- base de clientes;
- segmentação RFM;
- distribuição dos segmentos;
- participação no faturamento;
- clientes em risco;
- oportunidades de retenção.

![Clientes](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/02_clientes.png)

---

## 3. Produtos e Estoque

Apresenta:

- Curva ABC;
- situação atual do estoque;
- rupturas;
- estoque baixo;
- excesso;
- dias de cobertura;
- capital excedente;
- prioridades operacionais.

![Produtos e Estoque](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/03_produtos_estoque.png)

---

## 4. Compras

Apresenta:

- produtos para reposição;
- unidades sugeridas;
- investimento estimado;
- prioridades;
- fornecedores;
- mercadorias já em trânsito.

![Compras](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/04_compras.png)

---

## 5. Financeiro

Apresenta:

- contas a receber;
- inadimplência;
- Aging;
- contas a pagar;
- clientes inadimplentes;
- fluxo de caixa;
- saldo projetado.

![Financeiro](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/05_financeiro.png)

---

## 6. Equipe Comercial

Apresenta:

- faturamento da equipe;
- metas;
- atingimento;
- ranking de vendedores;
- clientes positivados;
- ticket médio;
- desempenho regional.

![Equipe Comercial](/Workspace/Users/anamarias.ufg@gmail.com/databricks-analise-varejo/documentacao/imagens/06_comercial.png)

---

# Estrutura do repositório

```text
databricks-analise-varejo/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── notebooks/
│   │
│   ├── 00_configuracao/
│   │   └── 00_configurar_ambiente
│   │
│   ├── 01_geracao_dados/
│   │   └── 01_gerar_dados_ficticios
│   │
│   ├── 02_bronze/
│   │   └── 02_criar_camada_bronze
│   │
│   ├── 03_silver/
│   │   └── 03_criar_camada_silver
│   │
│   ├── 04_qualidade/
│   │   └── 04_validar_qualidade
│   │
│   └── 05_gold/
│       ├── 05_analise_vendas
│       ├── 06_segmentacao_rfm
│       ├── 07_curva_abc
│       ├── 08_analise_estoque
│       ├── 09_sugestao_compras
│       └── 10_analise_financeira
│
├── consultas_sql/
│
├── documentacao/
│   ├── arquitetura.md
│   ├── modelo_dados.md
│   └── imagens/
│
└── dashboard/
    └── README.md
```

---

# Como executar

A ordem recomendada dos notebooks é:

```text
00_configurar_ambiente
        ↓
01_gerar_dados_ficticios
        ↓
02_criar_camada_bronze
        ↓
03_criar_camada_silver
        ↓
04_validar_qualidade
        ↓
05_analise_vendas
        ↓
06_segmentacao_rfm
        ↓
07_curva_abc
        ↓
08_analise_estoque
        ↓
09_sugestao_compras
        ↓
10_analise_financeira
```

Após o processamento:

```text
Databricks SQL
      ↓
AI/BI Dashboard
```

---

# Principais conceitos demonstrados

Este projeto demonstra na prática:

### Engenharia de Dados

- arquitetura Lakehouse;
- Medallion Architecture;
- ingestão de arquivos;
- Delta Lake;
- PySpark;
- tipagem;
- deduplicação;
- tratamento de dados;
- organização em dimensões e fatos.

### Qualidade de Dados

- completude;
- unicidade;
- domínio;
- integridade referencial;
- consistência financeira;
- consistência temporal;
- quarentena;
- Quality Gates.

### Data Analytics

- KPIs;
- análise temporal;
- análise de vendas;
- análise financeira;
- segmentação RFM;
- Curva ABC;
- análise de estoque;
- desempenho comercial.

### Analytics Prescritivo

- estoque de segurança;
- ponto de reposição;
- cobertura;
- priorização de produtos;
- sugestão quantitativa de compras.

### Business Intelligence

- Databricks SQL;
- AI/BI Dashboards;
- visualização executiva;
- datasets analíticos;
- exploração dos dados com Genie.

---

# Decisões de projeto

Algumas decisões importantes foram adotadas durante o desenvolvimento:

### Dados totalmente fictícios

Nenhum dado real, privado ou operacional é utilizado.

### Reprodutibilidade

A geração dos dados utiliza semente fixa.

### Separação entre camadas

Problemas existentes na origem são preservados na Bronze e tratados somente nas camadas posteriores.

### Não exclusão silenciosa

Registros inválidos são enviados para quarentena sempre que necessário.

### Quality Gate

A camada Gold não deve ser construída quando existirem falhas críticas de qualidade.

### Data de referência derivada dos dados

As análises não dependem da data atual do sistema.

Isso permite que os resultados sejam reproduzidos posteriormente.

### Regras interpretáveis

As recomendações de estoque e compras utilizam regras transparentes em vez de modelos considerados caixas-pretas.

---

# Limitações

Por se tratar de uma simulação educacional, algumas simplificações foram adotadas.

Uma solução produtiva poderia incluir:

- previsão probabilística de demanda;
- sazonalidade por produto;
- lead time variável;
- lote mínimo;
- múltiplos de embalagem;
- descontos por volume;
- validade de produtos;
- capacidade física de armazenagem;
- custos de manutenção de estoque;
- otimização matemática de compras;
- integração em tempo real com ERP;
- monitoramento automático de qualidade;
- workflows de produção.

Esses itens foram deixados fora do escopo para manter o foco na construção da solução analítica.

---

# Próximas evoluções

Possíveis extensões futuras:

- previsão de demanda;
- otimização de estoque;
- modelo de propensão de recompra;
- previsão de churn;
- recomendação de produtos;
- análise de elasticidade de preço;
- previsão de inadimplência;
- MLflow;
- Feature Engineering;
- Model Serving.

Essas evoluções podem ser desenvolvidas como projetos de Machine Learning independentes.

---

# Autor

**Ana Maria Alves**

Projeto desenvolvido para estudo e portfólio profissional nas áreas de:

- Data Analytics;
- Data Science;
- Machine Learning;
- Databricks;
- Lakehouse.
