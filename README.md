### MPV Puc Rio - Engenharia de dados

### Nome: Jenifer Estefani Volles


## OBJETIVO

Desenvolver um pipeline de Engenharia de Dados para tratamento, organização e análise de dados do transporte aéreo. O projeto busca transformar dados brutos em informações estruturadas que permitam responder a questões relacionadas à movimentação de passageiros, rotas, empresas aéreas e características das operações ao longo do período analisado, utilizando técnicas de tratamento, modelagem dimensional e consultas SQL.

### Perguntas do projeto
1. Como a quantidade de passageiros transportados evoluiu ao longo dos anos?
2. Quais foram as rotas com maior movimentação de passageiros ao longo do período analisado?
3. Quais empresas aéreas apresentaram a maior média de passageiros por decolagem?
4. Quais rotas apresentaram as maiores distâncias voadas?
5. Quais foram os registros de voo com maior quantidade de passageiros transportados?


## BUSCA DOS DADOS

Os dados utilizados neste projeto foram obtidos no portal de **Dados Abertos da Agência Nacional de Aviação Civil (ANAC)**, na seção de Dados Estatísticos do Transporte Aéreo. A fonte disponibiliza os dados em formato aberto, incluindo o arquivo `Dados_Estatisticos.csv`.

Site: https://sistemas.anac.gov.br/dadosabertos/Voos%20e%20opera%C3%A7%C3%B5es%20a%C3%A9reas/Dados%20Estat%C3%ADsticos%20do%20Transporte%20A%C3%A9reo/

<img width="693" height="222" alt="image" src="https://github.com/user-attachments/assets/2812da7b-dc88-4a94-877f-f741c93d10d3" />

*Imagem para ilustrar qual o arquivo .csv utilizado para este projeto.*

A coleta dos dados utilizados neste projeto foi realizada em **13/09/2026**. Como a fonte é atualizada diariamente, a data da coleta foi registrada para garantir a rastreabilidade e identificar a versão dos dados utilizada no desenvolvimento do projeto.

Para fins de governança e rastreabilidade, foram mantidas as seguintes informações sobre a origem dos dados: **fonte (ANAC), endereço da fonte, arquivo utilizado e data da coleta**. O arquivo original foi utilizado como base para a camada **Bronze**, preservando os dados recebidos da fonte antes da aplicação dos tratamentos realizados nas etapas seguintes do pipeline.

Os dados estatísticos do transporte aéreo do Brasil encontram-se regulamentados pela Resolução ANAC no 191/2011 e pelas Portarias ANAC no 1.189 e 1.190/SRE/2011. De acordo com a mencionada regulamentação, os dados são mensalmente fornecidos à ANAC, até o dia 10 do mês subsequente ao de referência, pelas empresas brasileiras e estrangeiras que exploram os serviços de transporte aéreo público regular e não regular no Brasil.


## COLETA: TRAZENDO OS DADOS PARA NUVEM

Os dados do CSV Dados Estatísticos do Transporte Aéreo da ANAC passam por duas etapas de armazenamento no Databricks:

1. Arquivo CSV bruto: O arquivo .csv foi salvo no Volume do Unity Catalog em: /Volumes/workspace/default/dados_anac
Volumes são armazenamentos de arquivos gerenciados pelo Unity Catalog.

2. Tabela Bronze: o arquivo .csv original reside no Volume /Volumes/workspace/default/dados_anac e os dados foram materializados como uma tabela Delta em workspace.default.dados_anac_bronze — ambos no catálogo workspace, schema default, com armazenamento físico no S3 da AWS.

<img width="359" height="632" alt="image" src="https://github.com/user-attachments/assets/4cfba32f-d15f-458b-a5ab-8139a9246fde" />

## ORGANIZAÇÃO DOS NOTEBOOKS

O pipeline de dados foi organizado em notebooks separados de acordo com as etapas da arquitetura de dados, facilitando a organização, manutenção e execução de cada camada. Foram utilizados quatro notebooks:

* **`Notebook MVP - Jenifer Volles - Bronze.ipynb`** → responsável pela **carga e criação da camada Bronze**, mantendo os dados em seu formato bruto, conforme disponibilizados pela ANAC.

* **`Notebook MVP - Jenifer Volles - Silver.ipynb`** → responsável pelo **tratamento e preparação dos dados da camada Silver**, incluindo remoção de colunas, eliminação de duplicidades e validações de qualidade.

* **`Notebook MVP - Jenifer Volles - Gold.ipynb`** → responsável pela **construção da camada Gold**, incluindo as tabelas dimensão, a tabela fato e a criação das Views utilizadas para consulta dos dados.

* **`Notebook MVP - Jenifer Volles - Análise dos dados.ipynb`** → responsável pelas **análises dos dados e consultas utilizadas para responder às perguntas do projeto**.


## IMPLEMENTAÇÃO E ARQUITETURA DO PIPELINE DE DADOS

Nesta etapa foi desenvolvido o pipeline de dados para tratamento, organização e análise dos dados estatísticos do transporte aéreo disponibilizados pela ANAC. O processo foi desenvolvido utilizando **SQL** e estruturado segundo a arquitetura de camadas **Bronze, Silver e Gold**.

Nas camadas **Bronze e Silver**, os dados foram mantidos em estruturas tabulares, sendo que a Bronze preserva os dados conforme recebidos da fonte e a Silver concentra as etapas de tratamento, limpeza e validação da qualidade dos dados.

Na camada **Gold**, os dados foram organizados utilizando um **modelo dimensional do tipo Star Schema**, composto por uma tabela fato central e quatro dimensões: empresa, aeroporto, natureza e grupo de voo. Essa estrutura permite separar as informações descritivas das métricas utilizadas nas análises, facilitando a realização das consultas e a resposta às perguntas de negócio definidas no projeto.

Além da modelagem, foi realizado o **catálogo de dados**, com a documentação dos campos e seus respectivos significados, utilizando tanto a documentação em Markdown quanto o **Unity Catalog**. Por fim, foi criada uma View que integra a tabela fato às dimensões, disponibilizando uma estrutura única para consulta e análise dos dados.

### Camada Bronze

Após importação dos dados brutos /Volumes/workspace/default/dados_anac, foi realizado a query de consulta abaixo para análise:

<img width="1128" height="691" alt="image" src="https://github.com/user-attachments/assets/d7087619-c38e-41d7-8a82-b4e1793a0163" />


Após identificado como os dados foram importados, foi realizado a query de criação da Tabela da camada Bronze que armazena os dados estatísticos do transporte aéreo da ANAC em formato bruto, conforme arquivo CSV original.
Nenhuma transformação ou limpeza é aplicada nesta camada, os dados são fiéis ao arquivo de origem.

<img width="1138" height="322" alt="image" src="https://github.com/user-attachments/assets/bb25dbe3-d171-4e3d-9783-6ea0d54e54e9" />


Consulta realizada para retornar a quantidade de dados da tabela Bronze:

<img width="564" height="253" alt="image" src="https://github.com/user-attachments/assets/5023c9fa-b3bb-4541-9f01-a29d4ceb0afc" />


Consulta para visualizar 10 registros dos dados brutos referentes ao ano de 2026:

<img width="1110" height="554" alt="image" src="https://github.com/user-attachments/assets/1862ed42-40b5-431c-874f-b560ad1a5e68" />


Consulta ao catálogo de dados da tabela Bronze (dados_anac_bronze) via Unity Catalog (information_schema):

<img width="717" height="681" alt="image" src="https://github.com/user-attachments/assets/d23dc6f6-a1b4-4729-8f5e-fd0607d309b2" />


**Catálogo com detalhamento (Descrição) do significado de cada coluna:**

*Os dados da **Descrição** foram realizados conforme a documentação dos metadados da ANAC: https://www.anac.gov.br/acesso-a-informacao/dados-abertos/areas-de-atuacao/voos-e-operacoes-aereas/dados-estatisticos-do-transporte-aereo/48-dados-estatisticos-do-transporte-aereo*

**Dicionário de Colunas (38 colunas)**

| Coluna | Tipo | Descrição |
| --- | --- | --- |
| `EMPRESA_SIGLA` | string | Sigla da empresa aérea responsável por operar as etapas. |
| `EMPRESA_NOME` | string | Nome da empresa aérea responsável por operar as etapas. |
| `EMPRESA_NACIONALIDADE` | string | Nacionalidade da empresa aérea. Valores: `BRASILEIRA` ou `ESTRANGEIRA`. |
| `ANO` | int | Ano de referência. Corresponde ao ano da data prevista para o início da primeira etapa de cada voo. |
| `MES` | int | Mês de referência. Corresponde ao mês da data prevista para o início da primeira etapa de cada voo. |
| `AEROPORTO_DE_ORIGEM_SIGLA` | string | Sigla do aeródromo de origem da etapa de voo. |
| `AEROPORTO_DE_ORIGEM_NOME` | string | Nome do aeródromo de origem da etapa de voo. |
| `AEROPORTO_DE_ORIGEM_UF` | string | Unidade Federativa do aeródromo de origem. |
| `AEROPORTO_DE_ORIGEM_REGIAO` | string | Região geográfica do aeródromo de origem. |
| `AEROPORTO_DE_ORIGEM_PAIS` | string | País onde está localizado o aeródromo de origem. |
| `AEROPORTO_DE_ORIGEM_CONTINENTE` | string | Continente onde está localizado o aeródromo de origem. |
| `AEROPORTO_DE_DESTINO_SIGLA` | string | Sigla do aeródromo de destino da etapa de voo. |
| `AEROPORTO_DE_DESTINO_NOME` | string | Nome do aeródromo de destino da etapa de voo. |
| `AEROPORTO_DE_DESTINO_UF` | string | Unidade Federativa do aeródromo de destino. |
| `AEROPORTO_DE_DESTINO_REGIAO` | string | Região geográfica do aeródromo de destino. |
| `AEROPORTO_DE_DESTINO_PAIS` | string | País onde está localizado o aeródromo de destino. |
| `AEROPORTO_DE_DESTINO_CONTINENTE` | string | Continente onde está localizado o aeródromo de destino. |
| `NATUREZA` | string | Natureza da etapa de voo. Pode ser `DOMÉSTICA` (quando pouso e decolagem são realizados no Brasil e a operação é de empresa brasileira) ou `INTERNACIONAL` (caso contrário). Nota: a ANAC documenta como `DOMÉSTICO`, mas os dados do CSV contêm `DOMÉSTICA`. |
| `GRUPO_DE_VOO` | string | Classificação do tipo de operação da etapa de voo. Valores: `REGULAR`, `NÃO REGULAR`, `IMPRODUTIVO` ou `NÃO IDENTIFICADO`. |
| `PASSAGEIROS_PAGOS` | int | Quantidade de passageiros que ocupam assentos comercializados ao público e geram receita para a empresa aérea. |
| `PASSAGEIROS_GRATIS` | int | Quantidade de passageiros que ocupam assentos comercializados ao público, mas não geram receita para a empresa aérea. |
| `CARGA_PAGA_KG` | int | Quantidade, em quilogramas, de bens transportados que geraram receita para a empresa aérea, excluindo correio e bagagem. |
| `CARGA_GRATIS_KG` | int | Quantidade, em quilogramas, de bens transportados que não geraram receita para a empresa aérea, excluindo correio e bagagem. |
| `CORREIO_KG` | int | Quantidade, em quilogramas, de objetos transportados da rede postal em cada trecho de voo. |
| `ASK` | int | Available Seat Kilometers. Oferta de transporte de passageiros medida em assentos-quilômetro. |
| `RPK` | int | Revenue Passenger Kilometers. Demanda de passageiros medida em passageiros-quilômetro. |
| `ATK` | int | Available Tonne Kilometers. Volume de tonelada-quilômetro oferecida, calculado a partir do payload e da distância da etapa. |
| `RTK` | int | Revenue Tonne Kilometers. Volume de toneladas-quilômetro transportadas, considerando a carga paga e o peso estimado de 75 kg por passageiro. |
| `COMBUSTIVEL_LITROS` | int | Quantidade, em litros, de combustível consumida pela aeronave na execução da etapa. Informação disponível apenas para empresas brasileiras. |
| `DISTANCIA_VOADA_KM` | int | Distância, em quilômetros, entre os aeródromos de origem e destino da etapa, considerando a curvatura da Terra. |
| `DECOLAGENS` | int | Quantidade de decolagens realizadas na etapa de voo entre o aeródromo de origem e o aeródromo de destino durante o período de referência da linha. |
| `CARGA_PAGA_KM` | bigint | Volume de carga paga, em kg, multiplicado pela distância da etapa. |
| `CARGA_GRATIS_KM` | int | Volume de carga grátis, em kg, multiplicado pela distância da etapa. |
| `CORREIO_KM` | bigint | Volume de correio, em kg, multiplicado pela distância da etapa. |
| `ASSENTOS` | int | Número de assentos disponíveis em cada etapa de voo, de acordo com a configuração da aeronave. |
| `PAYLOAD` | int | Capacidade total de peso da aeronave, em quilogramas, disponível para o transporte de passageiros, carga e correio. |
| `HORAS_VOADAS` | string | Quantidade de horas de voo entre os aeródromos de origem e destino da etapa. Nota: a ANAC documenta como `DOUBLE`, mas no CSV o valor usa vírgula decimal (ex: `291,084`), fazendo com que `inferSchema` o classifique como `string`. |
| `BAGAGEM_KG` | int | Quantidade total de bagagem despachada, expressa em quilogramas. |


### Camada Silver

A camada Silver recebe os dados da tabela Bronze (`workspace.default.dados_anac_bronze`) e aplica as seguintes transformações:

1. **Remoção de colunas desnecessárias** — 23 colunas foram removidas por não serem relevantes para a análise, conforme as perguntas de negócio
2. **Remoção de linhas duplicadas** — `SELECT DISTINCT` eliminou 380 linhas duplicadas
3. **Preservação de valores nulos (NULL)** — os NULLs foram mantidos conforme decisão de negócio e detalhamento mais abaixo
4. **Verificação de tipos de dados** — todas as colunas numéricas já estavam com o tipo correto (`int`)
5. **Espaços em branco:** verificado que nenhuma coluna de texto possui espaços extras (leading/trailing)
6. **Strings vazias:** nenhuma coluna de texto possui valores vazios

**1. Remoção de Colunas:**

**- 23 Colunas removidas:** `EMPRESA_SIGLA`, `AEROPORTO_DE_ORIGEM_SIGLA`, `AEROPORTO_DE_ORIGEM_UF`, `AEROPORTO_DE_ORIGEM_REGIAO`, `AEROPORTO_DE_ORIGEM_CONTINENTE`, `AEROPORTO_DE_DESTINO_SIGLA`, `AEROPORTO_DE_DESTINO_UF`, `AEROPORTO_DE_DESTINO_REGIAO`, `AEROPORTO_DE_DESTINO_CONTINENTE`, `CARGA_PAGA_KG`, `CARGA_GRATIS_KG`, `CORREIO_KG`, `ASK`, `RPK`, `ATK`, `RTK`, `COMBUSTIVEL_LITROS`, `CARGA_PAGA_KM`, `CARGA_GRATIS_KM`, `CORREIO_KM`, `PAYLOAD`, `HORAS_VOADAS`, `BAGAGEM_KG`

**- 15 Colunas mantidas:** `EMPRESA_NOME`, `EMPRESA_NACIONALIDADE`, `ANO`, `MES`, `AEROPORTO_DE_ORIGEM_NOME`, `AEROPORTO_DE_ORIGEM_PAIS`, `AEROPORTO_DE_DESTINO_NOME`, `AEROPORTO_DE_DESTINO_PAIS`, `NATUREZA`, `GRUPO_DE_VOO`, `PASSAGEIROS_PAGOS`, `PASSAGEIROS_GRATIS`, `DISTANCIA_VOADA_KM`, `ASSENTOS`, `DECOLAGENS`

**2. Remoção de Duplicatas**

**- Total na Bronze:** 1.096.554 linhas
**- Total na Silver:** 1.096.174 linhas
**- Duplicatas removidas:** 380 linhas (via `SELECT DISTINCT`)

As duplicatas foram identificadas comparando-se todas as 15 colunas que permaneceram na Silver. Linhas com valores idênticos em todos os campos foram consolidadas em uma única ocorrência.

<img width="886" height="773" alt="image" src="https://github.com/user-attachments/assets/3c56eaa4-5f5d-4452-82a5-401fa89485f8" />


**3. Tratamento de Valores Nulos**

Os valores nulos foram **mantidos** (não substituídos por zero), conforme decisão de negócio. Isso preserva a integridade dos dados e evita introduzir valores artificiais que poderiam distorcer análises.

Quantidade de NULLs por coluna:

| Coluna | Linhas com NULL | % do total | Internacionais | Domésticos |
| --- | --- | --- | --- | --- |
| `AEROPORTO_DE_ORIGEM_NOME` | 5.212 | 0,48% | 5.212 (100%) | 0 |
| `AEROPORTO_DE_ORIGEM_PAIS` | 5.212 | 0,48% | 5.212 (100%) | 0 |
| `GRUPO_DE_VOO` | 2 | ~0% | 2 (100%) | 0 |
| `EMPRESA_NACIONALIDADE` | 0 | 0% | — | — |
| `PASSAGEIROS_PAGOS` | 40.296 | 3,68% | 6.763 (17%) | 33.533 (83%) |
| `PASSAGEIROS_GRATIS` | 40.296 | 3,68% | 6.763 (17%) | 33.533 (83%) |
| `DISTANCIA_VOADA_KM` | 238.382 | 21,75% | 75.288 (32%) | 163.094 (68%) |
| `ASSENTOS` | 237.803 | 21,69% | 75.173 (32%) | 162.630 (68%) |
| `DECOLAGENS` | 237.802 | 21,69% | 75.172 (32%) | 162.630 (68%) |

Perfil das linhas com origem NULL (5.212 linhas):

- 100% são voos `INTERNACIONAL`
- 87,9% são de empresas estrangeiras (4.579 de 5.212 — American Airlines, Air France, etc.)
- O aeroporto de destino está 100% preenchido
- `PASSAGEIROS_PAGOS` está 100% preenchido
- `DISTANCIA_VOADA_KM`, `ASSENTOS` e `DECOLAGENS` são NULL em 100% dessas linhas (a ANAC não possui esses dados para voos partindo do exterior)

Perfil dos outros NULLs (passageiros, distância e assentos):

Ao contrário dos NULLs de origem (exclusivamente internacionais), os NULLs de passageiros, distância e assentos são **majoritariamente domésticos**:

| Coluna NULL | Total | Internacionais | Domésticos | Predominância |
| --- | --- | --- | --- | --- |
| `PASSAGEIROS_PAGOS` | 40.296 | 6.763 (17%) | 33.533 (83%) | Doméstica |
| `PASSAGEIROS_GRATIS` | 40.296 | 6.763 (17%) | 33.533 (83%) | Doméstica |
| `DISTANCIA_VOADA_KM` | 238.382 | 75.288 (32%) | 163.094 (68%) | Doméstica |
| `ASSENTOS` | 237.803 | 75.173 (32%) | 162.630 (68%) | Doméstica |
| `DECOLAGENS` | 237.802 | 75.172 (32%) | 162.630 (68%) | Doméstica |

Esses NULLs ocorrem principalmente em voos domésticos improdutivos ou não regulares (táxi aéreo, voos sem passageiros registrados), onde a ANAC não registra passageiros, distância, assentos nem decolagens. A preservação desses NULLs (em vez de substituir por zero) evita mascarar a distinção entre "sem dado registrado" e "valor zero".

<img width="875" height="759" alt="image" src="https://github.com/user-attachments/assets/271fd420-dcd2-42f5-aff1-491d1286b3de" />


<img width="874" height="574" alt="image" src="https://github.com/user-attachments/assets/5833111d-ba3f-4c26-9eba-806243189f7d" />


**4. Tipos de Dados**

Todas as colunas numéricas restantes já estavam com o tipo correto (`int`), portanto nenhuma conversão foi necessária:

| Coluna | Tipo |
| --- | --- |
| `EMPRESA_NOME` | string |
| `EMPRESA_NACIONALIDADE` | string |
| `ANO` | int |
| `MES` | int |
| `AEROPORTO_DE_ORIGEM_NOME` | string |
| `AEROPORTO_DE_ORIGEM_PAIS` | string |
| `AEROPORTO_DE_DESTINO_NOME` | string |
| `AEROPORTO_DE_DESTINO_PAIS` | string |
| `NATUREZA` | string |
| `GRUPO_DE_VOO` | string |
| `PASSAGEIROS_PAGOS` | int |
| `PASSAGEIROS_GRATIS` | int |
| `DISTANCIA_VOADA_KM` | int |
| `ASSENTOS` | int |
| `DECOLAGENS` | int |

**5. Espaços em branco:**

<img width="872" height="732" alt="image" src="https://github.com/user-attachments/assets/8c33e462-0525-4da5-9f75-2ed8a7b9b339" />


**6. Strings vazias:**

<img width="883" height="504" alt="image" src="https://github.com/user-attachments/assets/b4b64358-7adf-417f-9d5e-bc669d204f11" />

Query de criação da tabela Silver:

<img width="882" height="673" alt="image" src="https://github.com/user-attachments/assets/884a0c12-3306-480e-98ed-12e3063926b3" />

Consulta realizada para retornar a quantidade de dados da tabela Silver:

<img width="885" height="441" alt="image" src="https://github.com/user-attachments/assets/67406baf-a428-454b-ad98-475334f3aa9c" />

Consulta realizada para visualizar 10 registros dos dados brutos referentes ao ano de 2026:

<img width="872" height="497" alt="image" src="https://github.com/user-attachments/assets/f81bf491-8f01-4f64-98b3-06d4726d7aa1" />

Retorno da query da imagem acima:

| EMPRESA_NOME | EMPRESA_NACIONALIDADE | ANO | MES | AEROPORTO_DE_ORIGEM_NOME | AEROPORTO_DE_ORIGEM_PAIS | AEROPORTO_DE_DESTINO_NOME | AEROPORTO_DE_DESTINO_PAIS | NATUREZA | GRUPO_DE_VOO | PASSAGEIROS_PAGOS | PASSAGEIROS_GRATIS | DISTANCIA_VOADA_KM | ASSENTOS | DECOLAGENS |
| --- | --- | ---: | ---: | --- | --- | --- | --- | --- | --- | ---: | ---: | ---: | ---: | ---: |
| SERVICIOS AÉREOS PANAMERICANOS LTDA. SARPA S.A.S | ESTRANGEIRA | 2026 | 2 | GEORGETOWN | GUIANA | MANAUS | BRASIL | INTERNACIONAL | NÃO REGULAR | 26 | 0 | 1079 | 50 | 1 |
| AMERICAN AIRLINES, INC. | ESTRANGEIRA | 2026 | 1 | GUARULHOS | BRASIL | NEW YORK, NEW YORK | ESTADOS UNIDOS DA AMÉRICA | INTERNACIONAL | NÃO REGULAR | 91 | 2 | 7664 | 234 | 1 |
| AMERICAN AIRLINES, INC. | ESTRANGEIRA | 2026 | 7 | MIAMI, FLORIDA | ESTADOS UNIDOS DA AMÉRICA | GUARULHOS | BRASIL | INTERNACIONAL | NÃO REGULAR | 437 | 30 | 13148 | 544 | 2 |
| ATA - AEROTÁXI ABAETÉ LTDA. | BRASILEIRA | 2026 | 4 | SALVADOR | BRASIL | CAIRU | BRASIL | DOMÉSTICA | REGULAR | 56 | 0 | 594 | 54 | 6 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 1 | CONFINS | BRASIL | TEÓFILO OTONI | BRASIL | DOMÉSTICA | REGULAR | 47 | 2 | 2907 | 81 | 9 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 1 | CAMPINAS | BRASIL | FRANCA | BRASIL | DOMÉSTICA | REGULAR | 26 | 3 | 2700 | 90 | 10 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 1 | PORTO VELHO | BRASIL | MANAUS | BRASIL | DOMÉSTICA | REGULAR | 43 | 1 | 6088 | 72 | 8 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 1 | LINHARES | BRASIL | CONFINS | BRASIL | DOMÉSTICA | REGULAR | 71 | 1 | 4510 | 99 | 11 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 2 | VARGINHA | BRASIL | CONFINS | BRASIL | DOMÉSTICA | REGULAR | 59 | 2 | 2959 | 99 | 11 |
| AZUL CONECTA LTDA. (EX TWO TAXI AEREO LTDA) | BRASILEIRA | 2026 | 3 | CONFINS | BRASIL | RIO DE JANEIRO | BRASIL | DOMÉSTICA | REGULAR | 96 | 7 | 7959 | 189 | 21 |

Realizado a inclusão da descrição de cada campo:

<img width="889" height="594" alt="image" src="https://github.com/user-attachments/assets/02a73511-bda7-49bc-b6bf-3d52a6f060c5" />

### Camada Gold

### Dimensão empresa

A tabela dimensão armazena as empresas aéreas presentes nos dados da ANAC. Cada empresa possui um identificador único (EMPRESA_ID) que foi definido como chave primária (PK) desta tabela.

**Dicionário de Colunas**

| Coluna | Tipo | Descrição | Chave |
| --- | --- | --- | --- |
| `EMPRESA_ID` | int | Identificador único sequencial da empresa aérea, gerado por `ROW_NUMBER()` ordenado por `EMPRESA_NOME` | **Primária** |
| `EMPRESA_NOME` | string | Nome completo da empresa aérea conforme registro da ANAC | — |
| `EMPRESA_NACIONALIDADE` | string | Nacionalidade da empresa aérea. Valores possíveis: `BRASILEIRA` ou `ESTRANGEIRA` | — |

**Regras de Carga**

1. **Origem dos dados:** `workspace.default.dados_anac_silver`
2. **Deduplicação:** `SELECT DISTINCT` garante que cada empresa aparece apenas uma vez
3. **Filtro de qualidade:** linhas com `EMPRESA_NOME IS NULL` são removidas
4. **Geração do ID:** `ROW_NUMBER() OVER (ORDER BY EMPRESA_NOME)` atribui IDs sequenciais em ordem alfabética

<img width="947" height="577" alt="image" src="https://github.com/user-attachments/assets/1f38b7eb-b275-42af-8717-6ae29e4c0673" />

Consulta dos dados na tabela dim_empresa_gold:

<img width="957" height="519" alt="image" src="https://github.com/user-attachments/assets/67e9fa36-6912-4055-87db-c8b8c9c155cc" />


### Dimensão aeroporto

A tabela dimensão aeroporto armazena os aeroportos presentes nos dados da ANAC. Cada aeroporto possui um identificador único (AEROPORTO_ID) que foi definido como chave primaria desta tabela, tanto para os aeroportos de origem quanto para os de destino.
A tabela foi construída unindo os aeroportos de origem e destino da Silver, pois um mesmo aeroporto pode ser origem num voo e destino noutro. Foi verificado que não há inconsistências de país para um mesmo nome de aeroporto entre origem e destino.

**Dicionário de Colunas**

| Coluna | Tipo | Descrição | Chave |
| --- | --- | --- | --- |
| `AEROPORTO_ID` | int | Identificador único sequencial do aeroporto, gerado por `ROW_NUMBER()` ordenado por `AEROPORTO_NOME` | **Primária** |
| `AEROPORTO_NOME` | string | Nome do aeroporto conforme registro da ANAC | — |
| `PAIS` | string | País onde o aeroporto está localizado | — |

**Regras de Carga**

1. **Origem dos dados:** `workspace.default.dados_anac_silver`
2. **União de origem e destino:** `UNION` entre os aeroportos de origem (`AEROPORTO_DE_ORIGEM_NOME` + `AEROPORTO_DE_ORIGEM_PAIS`) e destino (`AEROPORTO_DE_DESTINO_NOME` + `AEROPORTO_DE_DESTINO_PAIS`), eliminando duplicatas automaticamente
3. **Filtro de qualidade:** linhas com `AEROPORTO_NOME IS NULL` são removidas (5.212 linhas com origem NULL foram mantidas na Silver mas não geram registro na dimensão)
4. **Geração do ID:** `ROW_NUMBER() OVER (ORDER BY AEROPORTO_NOME)` atribui IDs sequenciais em ordem alfabética
5. **Validação de consistência:** verificado que nenhum aeroporto possui país diferente entre origem e destino (0 inconsistências)

<img width="949" height="584" alt="image" src="https://github.com/user-attachments/assets/93f4f244-fb93-48fc-a475-ebbe43f732ff" />


Consulta dos dados na tabela dim_aeroporto_gold:

<img width="709" height="548" alt="image" src="https://github.com/user-attachments/assets/4a272880-dc49-4244-9431-f3fd1111bc3f" />


### Dimensão natureza

A tabela dimensão natureza classifica os voos conforme a natureza da operação. Cada tipo de natureza possui um identificador único (NATUREZA_ID) utilizado como chave primária desta tabela.

**Dicionário de Colunas**

| Coluna | Tipo | Descrição | Chave |
| --- | --- | --- | --- |
| `NATUREZA_ID` | int | Identificador único sequencial, gerado por `ROW_NUMBER()` ordenado por `NATUREZA` | **Primária** |
| `NATUREZA` | string | Tipo de natureza do voo. Valores: `DOMÉSTICA` ou `INTERNACIONAL` | — |

**Regras de Carga**

1. **Origem dos dados:** `workspace.default.dados_anac_silver`
2. **Deduplicação:** `SELECT DISTINCT` garante que cada tipo de natureza aparece apenas uma vez
3. **Filtro de qualidade:** linhas com `NATUREZA IS NULL` são removidas
4. **Geração do ID:** `ROW_NUMBER() OVER (ORDER BY NATUREZA)` atribui IDs sequenciais em ordem alfabética

<img width="632" height="347" alt="image" src="https://github.com/user-attachments/assets/34f8136e-177b-4706-8a8f-21deecd14953" />

Consulta dos dados na tabela dim_natureza_gold:

<img width="612" height="270" alt="image" src="https://github.com/user-attachments/assets/83df141e-fa31-4529-83d9-da82586696c2" />


### Dimensão Grupo Voo

A tabela dimensão grupo voo classifica os voos conforme o grupo de operação. Cada tipo de grupo possui um identificador único (`GRUPO_VOO_ID`) utilizado como chave primária desta tabela.

**Dicionário de Colunas**

| Coluna | Tipo | Descrição | Chave |
| --- | --- | --- | --- |
| `GRUPO_VOO_ID` | int | Identificador único sequencial, gerado por `ROW_NUMBER()` ordenado por `GRUPO_DE_VOO` | **Primária** |
| `GRUPO_DE_VOO` | string | Tipo de grupo do voo. Valores: `IMPRODUTIVO`, `NÃO IDENTIFICADO`, `NÃO REGULAR`, `REGULAR` | — |

**Regras de Carga**

1. **Origem dos dados:** `workspace.default.dados_anac_silver`
2. **Deduplicação:** `SELECT DISTINCT` garante que cada tipo de grupo aparece apenas uma vez
3. **Filtro de qualidade:** linhas com `GRUPO_DE_VOO IS NULL` são removidas
4. **Geração do ID:** `ROW_NUMBER() OVER (ORDER BY GRUPO_DE_VOO)` atribui IDs sequenciais em ordem alfabética

<img width="635" height="348" alt="image" src="https://github.com/user-attachments/assets/70368a1d-b494-4dd3-bb61-892bc570502a" />


Consulta dos dados na tabela dim_grupo_voo_gold:

<img width="625" height="374" alt="image" src="https://github.com/user-attachments/assets/8a2c2a27-01f0-43c4-bfda-89a9b077fe45" />

### Fato Voo

A tabela fato armazena os voos registrados pela ANAC. Cada linha representa um voo único, com chaves estrangeiras para as dimensões de empresa, aeroporto (origem e destino), natureza e grupo de voo. As métricas de passageiros, distância, assentos e decolagens são mantidas diretamente na fato.

**Dicionário de Colunas**

| Coluna | Tipo | Descrição | Chave |
| --- | --- | --- | --- |
| `VOO_ID` | int | Identificador único sequencial do voo, gerado por `ROW_NUMBER()` | **Primária** |
| `EMPRESA_ID` | int | FK para `dim_empresa_gold` | **Estrangeira** |
| `AEROPORTO_ORIGEM_ID` | int | FK para `dim_aeroporto_gold` (aeroporto de origem). Pode ser NULL para voos internacionais sem origem registrada | **Estrangeira** |
| `AEROPORTO_DESTINO_ID` | int | FK para `dim_aeroporto_gold` (aeroporto de destino) | **Estrangeira** |
| `NATUREZA_ID` | int | FK para `dim_natureza_gold` | **Estrangeira** |
| `GRUPO_VOO_ID` | int | FK para `dim_grupo_voo_gold`. Pode ser NULL (2 registros sem grupo) | **Estrangeira** |
| `ANO` | int | Ano do voo. Mantido direto na fato para filtros e agrupamentos | — |
| `MES` | int | Mês do voo. Mantido direto na fato para filtros e agrupamentos | — |
| `MES_ANO` | string | Combinação mês/ano no formato `MM/AAAA` (ex: `01/2026`). Útil para exibição em relatórios | — |
| `PASSAGEIROS_PAGOS` | int | Total de passageiros pagos. Pode ser NULL | — |
| `PASSAGEIROS_GRATIS` | int | Total de passageiros grátis. Pode ser NULL | — |
| `DISTANCIA_VOADA_KM` | int | Distância voada em km. Pode ser NULL (21,8% dos registros) | — |
| `ASSENTOS` | int | Total de assentos. Pode ser NULL (21,7% dos registros) | — |
| `DECOLAGENS` | int | Quantidade de decolagens realizadas na etapa de voo | — |

**Regras de Carga**

1. **Origem dos dados:** `workspace.default.dados_anac_silver`
2. **Joins:** `LEFT JOIN` com todas as dimensões para preservar linhas com campos NULL
3. **Preservação de NULLs:** linhas com `AEROPORTO_ORIGEM_ID = NULL` (5.212 voos internacionais sem origem) são mantidas
4. **Geração do ID:** `ROW_NUMBER() OVER (ORDER BY ...)` atribui IDs sequenciais
5. **MES_ANO:** gerado por `CONCAT(LPAD(CAST(MES AS STRING), 2, '0'), '/', CAST(ANO AS STRING))`

<img width="652" height="771" alt="image" src="https://github.com/user-attachments/assets/267218f0-18ef-45e9-b1e1-1ec9a3068ffc" />

Consulta de alguns dados da tabela dim_grupo_voo_gold:

<img width="954" height="494" alt="image" src="https://github.com/user-attachments/assets/27e0726f-092d-4bc4-b795-b6aa101e9975" />

Retorno dessa consulta:

| VOO_ID | EMPRESA_ID | AEROPORTO_ORIGEM_ID | AEROPORTO_DESTINO_ID | NATUREZA_ID | GRUPO_VOO_ID |  ANO | MES | MES_ANO | PASSAGEIROS_PAGOS | PASSAGEIROS_GRATIS | DISTANCIA_VOADA_KM | ASSENTOS | DECOLAGENS |
| -----: | ---------: | ------------------: | -------------------: | ----------: | -----------: | ---: | --: | ------- | ----------------: | -----------------: | -----------------: | -------: | ---------: |
|      1 |          1 |                 556 |                  136 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               null |     null |       null |
|      2 |          1 |                 556 |                  562 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               1701 |        0 |          1 |
|      3 |          1 |                 562 |                  136 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               1787 |        0 |          1 |
|      4 |          1 |                 598 |                  136 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               null |     null |       null |
|      5 |          1 |                 598 |                  556 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               2193 |        0 |          1 |
|      6 |          1 |                 598 |                  562 |           2 |            3 | 2017 |   3 | 03/2017 |                 0 |                  0 |               null |     null |       null |
|      7 |          1 |                 147 |                  587 |           2 |            3 | 2017 |   9 | 09/2017 |                 0 |                  0 |               3895 |        0 |          1 |
|      8 |          1 |                 147 |                  598 |           2 |            3 | 2017 |   9 | 09/2017 |                 0 |                  0 |               null |     null |       null |
|      9 |          1 |                 587 |                  598 |           2 |            3 | 2017 |   9 | 09/2017 |                 0 |                  0 |               2243 |        0 |          1 |
|     10 |          1 |                 598 |                  147 |           2 |            3 | 2017 |   9 | 09/2017 |                 0 |                  0 |               5808 |        0 |          1 |

### View Voos

Foi criada a View vw_voos_completos a partir da tabela fato fato_voos_gold, realizando a integração com as tabelas de dimensões do modelo estrela. A View disponibiliza, em uma única estrutura lógica, as informações descritivas das empresas, aeroportos, natureza e grupo de voo, juntamente com as principais métricas da movimentação aérea, como passageiros, distância, assentos e decolagens. Dessa forma, facilita a consulta e a análise dos dados, evitando a necessidade de realizar os JOINs entre as tabelas de forma manual.

**Dicionário de Colunas**

| Coluna | Tipo | Origem | Descrição |
| --- | --- | --- | --- |
| `VOO_ID` | int | `fato_voos_gold` | Identificador único sequencial do voo |
| `ANO` | int | `fato_voos_gold` | Ano do voo |
| `MES` | int | `fato_voos_gold` | Mês do voo |
| `MES_ANO` | string | `fato_voos_gold` | Combinação mês/ano no formato `MM/AAAA` |
| `EMPRESA_NOME` | string | `dim_empresa_gold` | Nome completo da empresa aérea |
| `EMPRESA_NACIONALIDADE` | string | `dim_empresa_gold` | `BRASILEIRA` ou `ESTRANGEIRA` |
| `AEROPORTO_ORIGEM_NOME` | string | `dim_aeroporto_gold` | Nome do aeroporto de origem. NULL em 5.212 voos internacionais |
| `AEROPORTO_ORIGEM_PAIS` | string | `dim_aeroporto_gold` | País de origem. NULL nos mesmos 5.212 voos |
| `AEROPORTO_DESTINO_NOME` | string | `dim_aeroporto_gold` | Nome do aeroporto de destino |
| `AEROPORTO_DESTINO_PAIS` | string | `dim_aeroporto_gold` | País de destino |
| `NATUREZA` | string | `dim_natureza_gold` | `DOMÉSTICA` ou `INTERNACIONAL` |
| `GRUPO_DE_VOO` | string | `dim_grupo_voo_gold` | `IMPRODUTIVO`, `NÃO IDENTIFICADO`, `NÃO REGULAR` ou `REGULAR`. NULL em 2 registros |
| `PASSAGEIROS_PAGOS` | int | `fato_voos_gold` | Total de passageiros pagos. Pode ser NULL |
| `PASSAGEIROS_GRATIS` | int | `fato_voos_gold` | Total de passageiros grátis. Pode ser NULL |
| `DISTANCIA_VOADA_KM` | int | `fato_voos_gold` | Distância voada em km. Pode ser NULL (21,7% dos registros) |
| `ASSENTOS` | int | `fato_voos_gold` | Total de assentos. Pode ser NULL (21,7% dos registros) |
| `DECOLAGENS` | int | `fato_voos_gold` | Quantidade de decolagens realizadas na etapa de voo. Pode ser NULL (21,7% dos registros) |

**Joins da View**

| Alias | Tabela | Tipo | Chave de Junção |
| --- | --- | --- | --- |
| `f` | `fato_voos_gold` | — | Tabela base |
| `e` | `dim_empresa_gold` | `LEFT JOIN` | `f.EMPRESA_ID = e.EMPRESA_ID` |
| `ao` | `dim_aeroporto_gold` | `LEFT JOIN` | `f.AEROPORTO_ORIGEM_ID = ao.AEROPORTO_ID` |
| `ad` | `dim_aeroporto_gold` | `LEFT JOIN` | `f.AEROPORTO_DESTINO_ID = ad.AEROPORTO_ID` |
| `n` | `dim_natureza_gold` | `LEFT JOIN` | `f.NATUREZA_ID = n.NATUREZA_ID` |
| `g` | `dim_grupo_voo_gold` | `LEFT JOIN` | `f.GRUPO_VOO_ID = g.GRUPO_VOO_ID` |

Esta view é consumida por todas as 5 perguntas do projeto:

1. Evolução de passageiros por ano — `GROUP BY ANO`
2. Top rotas por passageiros — `GROUP BY AEROPORTO_ORIGEM_NOME, AEROPORTO_DESTINO_NOME`
3. Média de passageiros por decolagem — `SUM(DECOLAGENS)` como divisor
4. Rotas com maiores distâncias — `MAX(DISTANCIA_VOADA_KM)`
5. Registros com mais passageiros — `ORDER BY total_passageiros DESC`

Por fim, o projeto final ficou dessa forma:

<img width="480" height="743" alt="image" src="https://github.com/user-attachments/assets/d8695e10-99e0-4c2f-9f47-2aa8bbe8fd66" />

<img width="475" height="883" alt="image" src="https://github.com/user-attachments/assets/25e92456-bc89-4619-bda3-be9e3b3e9a1a" />


## Análise Final dos Dados

Abaixo segue as consultas analíticas que respondem às perguntas do projeto sobre os dados de transporte aéreo da ANAC.

As consultas utilizam a view workspace.default.vw_voos_completos (camada Gold) que já junta a tabela fato com todas as dimensões.

A contagem de voos/decolagens é feita como SUM(DECOLAGENS) da tabela fato, onde cada linha agrega uma ou mais decolagens realizadas na etapa de voo.

**Pergunta 1: Como a quantidade de passageiros transportados evoluiu ao longo dos anos?**

<img width="908" height="721" alt="image" src="https://github.com/user-attachments/assets/3761ffd8-9925-4d06-82fb-e918c9b1574a" />

<img width="971" height="484" alt="image" src="https://github.com/user-attachments/assets/80571ac7-1119-4a30-940b-5be0595a92bb" />

A evolução mostra três fases bem distintas:

Crescimento sustentado (2000–2019): o número de passageiros saiu de cerca de 38,6 milhões em 2000 para 121,6 milhões em 2019, um crescimento de mais de 3x em quase duas décadas. Houve uma pequena queda entre 2002 e 2003, mas o movimento predominante foi de expansão contínua, com destaque para o salto entre 2009 e 2013 (de 71 para 112 milhões), período de forte expansão da aviação doméstica no Brasil.

Colapso da pandemia (2020–2021): em 2020 o total despenca para 52,4 milhões — uma queda de mais de 55% em relação a 2019 — reflexo direto da paralisação do transporte aéreo pela Covid-19. 2021 ainda é um ano de recuperação parcial, com 68,8 milhões.

Recuperação e novo recorde (2022–2025): o setor se recupera rapidamente: 100 milhões em 2022, superando o patamar pré-pandemia já em 2023 (115,4 milhões) e batendo recorde histórico em 2025, com 132,9 milhões de passageiros — o maior valor da série.

A quantidade de 2026 (79 milhões) aparece mais baixo, pois se trata de um ano ainda em andamento.


**Pergunta 2: Quais foram as rotas com maior movimentação de passageiros ao longo do período analisado?**
*Neste caso a consulta foi limitada a 20*

<img width="1338" height="672" alt="image" src="https://github.com/user-attachments/assets/4accf20b-2877-4450-8321-b277dc6d7ad6" />

<img width="919" height="880" alt="image" src="https://github.com/user-attachments/assets/0d0566ed-e302-4d1c-8720-4cfd4062aff7" />

O gráfico deixa claro um padrão dominado por poucos eixos, todos ligados a São Paulo:

A ponte aérea SP-RJ lidera com folga: as duas direções (São Paulo→Rio e Rio→São Paulo) somam cerca de 107,7 milhões de passageiros, mais que o dobro do segundo colocado. É de longe a rota mais movimentada do país, refletindo a intensa integração entre os dois maiores centros econômicos do Brasil.

São Paulo aparece em quase todas as rotas do top 20: seja como origem ou destino (via Congonhas/SP ou Guarulhos/GRU), a cidade está presente em praticamente todas as 20 linhas, conectando-se com Rio de Janeiro, Brasília, Salvador, Recife, Porto Alegre, Curitiba e Belo Horizonte — confirmando seu papel de hub central da malha aérea nacional.

Segundo bloco de rotas relevantes (SP-Brasília): com cerca de 22-23 milhões por direção, essa é a segunda ligação mais forte, ainda assim bem abaixo da ponte aérea SP-RJ.

Cauda mais homogênea: a partir da 5ª posição, as rotas ficam mais próximas entre si, na faixa de 16 a 19,5 milhões de passageiros — todas conectando Guarulhos ou São Paulo a capitais regionais (Salvador, Recife, Porto Alegre, Curitiba, Belo Horizonte), cada uma com volume bidirecional bem equilibrado (ida e volta somam valores muito próximos).

Isso mostra uma malha concentrada em poucos corredores de alta densidade, com São Paulo funcionando como o principal centro de conexão do transporte aéreo brasileiro.


**Pergunta 3:Pergunta 3: Quais empresas aéreas apresentaram a maior média de passageiros por decolagem?**
*Neste caso a consulta foi limitada a 20*

<img width="1129" height="700" alt="image" src="https://github.com/user-attachments/assets/6f0ac30c-a8b3-4dfb-9638-6a89f21e4b16" />

<img width="1100" height="805" alt="image" src="https://github.com/user-attachments/assets/705816f6-48d5-42f5-ae8e-be355c5fa7c9" />

O ranking é dominado quase inteiramente por companhias internacionais de longo curso, o que faz sentido: voos internacionais usam aeronaves maiores (wide-body) e têm menos frequência que voos domésticos.

Whitejets aparece disparada em 1º lugar com 540 passageiros/decolagem, mas isso vem de apenas 39 decolagens e 21 mil passageiros no total. Seria necessário mais informações para validade dos dados que hoje não temos em nosso projeto.

Grupo de liderança consistente (KLM, Norwegian UK, Japan Airlines, Air France): entre 260 e 280 passageiros por decolagem, todas companhias que operam em rotas intercontinentais longas.

Bloco intermediário europeu robusto: ITA Airways, Air Europa, Edelweiss, Ibéria, Alitalia e Lufthansa formam um grupo estável na faixa de 217 a 258 passageiros/decolagem — reflexo do padrão europeu de voos transatlânticos com boa ocupação.

TAP se destaca pelo volume, não pela média: apesar de ter a maior base de passageiros e decolagens da lista (33,4 milhões de passageiros em mais de 158 mil decolagens), fica na 19ª posição em média (211,16) — sinal de uma operação mais pulverizada, com voos menores e mais frequentes, provavelmente incluindo rotas regionais entre Brasil e Portugal.

Em resumo: a métrica de "passageiros por decolagem" favorece companhias intercontinentais com aeronaves grandes e poucas frequências — não deve ser confundida com volume total transportado, onde TAP, KLM e Ibéria lideram disparado.

