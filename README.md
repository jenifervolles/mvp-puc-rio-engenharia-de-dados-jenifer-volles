### MPV Puc Rio - Engenharia de dados

### Nome: Jenifer Estefano Volles


## OBJETIVO

Desenvolver um pipeline de Engenharia de Dados para tratamento, organização e análise de dados do transporte aéreo. O projeto busca transformar dados brutos em informações estruturadas que permitam responder a questões relacionadas à movimentação de passageiros, rotas, empresas aéreas e características das operações ao longo do período analisado, utilizando técnicas de tratamento, modelagem dimensional e consultas SQL.

### Perguntas que serão respondidas
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

2. Tabela Bronze — Delta Lake: o arquivo .csv original reside no Volume /Volumes/workspace/default/dados_anac e os dados foram materializados como uma tabela Delta em workspace.default.dados_anac_bronze — ambos no catálogo workspace, schema default, com armazenamento físico no S3 da AWS.

<img width="359" height="632" alt="image" src="https://github.com/user-attachments/assets/4cfba32f-d15f-458b-a5ab-8139a9246fde" />


## MODELAGEM: ORGANIZANDO OS DADOS

Foi utilizado o modelo Flat nas camadas Bronze e Silver (dados brutos e limpos em tabela única) e um Star Schema na camada Gold (fato central + 4 dimensões desnormalizadas + view de consulta), com catálogo de dados documentado tanto em markdown quanto no Unity Catalog.
A linguagem utilizada durante esse projeto foi SQL.

Na camada Bronze, os dados foram armazenados conforme recebidos da fonte. Na camada Silver, foram aplicados tratamentos de qualidade e padronização. Na camada Gold, os dados foram organizados em um modelo dimensional, separando informações descritivas em dimensões e métricas de movimentação na tabela fato. A partir desse modelo foram realizadas as consultas destinadas a responder às perguntas de negócio.


## CARGA: CONSTRUINDO O PIPELINE DE ETL

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


Catálogo com detalhamento (Descrição) do significado de cada coluna.

*Os dados da **Descrição** foram realizados conforme a documentação dos metadados da ANAC: https://www.anac.gov.br/acesso-a-informacao/dados-abertos/areas-de-atuacao/voos-e-operacoes-aereas/dados-estatisticos-do-transporte-aereo/48-dados-estatisticos-do-transporte-aereo*

### Dicionário de Colunas (38 colunas)

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


