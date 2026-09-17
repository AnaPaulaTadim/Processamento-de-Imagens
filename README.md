# Classificação Few-Shot de Doenças em Plantas via Fusão de Características Global-Local 🌿

## Sobre o projeto

Este projeto implementa uma abordagem de **Few-Shot Learning (FSL)** para classificação de doenças em folhas de plantas, baseada na **fusão de características globais e locais com mecanismo de autoatenção**.

A proposta é investigar como um modelo pode realizar a classificação de diferentes doenças utilizando apenas uma pequena quantidade de imagens de referência, reduzindo a necessidade de grandes conjuntos de dados específicos para cada nova classe.

A abordagem foi baseada no trabalho **"Few-Shot Image Classification Algorithm Based on Global–Local Feature Fusion"**, adaptando sua metodologia para um cenário de classificação de doenças em plantas.

---

## Objetivo

O objetivo principal foi implementar e avaliar um modelo capaz de classificar doenças em folhas utilizando o paradigma **5-way 5-shot**.

Nesse cenário:

- **5-way:** o modelo deve distinguir entre 5 classes;
- **5-shot:** são utilizadas apenas 5 imagens de referência para cada classe.

Assim, apenas **25 imagens de suporte** são utilizadas para construir as representações das classes.

Posteriormente, o modelo é avaliado utilizando imagens que não foram utilizadas na construção dos protótipos.

---

## Classes utilizadas

Foram utilizadas cinco classes de folhas:

1. `Potato Early Blight` — Pinta-preta da batata
2. `Potato Late Blight` — Requeima da batata
3. `Tomato Bacterial Spot` — Mancha bacteriana do tomate
4. `Tomato Late Blight` — Requeima do tomate
5. `Tomato Healthy` — Tomate saudável

O conjunto de consulta utilizado no experimento possui **500 imagens**, sendo:

- 100 imagens de Potato Early Blight;
- 100 imagens de Potato Late Blight;
- 100 imagens de Tomato Bacterial Spot;
- 100 imagens de Tomato Late Blight;
- 100 imagens de Tomato Healthy.

---

## Metodologia

### 1. ResNet18 pré-treinada

A arquitetura utiliza uma **ResNet18 pré-treinada na base ImageNet** como extrator de características.

A ResNet18 não é treinada novamente durante o experimento. Seus pesos permanecem **congelados**, sendo utilizada somente para transformar as imagens em vetores de características.

Dessa forma, não foi realizado o processo convencional de fine-tuning específico para as doenças utilizadas no experimento.

---

### 2. Fusão Global-Local

A imagem é analisada através de duas representações complementares.

#### Ramo Global

A imagem completa é processada pela ResNet18 para obter características relacionadas à estrutura geral da folha, como:

- formato;
- contorno;
- estrutura;
- características gerais da planta.

#### Ramo Local

Também são analisadas regiões específicas da imagem através de cinco recortes:

- canto superior esquerdo;
- canto superior direito;
- canto inferior esquerdo;
- canto inferior direito;
- região central.

Esses recortes permitem explorar características locais relacionadas às doenças, como manchas, alterações de textura e necroses.

---

### 3. Mecanismo de Autoatenção

As características obtidas dos recortes locais são processadas por um mecanismo de **Multi-Head Self-Attention**.

A autoatenção permite relacionar as diferentes regiões da folha e atribuir maior importância às regiões que apresentam características relevantes para a classificação.

---

### 4. Fusão das características

Após o processamento, a representação global e a representação local refinada pela autoatenção são normalizadas e concatenadas.

O resultado é um único vetor contendo informações:

- globais da folha;
- locais das regiões analisadas.

Esse vetor é utilizado posteriormente na classificação.

---

## Classificação por protótipos

A classificação é realizada utilizando uma abordagem baseada em **protótipos**.

Primeiramente, as cinco imagens de suporte de cada classe são processadas pela arquitetura.

As representações geradas são então utilizadas para calcular a média dos vetores de cada classe.

Essa média representa o **protótipo da classe**.

Assim, são construídos cinco protótipos:

```text
5 imagens de suporte
        ↓
Extração de características
        ↓
Fusão Global-Local
        ↓
Média dos vetores
        ↓
Protótipo da classe
