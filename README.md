# Projeto de Engenharia de Dados — Camada Bronze com SQLite

Este projeto tem como objetivo a construção de um pipeline de dados
utilizando Python e SQLite, seguindo o conceito de camadas
Bronze, Silver e Gold.

A camada Bronze é responsável pela ingestão e persistência dos dados
em seu formato bruto, sem aplicação de regras de negócio, garantindo
rastreabilidade, auditoria e reprocessamento.

## 🥉 Camada Bronze

A camada Bronze armazena os dados em seu formato bruto, conforme
recebidos da fonte, sem transformações ou validações de negócio.

Características:
- Dados brutos
- Carga incremental
- Sem deduplicação
- Persistência em SQLite

## 🥉 Camada Bronze: Modelagem de Dados

A tabela abaixo representa o dicionário de dados da camada Bronze,
descrevendo as colunas, tipos e justificativas de modelagem.
As decisões de tipagem seguem o princípio de flexibilidade da camada
Bronze, priorizando rastreabilidade e reprocessamento.

| Coluna               | Tipo | Por quê |
|---------------------|------|--------|
| id_produto           | TEXT | SQLite trata VARCHAR como TEXT |
| nome_produto         | TEXT | Texto livre |
| categoria            | TEXT | Classificação |
| tipo_dado            | TEXT | Sem enum no Bronze |
| resolucao_espacial   | REAL | Valor numérico com decimal |
| sistema_coordenadas  | TEXT | EPSG é texto |
| area_cobertura_km2   | REAL | Permite soma, média |
| data_aquisicao       | TEXT | ISO 8601 (padrão em dados) |
| formato              | TEXT | String |
| fornecedor           | TEXT | String |
| preco                | REAL | Cálculos futuros |
| data_bronze          | TEXT | Auditoria / linhagem |

### 🥈 Camada Silver (Curated / Cleansed / Refined)

Responsável por **limpeza, padronização e enriquecimento dos dados**, garantindo qualidade e consistência antes do consumo analítico. A camada Silver são oriundos da camada Bronze.

**Principais atividades:**

- Tratamento de nulos e duplicidades  
- Validação e correção de tipos de dados  
- Padronização de formatos e nomenclaturas  
- Inclusão de coluna de auditoria (data_silver)

**Resultado:**  
Dados estruturados, confiáveis e prontos para modelagem e evolução para a camada Gold.
Ao final da execução, os dados são persistidos na tabela silver_produtos no SQLite.



## 🛠️ Tecnologias Utilizadas

- Python 
- SQLite3
- Pandas
- Git / GitHub
- Jupyter Notebook
- VS Code

## 📁 Estrutura do Projeto

project/
├── landing/
│   └── dict_bronze_prod.csv
|   └── z0019_1.csv
|   └── z0019_2.csv
├── notebooks/
│   └── bronze_ingestao.ipynb
├── └── db_project_eng_dados.db
├── README.md


## ▶️ Como Executar

1. Clone o repositório
2. Crie o ambiente virtual
3. Instale as dependências
4. Execute o script de ingestão Bronze





