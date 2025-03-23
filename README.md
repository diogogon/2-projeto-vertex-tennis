### <p align="center"><strong>[DG-02] Painel de Análise de Resultados da Vertex Tennis</strong></p>
### <p align="center"><strong>[Painel Vertex Tennis](https://app.powerbi.com/view?r=eyJrIjoiZDZlYzZjM2YtNGI4MS00YzVkLTkyOGMtZjA3MzAzZmQyYjBjIiwidCI6IjI4ZThlYTA4LWE5N2EtNGExYS05ZjU0LWZhMGZmMzc1NDNlYSJ9)</strong></p>

### Objetivo do Documento:
O objetivo do documento é fornecer uma visão clara e detalhada sobre todos os aspectos essenciais do projeto. Isso inclui definir as metas e objetivos do dashboard, descrever os requisitos funcionais e não funcionais, estabelecer os usuários-alvo e suas necessidades específicas, delinear o escopo do projeto, identificar os recursos e tecnologias a serem utilizados.

### Justificativa do Projeto:
A abordagem atual de análise de dados da Vertex Tennis tem se mostrado ineficiente, pois os analistas não conseguem cumprir adequadamente suas funções devido à sobrecarga de trabalho. Isso ocorre porque grande parte do tempo deles é dedicada à criação manual de relatórios, impedindo o foco em atividades analíticas.

Vale ressaltar, que uma das principais dificuldades associadas ao trabalho manual é o aumento significativo do risco de erros e inconsistências nos dados. Isso ocorre por diversas razões, como, por exemplo, a inserção de dados em planilhas, a atualização de sistemas ou a coleta de informações de diferentes fontes, que frequentemente apresentam formatos e estruturas distintas. Esses processos manuais aumentam a probabilidade de falhas humanas.

Nesse cenário, é evidente que a implementação de uma solução de análise de dados mais eficiente e integrada se faz essencial para otimizar o trabalho da equipe, reduzir os riscos de erros e garantir uma análise mais assertiva e ágil.

### Responsabilidades das funções:
Diogo Gonçalves (eu): como *Analista de dados*, fui responsável por todas as etapas do projeto: coleta e ingestão de dados, estruturação e modelagem dos dados, Design, DataViz, documentação do projeto, desenvolvimento de funcionalidades analíticas (Regras e Cálculos) e publicação.

### Escopo:  
#### 🎯 Objetivo:
Dada a complexidade e os desafios na utilização dos dados disponíveis, o objetivo deste projeto é fornecer, por meio de uma solução analítica, uma visão clara e integrada das informações financeiras e operacionais essenciais para o negócio. Isso fortalecerá a capacidade da empresa de antecipar tendências, responder rapidamente aos desafios do mercado e, assim, promover o crescimento e a competitividade no setor.

#### 🫂 Público-Alvo:  
Diretoria, Gerentes e Analistas de dados da Vertex Tennis.

#### 🗓️ Recorrência de Atualização:  
Diariamente ao meio dia.

#### 📗 Descrição:  

*A) Ingestão de Dados*: processo para estabelecer a conexão entre as pastas de arquivos Excel e a plataforma de análise, garantindo que as informações sejam importadas de maneira eficiente e precisa para posterior processamento e análise.
1. Setor Vendas: Nome padronizado dos arquivos: Acompanhamento_Comercial_jan/2025.xlsx. Eles contêm 3 abas: a) Registro histórico das vendas, b) Cadastro de produto e c) Depara de Subcategorias.
2. Setor Importação: Nome padronizado dos arquivos: Importacoes_FornecedorA.xlsx. Nesse caso, os arquivos possuem uma infinidade de abas indicando as informações de cada Trimestre/Ano.

*B) Transformação de Dados*: focaremos no processo de uniformização de formatos e unidades, seleção e filtragem dos dados relevantes. Essas atividades serão realizadas continuamente no Power Query do Power BI.  

1. Desafio de Conversão USD para BRL/BRB: Os preços dos produtos estão inicialmente definidos em dólares, e para realizar a conversão dinâmica para reais, estabelecemos uma conexão com a API do [Banco Central do Brasil](https://dadosabertos.bcb.gov.br/dataset/dolar-americano-usd-todos-os-boletins-diarios/resource/22ab054c-b3ff-4864-82f7-b2815c7a77ec?inner_span=True). Isso nos permite obter as taxas de câmbio mais atualizadas. Abaixo está o cerne das etapas em M.
```M
let
    Data_Atual = Date.ToText(#date(Date.Year(DateTime.LocalNow()), Date.Month(DateTime.LocalNow()), Date.Day(DateTime.LocalNow())), "MM-dd-yyyy"),
    Data_Min = Date.ToText(Date.AddMonths(p_Data_Min, -1), "MM-dd-yyyy"),
    Fonte = Json.Document(Web.Contents("https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/odata/CotacaoDolarPeriodo(dataInicial=@dataInicial,dataFinalCotacao=@dataFinalCotacao)?@dataInicial='"&Data_Min&"'&@dataFinalCotacao='"&Data_Atual&"'&&$format=json&$select=cotacaoCompra,dataHoraCotacao")),
    #"Convertido para Tabela" = Table.FromRecords({Fonte}),
```
2. Função Personalizada M: empilhar os arquivos de importação de forma eficiente. Indicarei o processo mais fácil para familiarizar o processo.

4. Modelagem de dados: adotação da abordagem de modelo estrela, que organiza os dados em tabelas de fatos e dimensões para otimizar o processo de análise. As tabelas de fato serão responsáveis por armazenar os dados quantitativos e transacionais, enquanto as tabelas de dimensão fornecerão as informações contextuais necessárias para a análise.

As principais tabelas serão:
a) fact_Vendas: Registra as transações de vendas realizadas.
b) fact_Importação: Armazena os dados relacionados ao processo de importação de produtos.
c) dim_Produto: Contém informações sobre os produtos, como categorias e características.
d) dim_Clientes: Registra dados sobre os clientes, como localização e perfil.
e) dim_Fornecedores: Registra dados sobre os fornecedores.

*D) DataViz*: processo de construção de layout, design visual e visualizações adequadas para os dados. Todo o design foi feito no Figma e, para este projeto pessoal, foi interessante seguir os padrões estabelecidos pela [identidade visual](https://vertextennis.com/sobre/) da Vertex Tennis, tanto para as cores como para a marca.

#### ⚙️ Fontes:  

### Exclusões:

### Premissas:

### Inconsistências e observações:

### Regras de Negócio:
1. Métricas do Balanço Patrimonial:
```dax

```
2. Métricas da Demonstração do Resultado do Exercício:
```dax

```
3. Indicadores financeiros:
```dax

```
### Considerações Finais
Estou aberto a dúvidas e sugestões adicionais para garantir que o dashboard atenda plenamente às suas necessidades e expectativas do projeto.
