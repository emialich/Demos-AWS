# Configuração do bucket S3 (fonte de dados)

1 - Acesse o site https://registry.opendata.aws/noaa-gsod/.

2 - Crie na sua conta da AWS um bucket para hospedar esses dados
> Esse processo pode ser feito de forma manual ou pode ser feito usando CLI
> Esses dados podem ser acessados diretamente no bucket próprio da coleta `s3://noaa-gsod-pds/` porém esse só está disponível na região us-east-1.

![image](https://github.com/user-attachments/assets/05346a56-18cb-437b-be45-c7ee742df854)

3 - Resultado final do bucket pronto (dados particionados em ano)

![image](https://github.com/user-attachments/assets/b578bfe1-45d2-4fe9-823e-2878eab8b87d)


