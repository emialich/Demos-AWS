# Glue Studio e trabalhos ETL

O AWS Glue Studio é uma interface gráfica oferecida pela Amazon Web Services (AWS) que facilita a criação, edição e execução de pipelines de dados para tarefas de ETL (Extract, Transform, Load) na nuvem. Ele permite que desenvolvedores e analistas de dados criem workflows de ETL de forma visual e intuitiva, sem a necessidade de escrever muito código. O Glue Studio é parte do serviço AWS Glue, que é uma plataforma de integração de dados totalmente gerenciada, projetada para ajudar a mover e transformar dados entre diferentes fontes e destinos de forma eficiente.

### O que é ETL?
ETL é um processo fundamental na integração e preparação de dados para análise. Ele se divide nas seguintes etapas:

1. Extract (Extrair): A primeira etapa é a extração de dados de diferentes fontes. Isso pode incluir bancos de dados relacionais, arquivos CSV, logs, APIs, entre outros.

2. Transform (Transformar): Após a extração, os dados passam por um processo de transformação, no qual são limpos, filtrados, enriquecidos e, por vezes, consolidados. Isso é feito para garantir que os dados estejam no formato adequado para análise ou para serem carregados em outro sistema.

3. Load (Carregar): Por fim, os dados transformados são carregados em um destino de armazenamento ou análise, como um data warehouse, banco de dados relacional ou data lake.

# Criar um trabalho de ETL no Glue Studio
1. Acesse o menu de ETL jobs do Glue. Clique em Viual ETL.

>Visual ETL (Visual Interface): Para quem prefere uma abordagem sem código, usando arrastar e soltar para criar fluxos de dados.
>Notebook: Para quem deseja mais controle e interatividade com o código, utilizando notebooks Jupyter para escrever e testar código em Python ou Scala.
>Script Editor: Para desenvolvedores experientes que preferem escrever código manualmente em um editor de scripts, com maior flexibilidade e controle.

![image](https://github.com/user-attachments/assets/28661824-dd89-49a9-a67a-3b3f22e451ee)

2. Selecione no campo Add Nodes, a fonte S3. Especifique o nome, e a localização do data catalog que criamos. Selecione a tabela do catálogo que possui os dados.
> Podemos adicionar visualmente nós de transformação e alvos. Esses permitem a realização de alterações e validações nos nossos dados.
3. Selecionamos uma trasnformação ***change schema***. Nesse exemplo breve, vamos alterar o nome da coluna e o tipo de dado.
- Altere o campo date para report_date e o tipo de dado para date.
- Remova as linhas com tipo de dado long, faça isso clicando em drop.
![image](https://github.com/user-attachments/assets/48890ac8-8906-4a66-8408-f25de465dd1e)

4. Adicione um novo nó, e especifique um target (no caso, usaremos o S3).

- Em Format (Formatar), selecione Parquet. Não utilize qualquer tipo de compressão.
- Em S3 Target Location (Local alvo no S3), escolha um bucket de destino.
Para opções de atualização do Catálogo de Dados, selecione Create a table in the Data Catalog (Criar uma tabela no Catálogo de Dados) e, nas execuções subsequentes, atualize o esquema e adicione novas partições.
- Em Database (Banco de dados), selecione o database criado anteriormente.
- Em Table name insira um novo nome como data_parquet.
- Escolha Add a partition key (Adicionar uma chave de partição) e, em Partition (0) (Partição), selecione report_date (data_do_relatório).

5. Salve o Job (verifique os nomes em job details).

   ![image](https://github.com/user-attachments/assets/bf711bec-fedc-4dc8-86c5-b788925b1b85)
   ![image](https://github.com/user-attachments/assets/e560a8da-980a-49bb-b961-6ae55ab48497)

6. Acesse `Tables` em `Data Catalog`. Clique em `View Data` na coluna que representa a nova tabela transformada. Isso abrirá o Athena.
7. Antes de executar as queries, selecione um campo para armazenar onde os resultados serão armazenados.
8. Execute algumas queries para pesquisar os dados:
```
SELECT report_date, latitude, longitude, temp, prcp 
FROM "weather_data"."data_parquet" 
WHERE report_date between CAST('2022-01-01' AS DATE) and CAST('2022-06-30' AS DATE)
ORDER BY report_date
```
```
SELECT report_date, avg(temp) as "Avg Temp", avg(prcp ) as "Avg Prcp"
FROM "weather_data"."data_parquet"
WHERE report_date between CAST('2022-01-01' AS DATE) and CAST('2022-09-30' AS DATE)
Group by report_date
Order by 3 desc
```
> Essa consulta mostra as médias de temperatura e precipitação de cada report_date entre 1º de janeiro de 2022 e 30 de setembro de 2022. Os dados retornados se organizam de modo a exibir primeiro o dia mais úmido do período
