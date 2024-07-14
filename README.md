# Capstone Project

## Verificar Repositório fornecido para o desenvolvimento da sua arquitetura

1. Verificar VPC
   > Exemplo VPC

2. Verificar Subnets
   - Public 1: 10.0.0.0/24
   - Public 2: 10.0.1.0/24
   - Private 1: 10.0.2.0/23
   - Private 2: 10.0.4.0/23

3. Verificar Internet Gateway's (IG's | IGW's)
   > Exemplo IGW

4. Verificar Route Tables
   - Tabela de Rotas Pública
   - Tabela de Rotas Privada

5. Verificar Security Groups (SG's)
   - Bastion-SG
   - Inventory-App
   - Exemplo-DBSG
   - ALBSG

## Desenvolvendo a Aplicação

1. Criação do Load Balancer
   (distribui eficientemente o tráfego de rede entre um grupo de servidores backend)
   - Application Load Balancer
     - Nome: CapstoneELB
     - Mapeamento de Rede: Exemplo VPC
     - Mapeamento: Subnets Públicas 1 e 2
     - Security Group: ALBSG
     - Listeners e Roteamento: Criar Target Group
       - Tipo: Instâncias
       - Nome: CapstoneTG
       - HTTP 80
       - Exemplo VPC
       - Selecionar Instância Bastion
         - Incluir como pendente
       - Criar TG
     - Listeners e Roteamento: CapstoneTG

   - DNS Endpoint do CapstoneELB: CapstoneELB-1473392172.us-east-1.elb.amazonaws.com

2. Criação do Auto Scaling Group
   - Auto Scaling Group
     - Nome: CapstoneASG
     - Template: Exemplo-LT
     - Exemplo VPC
     - Mapeamento: Subnets Públicas 1 e 2
     - Load Balancer (anexar a um LB existente | Escolher a partir dos seus TG's): CapstoneTG | HTTP 80
     - Tamanho do Grupo:
       - Capacidade Desejada: 1
       - Capacidade Mínima: 1
       - Capacidade Máxima: 2

3. Criação do Banco de Dados Relacional
   - Criar o DB Subnet Group
     - Grupo de Subnet
       - Nome: DBSG
       - Descrição: DBSG
       - VPC: Exemplo VPC
       - Zonas de Disponibilidade: 1a e 1b
       - Subnets: Privada 1 e 2 (10.0.2.0/23 & 10.0.4.0/23)

   - Banco de Dados
     - Criar DB
       - Padrão
       - MySQL
       - Dev / Teste
       - Instância de DB Multi AZ
       - ID: ExemploDB
         - Nome: admin
         - Senha: password
       - Configuração da Instância:
         - Classes Burstables
         - db.t3.micro
       - Armazenamento:
         - gp2 (SSD de Propósito Geral)
         - Armazenamento alocado: 20 GiB
       - VPC: Exemplo VPC
       - DB Subnet Group: DBSG
       - Firewall do DB Subnet Group: Exemplo-DB
       - Desativar monitoramento aprimorado
       - Configuração adicional
         - Nome inicial: ExemploDB
         - Desativar backups automáticos

4. Criação de parâmetros usados pela aplicação PHP para se conectar ao banco de dados:
   - AWS System Manager
     - Store de Parâmetros
       - Criar Parâmetro
         - /exemplo/database: ExemploDB
         - /exemplo/username: admin
         - /exemplo/password: password
         - /exemplo/endpoint: exampledb.ctccnqipe9y5.us-east-1.rds.amazonaws.com

5. Configuração do BD na Instância
   - Tentei por uma meia hora acessar a instância Bastion por SSH através da IDE da AWS, mas não conectava
   - Conectei-me pelo MobbaXterm utilizando a SSH Key: labuser.ppk & User: ec2-user
     ```bash
     $ sudo yum update -y
     $ vi labuser.pem
     ```
     - Cole a Chave Privada RSA (Encontrada no Cloud Access do Projeto Capstone)
     ```bash
     :wq labuser.pem
     $ chmod 400 labuser.pem
     ```
   - Habilite a conexão SSH da instância ExampleApp (Endpoint: 10.0.0.143)
     - Ec2 -> Security Groups -> Inventory-App -> Edit Inbound Rules -> Add: SSH Conetion (22) via Custom: Bastion
     ```bash
     $ ssh -i labuser.pem ec2-user@10.0.0.143
     $ yes
     ```
     ```bash
     $ ll
     ```
     - Para verificar a existência do dump.sql

     ```bash
     $ mysql -u admin -p --host exampledb.ctccnqipe9y5.us-east-1.rds.amazonaws.com ExampleDB < Countrydatadump.sql
     $ password
     ```
     - Estes parâmetros diferenciam maiúsculas de minúsculas.

     ```bash
     $ mysql -u admin -p --host exampledb.ctccnqipe9y5.us-east-1.rds.amazonaws.com
     $ password
     ```
     ```mysql
     MySQL [(none)]>
     MySQL [(none)]> show databases;
     MySQL [(none)]> use ExampleDB;
     MySQL [ExampleDB]>
     MySQL [ExampleDB]> show tables;
     MySQL [ExampleDB]> select * from countrydata_final;
     ```
     - Se houver retorno das tabelas, o DB foi populado com sucesso pelo dump

     ```mysql
     MySQL [ExampleDB]> exit;
     ```

6. Teste da Aplicação
   - Acesse o endpoint da aplicação: http://capstoneelb-1473392172.us-east-1.elb.amazonaws.com/query.php
   - Navegue para a página de queries
   - Query Ndata
     - Se houver retorno, o serviço foi criado conforme esperado pela arquitetura proposta

**********

### Developed by

**Rafael Rodrigues Pereira**

[<img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/>](mailto:rafael.informa@gmail.com?subject=Inquiry:%20ACA%20Capstone%20Project) [<img src="https://img.shields.io/badge/linkedin-%230077B5.svg?&style=for-the-badge&logo=linkedin&logoColor=white"/>](https://www.linkedin.com/in/rafaelrodriguespereira/)

**********

If you have any questions, suggestions, or would like to contribute, feel free to reach out!

Thank you for checking out my project! 😉
