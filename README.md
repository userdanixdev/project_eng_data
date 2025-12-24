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

### 🥇 Camada Gold – Modelo Dimensional (Star Schema)

A **Camada Gold** adota o **Modelo Dimensional no padrão Star Schema**, no qual uma **tabela fato central** se relaciona com **tabelas dimensão desnormalizadas**, otimizando consultas analíticas e consumo por ferramentas de BI.

#### Visão Geral:

- Dados originados da **Camada Silver** (curados e validados)
- Aplicação adequadas para regras de negócio
- Estrutura orientada a análise
- Alto desempenho para consultas agregadas

---

#### ⭐ Star Schema:

No Star Schema:

- **Tabelas Dimensão** armazenam atributos descritivos do negócio
- **Tabela Fato** armazena métricas e eventos mensuráveis
- Relacionamentos via **chaves substitutas (surrogate keys)**
- Redução da complexidade de joins

---

#### 📐 Tabelas Dimensão

**dim_produto**  
Contém os atributos descritivos dos produtos geoespaciais, derivados da Camada Silver.

Principais características:

- Chave substituta (`sk_produto`)
- Atributos desnormalizados
- Baixa volatilidade
- Utilizada para filtragem e agrupamento nas análises

**dim_tempo**  
Representa o eixo temporal das análises.

Principais características:
- Chave substituta (`sk_tempo`)
- Derivação de atributos de data (ano, mês, dia)
- Padronização temporal para análises históricas

---

#### 📊 Tabela Fato

**fato_produto**

Armazena as métricas associadas aos produtos geoespaciais, mantendo a granularidade definida na Camada Silver.

Principais características:

- Métricas numéricas (preço, área de cobertura, resolução espacial)
- Referências às dimensões por chaves substitutas
- Granularidade clara e consistente
- Otimizada para agregações e métricas de negócio

---

#### 🎯 Benefícios do Star Schema na Camada Gold:

- Consultas SQL mais simples e performáticas
- Melhor compatibilidade com ferramentas de BI
- Clareza semântica para analistas e cientistas de dados
- Separação clara entre contexto (dimensões) e métricas (fato)

#### 🔄 Slowly Changing Dimensions (SCD)

As **Slowly Changing Dimensions (SCD)** tratam da forma como alterações nos atributos das dimensões são gerenciadas ao longo do tempo, preservando (ou não) o histórico das mudanças.

Neste projeto, a Camada Gold adota uma abordagem **controlada e explícita de SCD**, alinhada às necessidades analíticas e à simplicidade operacional.

---

##### Tipos de SCD considerados:

**SCD Tipo 1 – Sobrescrita**

- O valor antigo é substituído pelo novo
- Não há preservação de histórico
- Aplicado a atributos que não exigem rastreabilidade histórica

**Exemplos de uso:**

- Correção de nome do produto
- Ajustes ortográficos
- Padronização de valores

---

**SCD Tipo 2 – Preservação de Histórico**

- Cada mudança gera um novo registro na dimensão
- Preserva o histórico completo das alterações
- Permite análises históricas corretas

**Atributos de controle adicionais:**

- `data_inicio_vigencia`
- `data_fim_vigencia`
- `registro_ativo`

---

##### Estratégia adotada no projeto

A dimensão **dim_produto** é tratada como:

- **SCD Tipo 1** para atributos descritivos estáveis  
- **Evolutível para SCD Tipo 2** caso haja necessidade de análise histórica

Essa abordagem equilibra:

- Simplicidade do modelo
- Baixo custo operacional
- Possibilidade de evolução futura

---

##### Impacto na Tabela Fato

- A tabela **fato_produto** referencia sempre a **versão vigente** da dimensão
- Em um cenário SCD Tipo 2, a Fato passa a referenciar a dimensão válida no momento do evento
- Garante consistência histórica nas análises

---

##### Boas práticas adotadas:

- Definição explícita da estratégia SCD na documentação
- Separação clara entre atributos históricos e não históricos
- Preparação do modelo para evolução sem quebra de schema



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
│   └── curated_silver.ipynb
│   └── gold_layer.ipynb
|   └── db_project_eng_dados.db
|──.gitignore
|── file_1.txt
├── README.md


## ▶️ Como Executar

1. Clone o repositório
2. Crie o ambiente virtual
3. Instale as dependências
4. Execute os scripts 





