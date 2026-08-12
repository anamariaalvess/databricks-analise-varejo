# Modelo de Dados — Distribuidora Horizonte

## 1. Objetivo

Este documento descreve o modelo de dados utilizado no projeto **Análise de Varejo com Databricks**.

O projeto simula o ambiente operacional e analítico de uma empresa fictícia denominada **Distribuidora Horizonte**, especializada na distribuição de produtos para pequenos e médios estabelecimentos comerciais.

Todos os dados utilizados no projeto são sintéticos e foram gerados exclusivamente para fins educacionais e de portfólio.

## 2. Cenário de negócio

A Distribuidora Horizonte é uma empresa fictícia sediada no Centro-Oeste brasileiro e atende diferentes tipos de estabelecimentos comerciais.

Os principais segmentos atendidos são:

* supermercados;
* mercearias;
* padarias;
* restaurantes;
* farmácias;
* lojas de conveniência;
* escritórios.

A empresa comercializa produtos pertencentes às seguintes categorias:

* alimentos;
* bebidas;
* higiene;
* limpeza;
* papelaria;
* utilidades.

O ambiente de dados simula os principais processos da organização:

* cadastro de clientes;
* cadastro de produtos;
* cadastro de vendedores;
* cadastro de fornecedores;
* vendas;
* compras;
* estoque;
* contas a receber;
* contas a pagar;
* metas comerciais.

## 3. Período dos dados

O período simulado será:

**01/01/2024 a 31/07/2026**

A utilização de mais de dois anos de histórico permitirá análises de:

* sazonalidade;
* evolução de vendas;
* comparação entre períodos;
* desempenho comercial;
* comportamento de clientes;
* recorrência;
* inadimplência;
* estoque;
* tendências de negócio.

## 4. Volumes aproximados

| Fonte            |                   Volume esperado |
| ---------------- | --------------------------------: |
| Clientes         |                             2.000 |
| Produtos         |                               500 |
| Vendedores       |                                20 |
| Fornecedores     |                                80 |
| Pedidos de venda |            aproximadamente 40.000 |
| Itens de venda   |           aproximadamente 150.000 |
| Compras          |      aproximadamente 15.000 itens |
| Estoque          | aproximadamente 470.000 registros |
| Contas a receber |    aproximadamente 40.000 títulos |
| Contas a pagar   |    aproximadamente 10.000 títulos |
| Metas de vendas  |     aproximadamente 620 registros |

Os volumes são suficientes para demonstrar processamento distribuído e análise com Spark sem gerar uma carga excessiva para o ambiente Databricks Free Edition.

---

# 5. Clientes

Cada registro representa um cliente da distribuidora.

## Campos

| Campo            | Descrição                        |
| ---------------- | -------------------------------- |
| id_cliente       | Identificador único do cliente   |
| nome_cliente     | Nome fictício do estabelecimento |
| segmento_cliente | Segmento comercial               |
| cidade           | Cidade                           |
| estado           | Unidade federativa               |
| data_cadastro    | Data de entrada do cliente       |
| porte_cliente    | Pequeno, médio ou grande         |
| limite_credito   | Limite de crédito concedido      |
| situacao_cliente | Ativo ou inativo                 |

## Regras

Os clientes serão distribuídos entre diferentes portes e segmentos.

Clientes de maior porte terão, em média:

* pedidos de maior valor;
* maior quantidade de itens;
* maior frequência de compra;
* maiores limites de crédito.

Parte dos clientes apresentará redução gradual da frequência de compras ao longo do tempo para permitir a identificação de clientes em risco por meio da análise RFM.

---

# 6. Produtos

Cada registro representa um produto comercializado pela distribuidora.

## Campos

| Campo                   | Descrição                      |
| ----------------------- | ------------------------------ |
| id_produto              | Identificador único do produto |
| nome_produto            | Nome fictício do produto       |
| categoria               | Categoria do produto           |
| subcategoria            | Subcategoria                   |
| marca                   | Marca fictícia                 |
| unidade_medida          | Unidade comercial              |
| custo_unitario          | Custo médio do produto         |
| preco_venda             | Preço padrão de venda          |
| margem_padrao           | Margem comercial estimada      |
| id_fornecedor_principal | Principal fornecedor           |
| situacao_produto        | Ativo ou inativo               |

## Regras

As margens serão diferentes entre categorias.

Algumas categorias terão:

* alta receita e baixa margem;
* baixa receita e alta margem;
* maior sazonalidade;
* maior giro;
* maior necessidade de estoque.

Isso permitirá avaliar faturamento e rentabilidade separadamente.

---

# 7. Vendedores

Cada registro representa um integrante fictício da equipe comercial.

## Campos

| Campo             | Descrição                 |
| ----------------- | ------------------------- |
| id_vendedor       | Identificador do vendedor |
| nome_vendedor     | Nome fictício             |
| data_admissao     | Data de admissão          |
| regiao            | Região de atuação         |
| nivel             | Júnior, pleno ou sênior   |
| situacao_vendedor | Ativo ou inativo          |

## Regras

Os vendedores terão diferentes níveis de desempenho.

A equipe será dividida artificialmente em grupos de desempenho:

* alto desempenho;
* desempenho intermediário;
* desempenho abaixo da média.

Isso permitirá analisar:

* ranking;
* atingimento de metas;
* ticket médio;
* positivação de clientes;
* evolução das vendas;
* produtividade.

---

# 8. Fornecedores

Cada registro representa um fornecedor fictício.

## Campos

| Campo                | Descrição                        |
| -------------------- | -------------------------------- |
| id_fornecedor        | Identificador único              |
| nome_fornecedor      | Nome fictício                    |
| estado               | Estado de origem                 |
| prazo_medio_entrega  | Prazo médio em dias              |
| condicao_pagamento   | Condição comercial               |
| avaliacao_fornecedor | Indicador fictício de desempenho |
| situacao_fornecedor  | Ativo ou inativo                 |

Os fornecedores apresentarão diferenças de prazo, preço e confiabilidade.

---

# 9. Vendas

A granularidade principal da base de vendas será **um registro por item vendido**.

Um mesmo pedido poderá possuir vários produtos.

## Campos

| Campo               | Descrição                   |
| ------------------- | --------------------------- |
| id_item_venda       | Identificador único do item |
| id_venda            | Identificador do pedido     |
| data_venda          | Data da venda               |
| id_cliente          | Cliente                     |
| id_produto          | Produto                     |
| id_vendedor         | Vendedor                    |
| quantidade          | Quantidade vendida          |
| preco_unitario      | Preço praticado             |
| valor_bruto         | Valor antes dos descontos   |
| percentual_desconto | Percentual de desconto      |
| valor_desconto      | Valor concedido             |
| valor_liquido       | Valor final                 |
| custo_total         | Custo dos produtos vendidos |
| lucro_bruto         | Resultado bruto da venda    |
| margem_percentual   | Margem percentual           |

## Regras de comportamento

As vendas não serão distribuídas uniformemente.

Serão simulados:

* crescimento gradual da empresa;
* sazonalidade mensal;
* diferenças entre dias úteis;
* crescimento de determinadas categorias;
* clientes com maior recorrência;
* concentração de receita em parte da carteira;
* diferentes níveis de desempenho entre vendedores.

O final do ano apresentará aumento na demanda de determinadas categorias.

---

# 10. Compras

A granularidade será um registro por produto adquirido de um fornecedor.

## Campos

| Campo            | Descrição                   |
| ---------------- | --------------------------- |
| id_item_compra   | Identificador do item       |
| id_compra        | Identificador da compra     |
| data_compra      | Data da compra              |
| id_fornecedor    | Fornecedor                  |
| id_produto       | Produto                     |
| quantidade       | Quantidade comprada         |
| custo_unitario   | Valor unitário              |
| valor_total      | Valor total da compra       |
| prazo_entrega    | Prazo previsto              |
| data_recebimento | Data efetiva do recebimento |

As compras serão relacionadas à demanda e à necessidade de reposição do estoque.

---

# 11. Estoque

A base de estoque terá granularidade de **produto por dia**.

## Campos

| Campo              | Descrição                     |
| ------------------ | ----------------------------- |
| data_referencia    | Data da posição               |
| id_produto         | Produto                       |
| quantidade_estoque | Estoque disponível            |
| custo_medio        | Custo médio                   |
| valor_estoque      | Valor financeiro              |
| estoque_minimo     | Quantidade mínima recomendada |
| estoque_maximo     | Quantidade máxima recomendada |

Serão criados cenários de:

* estoque adequado;
* estoque baixo;
* ruptura;
* excesso de estoque;
* produtos com baixo giro.

Isso permitirá combinar Curva ABC e estoque para identificar prioridades de reposição.

---

# 12. Contas a receber

Cada registro representa um título originado principalmente de uma venda.

## Campos

| Campo             | Descrição                    |
| ----------------- | ---------------------------- |
| id_titulo_receber | Identificador do título      |
| id_venda          | Pedido de origem             |
| id_cliente        | Cliente                      |
| data_emissao      | Data de emissão              |
| data_vencimento   | Data de vencimento           |
| data_pagamento    | Data do pagamento            |
| valor_titulo      | Valor                        |
| status_titulo     | Status financeiro            |
| dias_atraso       | Quantidade de dias em atraso |

## Status possíveis

* A vencer
* Pago em dia
* Pago em atraso
* Vencido

Parte da carteira apresentará atrasos de pagamento.

A probabilidade de inadimplência não será igual para todos os clientes, permitindo que segmentos diferentes apresentem comportamentos financeiros distintos.

---

# 13. Contas a pagar

Cada registro representa uma obrigação financeira da distribuidora.

## Campos

| Campo           | Descrição                 |
| --------------- | ------------------------- |
| id_titulo_pagar | Identificador             |
| id_compra       | Compra relacionada        |
| id_fornecedor   | Fornecedor                |
| data_emissao    | Data de emissão           |
| data_vencimento | Data de vencimento        |
| data_pagamento  | Data efetiva de pagamento |
| valor_titulo    | Valor                     |
| status_titulo   | Situação                  |

Os pagamentos serão majoritariamente realizados dentro do vencimento, com uma pequena parcela de atrasos simulados.

---

# 14. Metas de vendas

A granularidade será:

**vendedor por mês**.

## Campos

| Campo         | Descrição                    |
| ------------- | ---------------------------- |
| ano_mes       | Mês de referência            |
| id_vendedor   | Vendedor                     |
| valor_meta    | Meta de faturamento          |
| meta_clientes | Meta de clientes positivados |

As metas serão baseadas no histórico de desempenho do vendedor com um componente de crescimento esperado.

---

# 15. Padrões analíticos intencionais

A base será construída para conter padrões que possam ser identificados durante a análise.

Entre eles:

### Concentração de receita

Uma parcela relativamente pequena dos clientes será responsável por grande parte do faturamento.

### Curva ABC

Uma parcela reduzida dos produtos será responsável pela maior parte das vendas.

### Clientes recorrentes

Alguns clientes apresentarão elevada frequência e valor de compras.

### Clientes em risco

Alguns clientes historicamente relevantes passarão longos períodos sem comprar.

### Clientes novos

Haverá entrada contínua de novos clientes ao longo do período.

### Sazonalidade

Alguns meses e categorias apresentarão maior volume de vendas.

### Diferença entre vendedores

O desempenho comercial será heterogêneo.

### Ruptura de estoque

Alguns produtos de alta demanda apresentarão períodos de estoque insuficiente.

### Excesso de estoque

Produtos de menor giro poderão apresentar estoques superiores à demanda esperada.

### Inadimplência

Parte da carteira de clientes apresentará atraso ou não pagamento de títulos.

---

# 16. Problemas controlados de qualidade

A camada de dados brutos conterá propositalmente uma pequena quantidade de problemas de qualidade.

Esses problemas serão utilizados para demonstrar os processos de tratamento realizados na camada Silver.

Poderão ser incluídos:

* registros duplicados;
* pequenas quantidades de valores nulos;
* diferenças de capitalização de textos;
* espaços adicionais em campos textuais;
* registros inconsistentes controlados.

Os problemas serão limitados para não comprometer a coerência geral do conjunto de dados.

---

# 17. Reprodutibilidade

A geração dos dados utilizará uma semente aleatória fixa:

```python
SEMENTE = 42
```

O uso da semente permitirá reproduzir os mesmos dados sempre que o processo for executado novamente.

Datas de referência utilizadas nos cálculos também serão derivadas do próprio conjunto de dados sempre que possível, evitando dependência da data em que os notebooks forem executados.

---

# 18. Arquitetura de destino

Os dados seguirão a arquitetura:

```text
Dados fictícios
      ↓
Landing
      ↓
Bronze
      ↓
Silver
      ↓
Gold
      ↓
Databricks SQL
      ↓
AI/BI Dashboards
      ↓
Genie
```

A camada Bronze armazenará os dados próximos de sua forma original.

A camada Silver realizará tratamentos, padronizações, validações e integração.

A camada Gold disponibilizará estruturas orientadas às análises de negócio.
