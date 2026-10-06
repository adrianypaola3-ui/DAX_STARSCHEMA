# DAX_STARSCHEMA
# 📊 Desafio Power BI: Modelagem Dimensional (Star Schema) - Financial Sample

Este repositório contém a solução desenvolvida para o desafio de modelagem de dados utilizando o Power BI e o conjunto de dados **Financial Sample**. 
O objetivo principal foi transformar uma tabela única e plana em uma arquitetura robusta de **Modelo em Estrela (Star Schema)**, aplicando boas práticas de ETL, tratamento no Power Query e modelagem relacional.

---

## 🛠️ Arquitetura do Modelo (Star Schema)

O modelo foi estruturado centralizando as transações na tabela de fatos e distribuindo os contextos nas tabelas de dimensão ao redor:

* **Tabela Fato (Centro):**
  * `F_Vendas`: Contém a chave primária transacional (`SK_ID`), a chave estrangeira (`ID_Produto`), além de métricas e atributos transacionais (como *Units Sold*, *Sales Price*, *Sales*, *Profit*, *Date*, *Segment*, *Country*, etc.)[cite: 2].
* **Tabelas Dimensão (Ao redor):**
  * `D_Produtos`: Agrupamento por produto contendo o `ID_produto`, nome do produto e métricas agregadas (média de unidades vendidas, valores mínimos, máximos e medianas)[cite: 2].
  * `D_Produtos_Detalhes`: Contém `ID_produtos`, *Discount Band*, *Sale Price*, *Units Sold* e *Manufacturing Price*[cite: 2].
  * `D_Descontos`: Contém `ID_produto`, *Discount* e *Discount Band*[cite: 2].
  * `D_Detalhes`: Agrupa informações complementares de vendas não contempladas nas demais dimensões[cite: 2].
  * `D_Calendário`: Tabela de dimensão temporal gerada via DAX[cite: 2].
* **Controle e Backup:**
  * `financials_origem`: Tabela base mantida em **modo oculto (backup)** exclusivamente para alimentar as referências no Power Query[cite: 2].

---

## ⚙️ Passo a Passo do Processo de Construção (ETL & Power Query)

1. **Importação e Cópia por Referência:**
   * Importação da base original `financials_origem`[cite: 2].
   * Criação de consultas independentes a partir da origem utilizando a função de **Referência** do Power Query para gerar cada tabela de dimensão e a tabela fato.

2. **Agrupamento e Tratamento de Dados (`D_Produtos`):**
   * Utilização da ferramenta **Agrupar por** (modo avançado) para consolidar os produtos de forma única[cite: 2].
   * Adição de agregações estatísticas (soma, média, mínimo, máximo e mediana) baseadas nas colunas de vendas e preços[cite: 2].

3. **Criação de Chaves (IDs e Índices):**
   * Adição de **Coluna de Índice** na tabela `D_Produtos` para gerar o identificador único numérico (`ID_Produto`).
   * Adição de **Coluna de Índice** na tabela `F_Vendas` para gerar a chave primária transacional (`SK_ID`)[cite: 2].
   * Propagação dos identificadores para as demais tabelas através de operações de **Mesclagem (*Merge*)** baseadas na coluna de produto.

4. **Criação da Tabela Calendário (DAX):**
   * Utilização da função `CALENDAR()` no Power BI para estruturar a dimensão de datas com base no intervalo transacional da tabela de fatos[cite: 2]:
     ```dax
     D_Calendário = CALENDAR(MIN(F_Vendas[Date]), MAX(F_Vendas[Date]))
     ```

---

## 🔗 Relacionamentos do Modelo (Vista de Modelo)

Na vista de modelo do Power BI, todas as tabelas de dimensão foram conectadas diretamente à tabela central de fatos `F_Vendas` através de relacionamentos de **Um para Muitos (1 to \*)**:
* `D_Produtos[ID_Produto]` ──> `F_Vendas[ID_Produto]`
* `D_Calendário[Date]` ──> `F_Vendas[Date]`
* Conexões correspondentes para as tabelas `D_Descontos`, `D_Produtos_Detalhes` e `D_Detalhes`.
* A tabela de origem `financials_origem` foi mantida oculta e isolada, servindo apenas como camada de segurança e histórico de ETL.

---

## 🚀 Como Executar o Projeto

1. Certifique-se de ter o **Power BI Desktop** instalado.
2. Descarregue o ficheiro do projeto `.pbix` deste repositório[cite: 5].
3. Abra o ficheiro para explorar a estrutura no **Power Query** e o diagrama relacional na **Vista de Modelo**.
