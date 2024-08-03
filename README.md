# Projeto de Inserção de Dados Contábeis no PostgreSQL

Este projeto é uma aplicação prática do uso de Python e SQLAlchemy para carregar dados contábeis de um arquivo Excel para um banco de dados PostgreSQL. Utilizamos a metodologia CRISP-DM para estruturar a análise dos dados.

## Artigo no Medium

Confira o artigo completo no Medium: [Análise Financeira do Esporte Clube Bahia: aplicando a Metodologia CRISP-DM]([https://medium.com/sua-url-do-artigo](https://medium.com/@reisrodri/an%C3%A1lise-financeira-do-esporte-clube-bahia-aplicando-a-metodologia-crisp-dm-com-python-postgresql-3275be6e375d))


## Estrutura do Projeto

O projeto consiste em 6 etapas principais:

1. **Business Understanding**: Compreensão dos requisitos e objetivos do negócio.
2. **Data Understanding**: Coleta e exploração inicial dos dados.
3. **Data Preparation**: Preparação e limpeza dos dados.
4. **Modeling**: Criação das tabelas no banco de dados.
5. **Evaluation**: Verificação dos dados inseridos.
6. **Deployment**: Implementação e documentação do projeto.

## Tecnologias Utilizadas

- Python
- Pandas
- SQLAlchemy
- PostgreSQL

## Como Executar

### Pré-requisitos

- Python 3.8 ou superior
- PostgreSQL instalado e em execução
- Biblioteca SQLAlchemy instalada (`pip install SQLAlchemy`)
- Biblioteca Pandas instalada (`pip install pandas`)

### Configuração

1. Clone o repositório para sua máquina local.
2. Defina a string de conexão para seu banco de dados PostgreSQL.
3. Ajuste o caminho do arquivo Excel para o local correto no seu sistema.

### Executando o Código

```python
import numpy as np
import pandas as pd
from sqlalchemy import create_engine, text, Table, Column, String, Float, Date, MetaData

# Caminho do arquivo Excel
excel_file = r'C:\Users\rodri\Downloads\DRE_PowerBI.xlsx'

# Carregar e processar dados da Tabela Razão Contábil
df = pd.read_excel(excel_file, sheet_name='Fato.RazaoContabil')
df.drop(columns=['Lote', 'Doc.', 'SEQ', 'Histórico'], inplace=True)
df.rename(columns={'CR': 'Centro_Resultado', 'Descrição CR': 'Descricao_Centro_Resultado', 'Conta': 'Conta', 'Descrição Conta': 'Descricao_Conta', 'Data': 'Data', 'Débito': 'Debito', 'Crédito': 'Credito', 'Saldo': 'Saldo'}, inplace=True)

# Configuração da conexão com o banco de dados
postgres_str = 'postgresql://postgres:123456789@localhost:5432/esquadrao'
engine = create_engine(postgres_str)

# Criação do schema dre_ecbahia
with engine.connect() as connection:
    connection.execute(text("CREATE SCHEMA IF NOT EXISTS dre_ecbahia;"))

metadata = MetaData(schema='dre_ecbahia')

# Definição da tabela f_razao_contabil
f_razao_contabil = Table('f_razao_contabil', metadata,
                         Column('Centro_Resultado', String),
                         Column('Descricao_Centro_Resultado', String),
                         Column('Descricao_Conta', String),
                         Column('Conta', String),
                         Column('Debito', Float),
                         Column('Credito', Float),
                         Column('Data', Date),
                         Column('Saldo', Float)
                         )

# Criação da tabela no banco de dados
metadata.create_all(engine)

# Inserção dos dados na tabela f_razao_contabil
df.to_sql('f_razao_contabil', engine, schema='dre_ecbahia', if_exists='append', index=False)

# Verificação dos dados inseridos
with engine.connect() as connection:
    result = connection.execute(text("SELECT * FROM dre_ecbahia.f_razao_contabil LIMIT 5;"))
    for row in result:
        print(row)

# Repita os mesmos passos para as outras tabelas (d.PlanoContas, d.MapaDRE, d.DRE (Resumida), d.Balancete) 

## Estrutura dos Dados

O arquivo Excel contém várias abas, cada uma representando diferentes tabelas:

- **Fato.RazaoContabil**: Contém dados de razão contábil.
- **d.PlanoContas**: Contém o plano de contas.
- **d.MapaDRE**: Contém o mapa do DRE.
- **d.DRE (Resumida)**: Contém uma versão resumida do DRE.
- **d.Balancete**: Contém o balancete.

Cada aba é carregada, processada e inserida no banco de dados PostgreSQL.

## Contato
Para dúvidas ou sugestões, entre em contato pelo LinkedIne-mail: https://www.linkedin.com/in/rodrigo-bessa/
