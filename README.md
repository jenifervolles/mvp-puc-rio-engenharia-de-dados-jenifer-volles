### MPV Puc Rio - Engenharia de dados

### Nome: Jenifer Estefani Volles


## OBJETIVO

Desenvolver um pipeline de Engenharia de Dados para tratamento, organização e análise de dados do transporte aéreo. O projeto busca transformar dados brutos em informações estruturadas que permitam responder a questões relacionadas à movimentação de passageiros, rotas, empresas aéreas e características das operações ao longo do período analisado, utilizando técnicas de tratamento, modelagem dimensional e consultas SQL.

### Perguntas do projeto
1. Como a quantidade de passageiros transportados evoluiu ao longo dos anos?
2. Quais foram as rotas com maior movimentação de passageiros ao longo do período analisado?
3. Quais empresas aéreas apresentaram a maior média de passageiros por decolagem?
4. Quais rotas apresentaram as maiores distâncias entre os aeródromos de origem e destino?
5. Quais etapas de voo apresentaram as maiores quantidades de passageiros transportados?


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


Retorno da consulta:


| col_name | data_type | comment |
|---|---|---|
| EMPRESA_NOME | string | Nome completo da empresa aérea |
| EMPRESA_NACIONALIDADE | string | Nacionalidade da empresa: BRASILEIRA ou ESTRANGEIRA |
| ANO | int | Ano do registro |
| MES | int | Mês do registro (1 a 12) |
| AEROPORTO_DE_ORIGEM_NOME | string | Nome do aeroporto de origem. NULL para voos internacionais sem origem registrada (5.212 registros) |
| AEROPORTO_DE_ORIGEM_PAIS | string | País do aeroporto de origem. NULL para voos internacionais sem origem registrada |
| AEROPORTO_DE_DESTINO_NOME | string | Nome do aeroporto de destino |
| AEROPORTO_DE_DESTINO_PAIS | string | País do aeroporto de destino |
| NATUREZA | string | Natureza do voo: DOMÉSTICA ou INTERNACIONAL |
| GRUPO_DE_VOO | string | Grupo do voo: IMPRODUTIVO, NÃO IDENTIFICADO, NÃO REGULAR ou REGULAR. NULL em 2 registros |
| PASSAGEIROS_PAGOS | int | Total de passageiros pagos. NULL em 3,7% dos registros |
| PASSAGEIROS_GRATIS | int | Total de passageiros grátis. NULL em 3,7% dos registros |
| DISTANCIA_VOADA_KM | int | Distância voada em km. NULL em 21,8% dos registros |
| ASSENTOS | int | Total de assentos. NULL em 21,7% dos registros |
| DECOLAGENS | int | Quantidade de decolagens realizadas na etapa de voo. NULL em 21,7% dos registros |


### Camada Gold

### Dimensão Empresa

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


### Dimensão Aeroporto

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


### Dimensão Natureza

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

**Tabela Fato → Dimensões**


| Fato (`fato_voos_gold`) | FK | Dimensão | PK | Cardinalidade | NULLs na FK |
| --- | --- | --- | --- | --- | --- |
| `EMPRESA_ID` | → | `dim_empresa_gold` | `EMPRESA_ID` | N:1 | 0 |
| `AEROPORTO_ORIGEM_ID` | → | `dim_aeroporto_gold` | `AEROPORTO_ID` | N:1 | 5.212 (voos internacionais sem origem) |
| `AEROPORTO_DESTINO_ID` | → | `dim_aeroporto_gold` | `AEROPORTO_ID` | N:1 | 0 |
| `NATUREZA_ID` | → | `dim_natureza_gold` | `NATUREZA_ID` | N:1 | 0 |
| `GRUPO_VOO_ID` | → | `dim_grupo_voo_gold` | `GRUPO_VOO_ID` | N:1 | 2 (registros sem grupo) |


**Dimensões (detalhe)**


| Dimensão | PK | Registros | Atributos |
| --- | --- | --- | --- |
| `dim_empresa_gold` | `EMPRESA_ID` | 328 | `EMPRESA_NOME`, `EMPRESA_NACIONALIDADE` |
| `dim_aeroporto_gold` | `AEROPORTO_ID` | 1.058 | `AEROPORTO_NOME`, `PAIS` |
| `dim_natureza_gold` | `NATUREZA_ID` | 2 | `NATUREZA` (`DOMÉSTICA`, `INTERNACIONAL`) |
| `dim_grupo_voo_gold` | `GRUPO_VOO_ID` | 4 | `GRUPO_DE_VOO` (`IMPRODUTIVO`, `NÃO IDENTIFICADO`, `NÃO REGULAR`, `REGULAR`) |


> **Nota:** `dim_aeroporto_gold` é usada **duas vezes** na fato — uma para origem e outra para destino. Por isso a tabela tem 5 FKs mas apenas 4 dimensões.


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


<img width="907" height="622" alt="image" src="https://github.com/user-attachments/assets/fb94c580-6b2a-4fe5-98b8-adcddc41c386" />


Retorno da consulta:


| ANO | total_passageiros | passageiros_pagos | passageiros_gratis |
|---:|---:|---:|---:|
| 2000 | 38648361 | 37413460 | 1234901 |
| 2001 | 40107695 | 38545299 | 1562396 |
| 2002 | 40008782 | 38301313 | 1707469 |
| 2003 | 38188226 | 37203867 | 984359 |
| 2004 | 42200355 | 41174131 | 1026224 |
| 2005 | 50408315 | 49106864 | 1301451 |
| 2006 | 55035245 | 53863577 | 1171668 |
| 2007 | 60775826 | 59611480 | 1164346 |
| 2008 | 64792452 | 63378804 | 1413648 |
| 2009 | 71203924 | 69555497 | 1648427 |
| 2010 | 87054203 | 85343074 | 1711129 |
| 2011 | 101784909 | 99766620 | 2018289 |
| 2012 | 109610630 | 107403734 | 2206896 |
| 2013 | 112005233 | 109724283 | 2280950 |
| 2014 | 119302771 | 117127171 | 2175600 |
| 2015 | 119707642 | 117646606 | 2061036 |
| 2016 | 111457276 | 109532901 | 1924375 |
| 2017 | 114384204 | 112475650 | 1908554 |
| 2018 | 120104760 | 117742390 | 2362370 |
| 2019 | 121651373 | 119229088 | 2422285 |
| 2020 | 52433193 | 51277301 | 1155892 |
| 2021 | 68762139 | 67413559 | 1348580 |
| 2022 | 100084311 | 98021465 | 2062846 |
| 2023 | 115362473 | 113052661 | 2309812 |
| 2024 | 121360316 | 119186351 | 2173965 |
| 2025 | 132902708 | 130685074 | 2217634 |
| 2026 | 79093270 | 77621120 | 1472150 |


<img width="1027" height="520" alt="image" src="https://github.com/user-attachments/assets/ec3aad88-39b9-4e23-9feb-90be0bb16ac0" />


A evolução mostra cinco momentos:

- Crescimento (2000–2015): o total saiu de 38,6 milhões para 119,7 milhões de passageiros, mais de 3x. Houve pequenas quedas em 2002 (−0,2%) e 2003 (−4,6%), e o trecho mais acelerado foi 2009–2013, de 71,2 para 112,0 milhões.
- Oscilação e novo pico (2016–2019): em 2016 o total caiu 6,9% (de 119,7 para 111,5 milhões). Depois houve recuperação até 121,7 milhões em 2019, o maior valor até então.
- Pandemia (2020–2021): em 2020 o total cai para 52,4 milhões, uma queda de 56,9% em relação a 2019. Em 2021 sobe 31,1% (68,8 milhões), mas ainda fica 43,5% abaixo de 2019.
- Recuperação (2022–2025): 100,1 milhões em 2022 (82% de 2019) e 115,4 milhões em 2023 (94,8%). Em 2024 o total chega a 121,4 milhões, praticamente o nível de 2019 (99,8%). Em 2025 chega a 132,9 milhões, recorde da série: 9,2% acima de 2019 e 9,5% acima de 2024.
- 2026: os 79,1 milhões refletem um ano em andamento, e não uma queda.


**Pergunta 2: Quais foram as rotas com maior movimentação de passageiros ao longo do período analisado?**

*Consulta limitada a 20*


<img width="1319" height="676" alt="image" src="https://github.com/user-attachments/assets/ef88b6de-32ca-4bf6-94cd-f873227611f7" />


Retorno da consulta:


| AEROPORTO_ORIGEM_NOME | AEROPORTO_ORIGEM_PAIS | AEROPORTO_DESTINO_NOME | AEROPORTO_DESTINO_PAIS | quantidade_voos | total_passageiros |
|---|---|---|---|---:|---:|
| SÃO PAULO | BRASIL | RIO DE JANEIRO | BRASIL | 587452 | 53949158 |
| RIO DE JANEIRO | BRASIL | SÃO PAULO | BRASIL | 583384 | 53790305 |
| SÃO PAULO | BRASIL | BRASÍLIA | BRASIL | 210743 | 23070152 |
| BRASÍLIA | BRASIL | SÃO PAULO | BRASIL | 209173 | 22848154 |
| RIO DE JANEIRO | BRASIL | GUARULHOS | BRASIL | 233825 | 19475429 |
| BRASÍLIA | BRASIL | RIO DE JANEIRO | BRASIL | 193313 | 19454080 |
| RIO DE JANEIRO | BRASIL | BRASÍLIA | BRASIL | 191827 | 19417812 |
| GUARULHOS | BRASIL | RIO DE JANEIRO | BRASIL | 234433 | 19027414 |
| SALVADOR | BRASIL | GUARULHOS | BRASIL | 153662 | 18966973 |
| GUARULHOS | BRASIL | SALVADOR | BRASIL | 158116 | 18775088 |
| RECIFE | BRASIL | GUARULHOS | BRASIL | 131262 | 18624146 |
| GUARULHOS | BRASIL | RECIFE | BRASIL | 131680 | 18621096 |
| PORTO ALEGRE | BRASIL | GUARULHOS | BRASIL | 156678 | 18204097 |
| GUARULHOS | BRASIL | PORTO ALEGRE | BRASIL | 153312 | 18045086 |
| SÃO PAULO | BRASIL | PORTO ALEGRE | BRASIL | 137198 | 17846470 |
| PORTO ALEGRE | BRASIL | SÃO PAULO | BRASIL | 136168 | 17641995 |
| SÃO PAULO | BRASIL | SÃO JOSÉ DOS PINHAIS | BRASIL | 172295 | 16711598 |
| SÃO JOSÉ DOS PINHAIS | BRASIL | SÃO PAULO | BRASIL | 171037 | 16560914 |
| SÃO PAULO | BRASIL | CONFINS | BRASIL | 147267 | 16437136 |
| CONFINS | BRASIL | SÃO PAULO | BRASIL | 146387 | 16258947 |


<img width="956" height="860" alt="image" src="https://github.com/user-attachments/assets/59583595-d5e5-45e2-b692-231afbaa6cb1" />


Os 20 registros são 10 pares de aeroportos, cada um com dois sentidos:

- A ponte aérea São Paulo–Rio de Janeiro lidera com folga. Somando os dois sentidos, são 107,7 milhões de passageiros e cerca de 1,17 milhão de decolagens, 2,3x o segundo par (São Paulo–Brasília, com 45,9 milhões).
- O fluxo é equilibrado nos dois sentidos. Em todos os pares, a diferença entre ida e volta é de no máximo 2,3%.
- São Paulo é o eixo da malha. Em 9 dos 10 pares, um dos aeroportos é paulistano (SÃO PAULO ou GUARULHOS, como aparecem na base). Eles conectam Rio de Janeiro, Brasília, Salvador, Recife, Porto Alegre, São José dos Pinhais (Curitiba) e Confins (Belo Horizonte). Guarulhos concentra as ligações com Rio, Salvador, Recife e Porto Alegre.
- Brasília–Rio de Janeiro é a única exceção. Com 38,9 milhões, é o terceiro maior par e supera Rio–Guarulhos (38,5 milhões) e as ligações de Guarulhos com Salvador, Recife e Porto Alegre.
- Os demais pares ficam entre 32,7 e 38,5 milhões de passageiros.

O ranking agrupa por nome do aeroporto, então uma mesma cidade pode reunir mais de um aeroporto (como o Rio de Janeiro). O limite de 20 registros cobre só os 10 pares mais movimentados.


**Pergunta 3: Quais empresas aéreas apresentaram a maior média de passageiros por decolagem?**

*Consulta limitada a 20*


<img width="1129" height="700" alt="image" src="https://github.com/user-attachments/assets/6f0ac30c-a8b3-4dfb-9638-6a89f21e4b16" />


Retorno da consulta:

| EMPRESA_NOME | total_passageiros | total_decolagens | media_passageiros_por_decolagem |
|---|---:|---:|---:|
| WHITEJETS TRANSPORTES AÉREOS S.A. | 21066 | 39 | 540.15 |
| KLM CIA. REAL HOLANDESA DE AVIAÇÃO | 7801026 | 27887 | 279.74 |
| NORWEGIAN AIR UK LIMITED | 107587 | 402 | 267.63 |
| JAPAN AIRLINES INTERNATIONAL COMPANY LIMITED | 1022354 | 3921 | 260.74 |
| SOCIÉTÉ AIR FRANCE | 15488703 | 59859 | 258.75 |
| ITALIA TRANSPORTO AEREO S.P.A. | 1788591 | 6953 | 257.24 |
| AIR EUROPA LINEAS AEREAS SOCIEDAD ANONIMA | 3824484 | 15087 | 253.50 |
| EDELWEISS AIR AG | 197258 | 800 | 246.57 |
| IBÉRIA LINEAS AEREAS DE ESPAÑA SOCIEDAD ANONIMA OPERADORA | 9727150 | 39521 | 246.13 |
| IBERWORLD AIRLINES S.A. | 29748 | 126 | 236.10 |
| EL AL ISRAEL AIRLINES LTD | 128155 | 558 | 229.67 |
| ALITALIA SOCIETA AEREA ITALIANA S.P.A. | 5194620 | 22757 | 228.26 |
| CONDOR FLUGDIENST GMBH | 803956 | 3569 | 225.26 |
| AIGLE AZUR | 100178 | 450 | 222.62 |
| LOT POLISH AIRLINES | 19098 | 86 | 222.07 |
| AIR CARAIBES ATLANTIQUE | 1324 | 6 | 220.67 |
| DEUTSCHE LUFTHANSA A.G. | 9815421 | 45191 | 217.20 |
| ETIHAD AIRWAYS P.J.S.C. | 530450 | 2447 | 216.78 |
| TAP - TRANSPORTES AÉREOS PORTUGUESES S/A | 33409071 | 158217 | 211.16 |
| AIR TRANSAT A.T. INC DO BRASIL | 20796 | 100 | 207.96 |


<img width="960" height="879" alt="image" src="https://github.com/user-attachments/assets/b4e58ef2-b175-4f8f-97c3-5a45fecdc630" />


- Whitejets (540 passageiros por decolagem) não é comparável ao restante. O valor vem de apenas 39 decolagens e 21 mil passageiros, e é muito superior ao que a maioria das aeronaves comerciais comporta. Sugere um problema de registro, e seria preciso validá-lo antes de usar.
- Outras amostras pequenas pedem cautela: Air Caraïbes (6 decolagens), LOT (86), Air Transat (100) e Iberworld (126).
- Entre as empresas com volume relevante, KLM lidera com 279,7 passageiros por decolagem, seguida de Japan Airlines (260,7) e Air France (258,8). Norwegian UK aparece com 267,6, mas em apenas 402 decolagens.
- Bloco intermediário: ITA Airways, Air Europa, Ibéria, Alitalia, Condor e Lufthansa ficam entre 217 e 257 passageiros por decolagem.
- TAP tem a menor média do top 20 entre as grandes (211,2), mas o maior volume. São 33,4 milhões de passageiros em 158 mil decolagens, mais que o dobro da segunda colocada em passageiros, a Air France (15,5 milhões). Em seguida vêm Lufthansa (9,8 milhões), Ibéria (9,7 milhões) e KLM (7,8 milhões).


**Pergunta 4: Quais rotas apresentaram as maiores distâncias entre os aeródromos de origem e destino?**

*Consulta limitada a 10*


<img width="1397" height="724" alt="image" src="https://github.com/user-attachments/assets/a612fd5b-6c01-4c43-ad9b-8734fb3ec9b2" />


Retorno da consulta:


| AEROPORTO_ORIGEM_NOME | AEROPORTO_ORIGEM_PAIS | AEROPORTO_DESTINO_NOME | AEROPORTO_DESTINO_PAIS | distancia_voada_km | quantidade_decolagens |
|---|---|---|---|---:|---:|
| GUARULHOS | BRASIL | DOHA | QATAR | 1043504 | 7839 |
| DOHA | QATAR | GUARULHOS | BRASIL | 1019788 | 7814 |
| GUARULHOS | BRASIL | PARIS | FRANÇA | 1015740 | 29105 |
| PARIS | FRANÇA | GUARULHOS | BRASIL | 1015740 | 28966 |
| PANAMA | PANAMÁ | GUARULHOS | BRASIL | 951082 | 29754 |
| GUARULHOS | BRASIL | PANAMA | PANAMÁ | 945996 | 29814 |
| GUARULHOS | BRASIL | MIAMI, FLORIDA | ESTADOS UNIDOS DA AMÉRICA | 815176 | 45669 |
| MIAMI, FLORIDA | ESTADOS UNIDOS DA AMÉRICA | GUARULHOS | BRASIL | 808602 | 48406 |
| GUARULHOS | BRASIL | LISBOA | PORTUGAL | 769695 | 20976 |
| LISBOA | PORTUGAL | GUARULHOS | BRASIL | 769695 | 20839 |


<img width="974" height="624" alt="image" src="https://github.com/user-attachments/assets/354ff3af-a193-4f42-a25b-ef0683d6f4e0" />


- Guarulhos ↔ Doha (Qatar) tem a maior distância entre aeródromos. Guarulhos → Doha soma 1.043.504 km e Doha → Guarulhos, 1.019.788 km. É a ligação mais longa da lista, mas também uma das menos frequentes, com cerca de 7,8 mil decolagens por sentido.
- Guarulhos ↔ Paris ocupa o terceiro e o quarto lugar, empatados. Os dois sentidos têm exatamente 1.015.740 km, com cerca de 29 mil decolagens cada. É uma rota longa e com frequência alta.
- Panamá ↔ Guarulhos vem logo depois. Panamá → Guarulhos tem 951.082 km e Guarulhos → Panamá, 945.996 km, ambos com quase 30 mil decolagens.
- Miami e Lisboa fecham o top 10. Guarulhos → Miami tem 815.176 km e Miami → Guarulhos, 808.602 km, com o maior volume do grupo (mais de 45 mil decolagens por sentido). Guarulhos ↔ Lisboa tem 769.695 km nos dois sentidos, com cerca de 21 mil decolagens.

Todas as 10 rotas mais longas são intercontinentais e têm Guarulhos como uma das pontas. Os trechos para o Oriente Médio e a Europa são os mais distantes, seguidos pelas rotas para as Américas Central e do Norte.


**Pergunta 5: Quais etapas de voo apresentaram as maiores quantidades de passageiros transportados?**

*Consulta limitada a 10*


<img width="1526" height="707" alt="image" src="https://github.com/user-attachments/assets/3c42dadb-b9d2-4719-ac71-6b9256e86bbd" />


Retorno da consulta:

| VOO_ID | MES_ANO | EMPRESA_NOME | EMPRESA_NACIONALIDADE | AEROPORTO_ORIGEM_NOME | AEROPORTO_DESTINO_NOME | NATUREZA | GRUPO_DE_VOO | total_passageiros | PASSAGEIROS_PAGOS | PASSAGEIROS_GRATIS | DISTANCIA_VOADA_KM | DECOLAGENS | distancia_por_decolagem_km | passageiros_por_decolagem | ASSENTOS |
|---:|---|---|---|---|---|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 850691 | 12/2019 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 93719 | 92357 | 1362 | 256566 | 701 | 366 | 133.7 | 107394 |
| 845077 | 01/2019 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | RIO DE JANEIRO | SÃO PAULO | DOMÉSTICA | REGULAR | 93347 | 91980 | 1367 | 262788 | 718 | 366 | 130 | 103392 |
| 849617 | 10/2019 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | RIO DE JANEIRO | SÃO PAULO | DOMÉSTICA | REGULAR | 92828 | 91760 | 1068 | 267546 | 731 | 366 | 127 | 105414 |
| 851089 | 01/2020 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | RIO DE JANEIRO | SÃO PAULO | DOMÉSTICA | REGULAR | 91898 | 90450 | 1448 | 253638 | 693 | 366 | 132.6 | 106470 |
| 364265 | 09/2014 | GOL LINHAS AÉREAS S.A. (EX- VRG LINHAS AÉREAS S.A.) | BRASILEIRA | RIO DE JANEIRO | SÃO PAULO | DOMÉSTICA | REGULAR | 91515 | 88965 | 2550 | 294630 | 805 | 366 | 113.7 | 142407 |
| 406532 | 12/2019 | GOL LINHAS AÉREAS S.A. (EX- VRG LINHAS AÉREAS S.A.) | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 90239 | 88017 | 2222 | 255834 | 699 | 366 | 129.1 | 124878 |
| 844657 | 12/2018 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 89804 | 88386 | 1418 | 257664 | 704 | 366 | 127.6 | 101376 |
| 364401 | 09/2014 | GOL LINHAS AÉREAS S.A. (EX- VRG LINHAS AÉREAS S.A.) | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 88861 | 86134 | 2727 | 294264 | 804 | 366 | 110.5 | 142191 |
| 849702 | 10/2019 | TAM LINHAS AÉREAS S.A. | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 88398 | 87606 | 792 | 269742 | 737 | 366 | 119.9 | 106278 |
| 366834 | 12/2014 | GOL LINHAS AÉREAS S.A. (EX- VRG LINHAS AÉREAS S.A.) | BRASILEIRA | SÃO PAULO | RIO DE JANEIRO | DOMÉSTICA | REGULAR | 88375 | 85788 | 2587 | 272670 | 745 | 366 | 118.6 | 125157 |


<img width="962" height="783" alt="image" src="https://github.com/user-attachments/assets/376434c6-a176-4021-8895-704779898a86" />


- Todos os 10 registros são da ponte aérea São Paulo–Rio de Janeiro. São voos domésticos regulares de empresas brasileiras, num trecho de 366 km. Cada barra é o total de um mês para uma empresa em uma rota, com todas as decolagens somadas. A TAM tem 6 registros e a GOL, 4.
- O recorde é da TAM, em dezembro de 2019. São Paulo → Rio de Janeiro somou 93.719 passageiros em 701 decolagens, média de 133,7 por decolagem. Rio → São Paulo (TAM, janeiro de 2019) vem logo atrás, com 93.347 passageiros em 718 decolagens. Os cinco primeiros ficam dentro de 2,4% um do outro.
- Todos os registros são anteriores à pandemia, entre setembro de 2014 e janeiro de 2020. Cada um tem entre 693 e 805 decolagens no mês, ou seja, de 22 a 27 por dia.
- A TAM enche mais os aviões. Seus registros têm 120 a 134 passageiros por decolagem e ocupação de 83% a 90% dos assentos. Os da GOL têm 110 a 129 passageiros por decolagem e ocupação de 62% a 72%.
- A GOL oferecia mais assentos por decolagem: entre 168 e 179, contra 144 a 154 da TAM. Os dois registros de setembro de 2014 têm o maior número de decolagens (805 e 804) e a menor ocupação (64% e 62%).
- Passageiros gratuitos são poucos, mas variam por empresa: de 0,9% a 1,6% na TAM e de 2,5% a 3,1% na GOL.


##AUTOAVALIAÇÃO

A realização deste projeto permitiu ampliar meus conhecimentos sobre o processo de Engenharia de Dados, principalmente em relação às etapas de coleta, tratamento, organização, modelagem e análise de dados. Ao longo do desenvolvimento, pude compreender de forma mais prática o conceito de arquitetura em camadas, utilizando o modelo Medalhão (Bronze, Silver e Gold), além da importância de cada etapa para transformar dados brutos em informações estruturadas e adequadas para análise.

Também pude aprofundar meus conhecimentos sobre qualidade e tratamento de dados, realizando atividades como remoção de duplicidades, tratamento de valores nulos, conversão de tipos de dados e validação das informações. Outro aprendizado importante foi a utilização da modelagem dimensional, com a criação de tabelas de dimensão e fato, além da utilização de identificadores e relacionamentos entre essas estruturas.

O projeto também contribuiu para meu desenvolvimento em SQL e Databricks, especialmente na criação e consulta das tabelas, na organização das diferentes camadas e na elaboração de consultas capazes de responder às perguntas propostas inicialmente. Além disso, a construção do catálogo de dados me ajudou a compreender melhor a importância da documentação dos campos, seus tipos e significados para garantir maior entendimento e governança dos dados.

Atualmente, não atuo diretamente na área de Engenharia de Dados. Em agosto de 2026, iniciei minha atuação profissional como Data Analyst, portanto não tenho contato direto com as atividades desempenhadas por um profissional de Engenharia de Dados. Apesar disso, alguns dos conceitos trabalhados no projeto já faziam parte do meu conhecimento, principalmente por eu já trabalhar com dados e SQL. Inclusive, já desenvolvi views que são utilizadas na camada Gold, o que tornou possível relacionar parte do conteúdo estudado com uma atividade que já faz parte da minha rotina profissional.

Por fim, esse projeto me proporcionou uma visão mais prática e completa sobre o fluxo de dados. Mais do que conhecer os conceitos teoricamente, pude compreender como eles se conectam em um projeto real: desde a obtenção dos dados brutos, passando pelo tratamento e organização, até sua disponibilização estruturada para análise. Esse conhecimento também ampliou minha visão sobre a relação entre as áreas de Engenharia de Dados e Análise de Dados e poderá contribuir para minha evolução profissional.
