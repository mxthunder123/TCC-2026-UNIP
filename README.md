# TCC-2026-UNIP

Código do Trabalho de Conclusão de Curso **"Desenvolvimento e avaliação de modelos de visão computacional para classificação de lesões cutâneas em imagens dermatoscópicas"**, do curso de Ciência da Computação da Universidade Paulista (UNIP), 2026.

O trabalho compara a ResNet50 e a EfficientNet-B0, treinadas com transferência de aprendizado, na classificação de sete classes de lesões de pele. Os dados são um recorte do conjunto de treinamento do ISIC 2019: 23.391 imagens, sem o carcinoma espinocelular e com a categoria BKL restrita à ceratose seborreica.

> Este código faz parte de uma pesquisa experimental. Os modelos não são um dispositivo médico e não devem ser usados para diagnóstico.

## Estrutura

O pipeline segue as etapas descritas no Capítulo 4 do trabalho (Seção 4.5), com um notebook para cada uma (01 a 09), executados nesta ordem. O notebook 00 executa todos em sequência.

| Notebook | Etapa |
|---|---|
| `00_executar_todos.ipynb` | Executa os notebooks 01 a 09 em sequência, na mesma sessão | — |
| `01_coleta_ingestao.ipynb` | Coleta e ingestão dos dados (isic-cli, coleção 65 do ISIC Archive) | 
| `02_limpeza_analise_exploratoria.ipynb` | Limpeza dos dados e análise exploratória | 
| `03_preprocessamento.ipynb` | Pré-processamento das imagens (DullRazor, 224 × 224) e divisão estratificada e agrupada por lesão | 
| `04_pipeline_dados_pytorch.ipynb` | Construção do pipeline de dados em PyTorch | 
| `05_modelo_baseline.ipynb` | Construção de um modelo baseline | 
| `06_treinamento_fine_tuning.ipynb` | Treinamento com fine-tuning em duas etapas, incluindo as ablações e a seleção da configuração final | 
| `07_avaliacao_teste.ipynb` | Avaliação de desempenho no conjunto de teste | 
| `08_interpretabilidade_gradcam.ipynb` | Interpretabilidade com Grad-CAM | 
| `09_analise_subgrupos.ipynb` | Análise por subgrupos demográficos (sexo e faixa etária) e empacotamento dos resultados | 

Os notebooks se comunicam apenas pelos arquivos gravados em disco (CSVs, JSONs, imagens processadas e checkpoints). As funções de carregamento de dados do notebook 04 são reutilizadas nos notebooks 05 a 09 e aparecem neles marcadas com o comentário `# [Célula reutilizada de ...]`.

## Como executar no Google Colab

**Opção 1: tudo de uma vez.** Abra o `00_executar_todos.ipynb` no Colab, selecione a GPU e execute as células. Ele baixa este repositório e roda os nove notebooks em sequência, na mesma sessão, com `%run`. Cada etapa fica numa célula própria, então dá para retomar a partir de uma etapa se a sessão cair.

**Opção 2: um notebook por vez.**

1. Abra cada notebook no Colab e selecione *Ambiente de execução → Alterar tipo de ambiente de execução → GPU*. A GPU é necessária a partir do notebook 05.
2. Cada notebook começa com uma célula de **persistência no Google Drive**. Ela liga a pasta `/content/data` à pasta `MyDrive/TCC_UNIP/data`, para que os arquivos de um notebook fiquem disponíveis para os seguintes, já que cada notebook roda em uma sessão própria do Colab. Para rodar todos os notebooks na mesma sessão, defina `USAR_DRIVE = False`.
3. Execute os notebooks de 01 a 09, em ordem. Gravar e ler as imagens no Drive é mais lento do que no disco local do Colab.

As imagens não estão neste repositório: são públicas e são baixadas do ISIC Archive pelo notebook 01.

## Principais arquivos gerados

Todos ficam em `/content/data/processed/`:

- **Partições:** `train.csv`, `val.csv`, `test.csv` e as versões `*_sem_dullrazor.csv`, com o `isic_id` de cada imagem.
- **Auditoria da divisão:** `split_audit.csv`, `perceptual_duplicates.csv` e `split_config.json`.
- **Configuração:** `pipeline_config.json` e `experimentos/config_final.json`.
- **Resultados:** `resultados_comparacao.json`, `avaliacao/`, `gradcam/` e `equidade/`.
As sementes são fixadas (42). Mesmo assim, uma nova execução pode produzir valores ligeiramente diferentes, porque algumas operações da GPU não são determinísticas e os processos de carregamento de imagens não recebem semente própria (Seção 5.5 do trabalho). As saídas exibidas nos notebooks são as da execução original, feita em um único notebook no Goo
