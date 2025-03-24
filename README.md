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
        #"Convertido para Tabela" = Table.FromRecords({Fonte})
    ```
2. Empilhamento eficiente dos Arquivos de Importação (Função): Para lidar com múltiplos fornecedores e otimizar o empilhamento dos dados, precisamos de uma abordagem mais avançada do que simplesmente expandir o campo Content. Em vez de empilhar diretamente os dados, vamos criar uma função personalizada que facilitará esse processo.

    O código abaixo serve como ponto de partida para essa função. Inicialmente, vamos simular que estamos trabalhando apenas com o FornecedorA (3ª linha). A partir desse momento, avançaremos até antes de expandir o conteúdo, preparando a base para lidar com os dados de forma organizada e controlada.
    ```m
    let
        #"Pasta dos Arquivos"  = Folder.Files(aux_Folder_Imp),
        #"Mantém Conteúdo e Fornecedores" = Table.SelectColumns(#"Pasta dos Arquivos",{"Name", "Content"}),
        #"Linhas Filtradas" = Table.SelectRows(#"Mantém Conteúdo e Fornecedores", each ([Name] = "Importacoes_FornecedorA.xlsx")),
        #"Arquivo do Fornecedor" = #"Linhas Filtradas"{0}[Content],
        #"Conteúdo do Arquivo" = Excel.Workbook(#"Arquivo do Fornecedor"),
        #"Seleção dos Trimestres" = Table.SelectRows(#"Conteúdo do Arquivo", each ([Kind] = "Sheet")),
        #"Remove o Excesso" = Table.RemoveColumns(#"Seleção dos Trimestres",{"Item", "Kind", "Hidden"})
    in
        #"Remove o Excesso"
    ```
    Agora, clique com o botão direito na consulta e transforme-a em uma função. Isso irá adicionar uma linha no início do código com os parâmetros necessários, que no nosso caso é apenas o diretório dos arquivos, chamado aux_Folder_Imp. A linha gerada será algo como: (aux_Folder_Imp as any) => let. 
    
    Seguindo nosso processo, precisamos automatizar o processo para incluir outros fornecedores. Lembre-se de que, na primeira etapa, filtramos um único fornecedor para realizar o processo inicial. Vamos modificar esse filtro para operar a nível de linha da tabela. No código atual, temos a seguinte linha para filtrar o fornecedor específico:
    => Table.SelectRows(#"Mantém Conteúdo e Fornecedores", each ([Name] = "Importacoes_FornecedorA.xlsx")
    Para automatizar, vamos substituí-los por variáveis dinâmicas: Tabela ocupada por "Mantém Conteúdo e Fornecedores" e SupplierID ocupado por "Importacoes_FornecedorA.xlsx".
    ```m
    let
        Fonte = (SupplierID as text, Tabela as table) =>
        let
            #"Linhas Filtradas" = Table.SelectRows(Tabela, each ([Name] = SupplierID)),
            #"Arquivo do Fornecedor" = #"Linhas Filtradas"{0}[Content],
            #"Conteúdo do Arquivo" = Excel.Workbook(#"Arquivo do Fornecedor"),
            #"Seleção dos Trimestres" = Table.SelectRows(#"Conteúdo do Arquivo", each ([Kind] = "Sheet")),
            #"Remove o Excesso" = Table.RemoveColumns(#"Seleção dos Trimestres",{"Item", "Kind", "Hidden"})
        in
        #"Remove o Excesso"
    in
        Fonte
    ```
    Para finalizar, basta construir uma consulta principal para os arquivos de importação e Invocar Função Personalizada. Será pedido dois itens (SupplierID e Tabela). No SupplierID, coloque o campo Content e em Tabela coloque qualquer uma que tiver disponível, mas depois troque, na barra de fórmulas, para sua etapa anterior. E pronto, vai empilhar todos os trimestres para cada fornecedor.

3. Modelagem de dados: Adoção da abordagem de modelo estrela, que organiza os dados em tabelas de fatos e dimensões para otimizar o processo de análise. As tabelas de fato serão responsáveis por armazenar os dados quantitativos e transacionais, enquanto as tabelas de dimensão fornecerão as informações contextuais necessárias para a análise. As principais tabelas serão:

    i) fact_Vendas: Registra as transações de vendas realizadas.  
    ii) fact_Importação: Armazena os dados relacionados ao processo de importação de produtos.  
    iii) dim_Produto: Contém informações sobre os produtos, como categorias e características.  
    iv) dim_Clientes: Registra dados sobre os clientes, como localização e perfil.  
    v) dim_Fornecedores: Registra dados sobre os fornecedores.

*D) DataViz*: processo de construção de layout, design visual e visualizações adequadas para os dados. Todo o design foi feito no Figma e, para este projeto, foi necessário seguir os padrões estabelecidos pela [identidade visual](https://vertextennis.com/sobre/) da Vertex Tennis, tanto para as cores como para a marca.

#### ⚙️ Fontes:  
1. Coleção de Arquivos de Vendas. Modelo: Acompanhamento_Comercial_jan/2025.xlsx;
2. Coleção de Arquivos de Importação. Modelo: Importacoes_FornecedorA.xlsx.

### Exclusões:
1. A empresa atualmente trabalha apenas com excel e não possui um banco de dados com maturidade suficiente para suportar decisões estratégicas;
2. O campo de custo médio dos produtos no cadastro é ineficaz para a dinâmica do mercado.

### Premissas:
1. O projeto considerará as informações consolidadas e trimestrais dos arquivos;
2. Visualização de Dados, Ingestão de Dados e ETL no Power BI;
3. A conversão do dólar deve ser acompahada pelo mercado;
4. O COGS será calculado com base nos custos das fichas de importação;
5. Inclusão de visual específico utilizado frequentemente em reuniões (Requisição do gerente comercial)

### Inconsistências e observações:
1. O COGS é calculado a nível trimestral, mas para incorporá-los nas vendas devem ser vinculados às datas de ordem de pedido. Isso significa que os valores irão se repetir até a nova ocorrência nas fichas;
2. Os arquivos disponibilizados não podem sofrer alterações de nomenclatura ou metadados por causa de requisitos de integridade e rastreabilidade, que são essenciais para garantir as etapas no power query.

### Regras de Negócio:
1. Métricas Base
```dax:
Custo Unitário Real: Com base nos custos de compra registrado na importação correspondente, considerar sempre a data da última compra disponível para calcular o custo unitário real.
COGS: Custo Unitário Real * Quantidade Vendida
Receita Líquida: Receita Bruta (Faturamento) - Descontos
Lucro Bruto: Receita Líquida - COGS
Margem Bruta: Lucro Bruto / Receita Líquida
```
1. Métricas de Clientes:
```dax
Clientes Novos = Aqueles que realizaram sua primeira compra no período analisado;
Clientes Antigos = Aqueles que já compraram em períodos passados e também comparam no período analisado, demonstrando fidelidade;
Clientes Sem Compra: Aqueles que não realizaram compras no período, indicando possível perda de engajamento.
```
2. Métricas de Estoque:
```dax
Saldo de Estoque ao Longo do Tempo: Combinar os dados de vendas e compras para determinar os saldos de estoque por produto ao longo do tempo.
Giro de Estoque: Medir quantas vezes o estoque foi renovado (ou girado) durante um período (Giro = Quantidade Vendida no Período / Estoque Médio)
Estoque Médio: (Estoque inicial + Estoque Final)/2
Estoque de Segurança: Calcular um estoque mínimo necessário para proteger contra variações na demanda ou atrasos no reabastecimento (SS = Nivel de Serviço (1,65 para 95% de confiança) * σ Demanda Diária * √ Lead Time )
Ponto de Reposição: Momento exato em que um novo pedido deve ser feito para evitar rupturas no estoque (ROP = Demanda Média Diária * Lead Time + SS)
```
4. Métricas de Segmentação:
```dax
Segmentação dos Níveis de Estoque: a) Ruptura: Estoque final zero ou negativo; b) Crítico: Estoque final abaixo do estoque de segurança; c) Ponto de Pedido: Estoque final abaixo do ponto de reposição; e d) Estoque Alto: Estoque acima do ponto de reposição;
Segmentação ABC: A: Itens que acumulam até 70% da margem bruta; B: Itens entre 70% e 90% da margem bruta; e C: Itens com os 10% restantes da margem bruta;
Segmentação TOPN: Seleção dinâmica de um número Top N de famílias de produtos.
```

### Considerações Finais
Estou aberto a dúvidas e sugestões adicionais para garantir que o dashboard atenda plenamente às suas necessidades e expectativas do projeto.
