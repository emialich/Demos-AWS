# AWS Glue Crawler, Studio, Athena

O AWS Glue é um serviço de integração de dados com tecnologia sem servidor que facilita a descoberta, preparação, movimentação e integração de dados de várias fontes para análise, machine learning (ML) e desenvolvimento de aplicações.
![Glue](https://d1.awsstatic.com/reInvent/reinvent-2022/glue/Product-Page-Diagram_AWS-Glue_for-Ray%402x.f34b47cf0280c7d843ea457b704ea512bebd91d5.png)
Neste laboratório, utilizaremos um subconjunto do conjunto de dados Global Surface Summary of Day (GSOD) da NOAA, disponível no AWS Open Data Registry (acessado em 5 de agosto de 2022). Esse conjunto reúne registros diários de variáveis meteorológicas, como temperatura, pressão atmosférica e velocidade do vento, coletados por mais de 9.000 estações ao redor do mundo. Para otimizar o tempo de execução, trabalharemos com uma versão reduzida dos dados, contendo apenas um recorte dos anos disponíveis.

Objetivos do laboratório:

- Configurar um crawler no AWS Glue para descobrir e catalogar dados automaticamente.
- Criar e executar um job no AWS Glue Studio para processar e transformar os dados.
- Compreender as permissões necessárias para operar crawlers e jobs no AWS Glue.
- Realizar consultas no AWS Glue Data Catalog utilizando o Amazon Athena.

> **Observação:** Esse laboratório pode incorrer custos, tome o devido cuidado e responsabilidade ao seguir esses passos

[Step 1 - Configuração de Dados do Crawler](https://github.com/emialich/main/blob/main/glue/crawler/1-DataSetup.md)

[Step 2 - Glue Intro + Setup Cralwer](https://github.com/emialich/main/blob/main/glue/crawler/2-CrawlerSetup.md)

[Step 3 - ETL + Search with Athena](https://github.com/emialich/main/blob/main/glue/crawler/3-ETL%20Transform.md)
