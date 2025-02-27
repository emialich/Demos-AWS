# Glue Crawler

Você pode usar um Crawler do AWS Glue para preencher o AWS Glue Data Catalog com bancos de dados e tabelas. Este é o principal método usado pela maioria dos usuários do AWS Glue. 
Um crawler pode rastrear vários armazenamentos de dados em uma única execução. Após a conclusão, o crawler cria ou atualiza uma ou mais tabelas no Data Catalog. As tarefas de extração, transformação e carregamento (ETL) que você define no AWS Glue usam essas tabelas do Data Catalog como fontes e destinos. 
O trabalho de ETL lê e grava os armazenamentos de dados que são especificados nas tabelas do Data Catalog de fonte e de destino.

![PopulateCatalog-overview](https://github.com/user-attachments/assets/e80b4926-6aad-4e5e-a80d-1e8aed93ac61)

### Fluxo de Trabalho de um Crawler no AWS Glue  

1. O **crawler** executa **classificadores personalizados** na ordem definida para inferir o esquema dos dados.  
2. O **primeiro classificador** que reconhecer a estrutura cria o esquema; os demais são ignorados.  
3. Se nenhum classificador personalizado corresponder, **classificadores integrados** (ex.: JSON) tentam identificar o formato.  
4. O **crawler se conecta ao armazenamento de dados**, podendo exigir propriedades de conexão.  
5. O **esquema inferido é gerado** e os **metadados são gravados no AWS Glue Data Catalog**.  
6. As **tabelas criadas** armazenam metadados sobre os dados e incluem informações do classificador utilizado.  

### Ações realizadas por um Crawler no AWS Glue  

Quando um crawler é executado, ele segue estas etapas para interrogar um armazenamento de dados:  

1. **Classifica os dados** – Determina o formato, o esquema e as propriedades dos dados brutos.  
   - É possível configurar essa classificação criando um **classificador personalizado**.  
2. **Agrupa os dados em tabelas ou partições** – O agrupamento é feito com base na **heurística do crawler**.  
3. **Grava metadados no Data Catalog** – O comportamento do crawler pode ser configurado para **adicionar, atualizar ou excluir tabelas e partições**.  

# Configuração do Crawler para o Laboratório
1. Acesse o **AWS Glue**, no Menu de catálogo de dados
2. Crie um Crawler
   
![image](https://github.com/user-attachments/assets/cfc04e3c-9a8a-42c8-b10d-41404188e179)
4. Selecione a opção apropriada para Seus dados já estão mapeados para tabelas do AWS Glue? escolha 'Ainda não' ou 'Sim'. Devemos selecionar 'Ainda não'.
> O crawler pode acessar armazenamentos de dados diretamente como a origem do crawl, ou pode usar tabelas existentes no Data Catalog como a origem. Se o crawler usar tabelas de catálogo existentes, ele rastreia os armazenamentos de dados que são especificados por essas tabelas de catálogo.
4. Adicione o S3 como fonte de dados e especifique o arn do Bucket, faça o Crawl em todas as subpastas (afinal, os dados estão particionados).

![image](https://github.com/user-attachments/assets/47e8f9cc-5225-43d8-ae13-e6ae6d8a61cc)
> Podemos adicionar padrões de exclusão em cenários onde não precisamos mapear TODOS os dados de uma fonte. Podemos usar caracteres coringas e expressões regulares.
> Por exemplo, se quisessemos apenas os dados de 2010 para frente, fariamos os seguintes padrões de exclusão: `19**` e `200*`
5. Defina a IAM Role associada ao Crawler (crie uma ou selecione uma já pré-criada)
> A policy gerenciada `AWSGlueServiceRole` é suficiente, caso opte por criar uma política customizada. Certifique-se também que o Glue terá acesso ao bucket, por meio de uma política de allow ou uma bucket policy que permita como tal.
6. Defina o local onde será armazenado a saída e outras configurações. Especifique o banco dados (crie um do zero) como destino. Execute o crawler.

> Bancos de dados são usados para organizar tabelas de metadados no AWS Glue. Quando você define uma tabela no AWS Glue Data Catalog, você a adiciona a um banco de dados. Uma tabela pode estar em apenas um banco de dados.

> Seu banco de dados pode conter tabelas que definem dados de diversos armazéns de dados diferentes. Esses dados podem incluir objetos no Amazon Simple Storage Service (Amazon S3) e tabelas relacionais no Amazon Relational Database Service.

![image](https://github.com/user-attachments/assets/467db416-5804-446d-8a7b-ff64ce69d3df)

> As opções incluem como o rastreador deve manipular alterações de esquema detectadas, objetos excluídos no armazenamento de dados e muito mais.
> Cronograma do rastreador
> Você pode executar um rastreador sob demanda ou definir uma programação baseada em tempo para seus rastreadores e trabalhos no AWS Glue. A definição dessas programações usa a sintaxe cron semelhante ao Unix. 
