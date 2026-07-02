# Vacinação contra COVID-19 no Espírito Santo — Mineração de Dados

Projeto final da disciplina **Tópicos Especiais I (TSI)**, prof. Matheus Jagi. Pipeline completo de mineração de dados aplicado aos registros públicos de vacinação contra COVID-19 no Espírito Santo: coleta, limpeza, análise exploratória, clustering, detecção de outliers temporais e avaliação dos resultados.

## Artigo

**Segmentação de Perfis e Detecção de Anomalias Temporais na Campanha de Vacinação contra a Covid-19 no Espírito Santo** — artigo completo no formato SBC: [ler no Google Docs](https://docs.google.com/document/d/1BSNRcjCx-hSvrXQf2CiSYGXxezT-5UdBReMdw1PVQ9Q/edit?usp=sharing)

## Pergunta de pesquisa

Como se comportou a campanha de vacinação contra COVID-19 no Espírito Santo ao longo do tempo? Existem perfis distintos de vacinados que reflitam a estratégia de priorização adotada?

## Fonte dos dados

- **OpenDataSUS (RNDS/Ministério da Saúde)** — [Campanha Nacional de Vacinação COVID-19 — Dados ES Parte 1](https://dadosabertos.saude.gov.br/dataset/covid-19-vacinacao/resource/5093679f-12c3-4d6b-b7bd-07694de54173)
- Registros individuais, anonimizados e de acesso público, desde o início da campanha em 2021.
- Por limitação de memória do Colab, o carregamento foi limitado a 2 milhões de registros.
- Colunas utilizadas: identificador do paciente, idade, sexo biológico, fabricante, data de aplicação, município e descrição da dose.

## Pipeline

1. **Coleta e carregamento** — leitura do CSV via Google Drive montado no Colab.
2. **Limpeza e preparação** — remoção de duplicatas e nulos, correção de datas e encoding, padronização de nomes de municípios e fabricantes, filtro de idades plausíveis (0–115 anos), criação de colunas auxiliares de mês/ano.
3. **Análise exploratória** — estatísticas descritivas de idade, distribuição por sexo, série temporal de aplicações diárias, detecção de outliers de idade (método IQR), KPIs da campanha e mapa coroplético de vacinações por município.
4. **Mineração de dados**:
   - **K-Means Clustering** — segmentação de perfis de vacinados por idade e período de vacinação (features normalizadas com Z-Score; K escolhido via método do cotovelo e Silhouette Score).
   - **Análise de tendência + outliers temporais** — média móvel de 7 dias, tendência de 30 dias e detecção de dias anômalos (método IQR) na série de vacinações diárias.
5. **Avaliação dos resultados** — consolidação das métricas (Silhouette Score, inércia, % de dias outliers) e heatmap mensal de volume de vacinações.

## Principais resultados

- O K-Means (K=4) identificou **dois ciclos** de vacinação por idade, não uma relação linear simples:
  - Idosos (~61 anos) vacinados no início da campanha (ago/2021) — grupo prioritário inicial.
  - Adultos jovens (~28 anos) vacinados meses depois (out/2021) — abertura geral da campanha.
  - Um segundo grupo de idosos (~61 anos), bem mais tarde (out/2023) — coerente com doses de reforço.
  - Jovens (~16 anos) vacinados tardiamente (nov/2023) — ampliação de elegibilidade ou reforço.
  - Silhouette Score ≈ 0,45 (separação razoável entre clusters).
- A análise temporal confirma o arco esperado da campanha: crescimento acelerado no início de 2021, pico entre meados de 2021 e início de 2022, e queda progressiva depois. Picos anômalos coincidem provavelmente com mutirões/abertura de novos grupos; quedas bruscas com feriados e fins de semana prolongados.

## Limitações

- Dataset restrito à Parte 1 do ES — outras partes do OpenDataSUS ampliariam a análise.
- Clustering usa apenas idade e período; incluir município ou tipo de dose enriqueceria os perfis.
- Outliers temporais não foram validados com fontes externas (ex.: boletins da SESA-ES); as causas ficam no campo da hipótese.

## Tecnologias

Python, Pandas, NumPy, Matplotlib, Seaborn, GeoPandas, Scikit-learn (KMeans, StandardScaler, Silhouette Score), Google Colab.

## Como executar

1. Abra o notebook `mineracao_dados_covid19.ipynb` no [Google Colab](https://colab.research.google.com/).
2. Baixe o dataset "Dados ES - Parte 1" no [OpenDataSUS](https://dadosabertos.saude.gov.br/dataset/covid-19-vacinacao) e salve-o no seu Google Drive.
3. Ajuste o caminho do arquivo na variável `CAMINHO_ARQUIVO`.
4. Execute as células em ordem — a montagem do Drive será solicitada na primeira célula de código.

## Estrutura de saída

O notebook exporta três arquivos ao final:
- `dataset_covid_es_final.csv` — dados limpos.
- `outliers_temporais.csv` — dias com comportamento anômalo na série de vacinação.
- `perfil_clusters.csv` — amostra usada no clustering com o cluster atribuído.

## Autor

Alexandre Venturini
