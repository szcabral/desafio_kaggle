# Contexto

Este conjunto de dados reúne informações reais sobre startups de diferentes setores, incluindo histórico de rodadas de investimento, valores captados, localização e áreas de atuação.  
O objetivo é **prever se uma startup terá sucesso (ativa/adquirida) ou insucesso (fechada)** com base nessas variáveis.

A base foi adaptada para fins acadêmicos: identificadores, colunas que poderiam gerar vazamento e valores inconsistentes foram removidos. Alguns campos podem conter valores ausentes (`NaN`), refletindo casos em que o evento não ocorreu ou não foi registrado.

Mais do que buscar o melhor desempenho, este desafio incentiva os participantes a explorar **técnicas de pré-processamento, seleção de variáveis e modelagem preditiva** aplicadas ao empreendedorismo e inovação.

---

# Visão Geral

- **Tarefa:** Classificação binária – prever labels (sucesso/insucesso da startup).  
- **Linhas:** 923  
- **Colunas:** 32  

### Observações gerais
- Colunas `age_*` podem ter `NaN` (evento não ocorreu).  
- `category_code` é uma variável categórica bruta.  
- As demais dummies são binárias 0/1.  

---

# Variável Alvo

- **labels** | `int64` | Target | Indicador de sucesso | {0, 1}  
  - `1 = sucesso` (ativa/adquirida) → 597 startups (~64,7%)  
  - `0 = insucesso` (fechada) → 326 startups (~35,3%)  

👉 A base está **moderadamente desbalanceada**, mas adequada para modelagem preditiva.

---

# Estrutura do Conjunto de Dados

## Idades relativas (anos desde a fundação até o evento)
Medidas contínuas em anos, com 2 casas decimais.  
Valores negativos foram tratados; `NaN` significa que o evento não ocorreu / está indisponível.

- `age_first_funding_year` | `float64` | Anos até o primeiro funding | ≥ 0 ou NaN  
- `age_last_funding_year` | `float64` | Anos até o último funding | ≥ 0 ou NaN  
- `age_first_milestone_year` | `float64` | Anos até o primeiro milestone | ≥ 0 ou NaN (muitos NaN)  
- `age_last_milestone_year` | `float64` | Anos até o último milestone | ≥ 0 ou NaN (muitos NaN)  

## Estrutura, histórico e escala de captação
- `relationships` | `int64` | Contagem de relações (fundadores, executivos, investidores) | ≥ 0  
- `funding_rounds` | `int64` | Número de rodadas de captação | ≥ 0  
- `funding_total_usd` | `float64` | Total captado (USD) | Outliers suavizados (IQR → valores extremos viraram NaN)  
- `milestones` | `int64` | Contagem de marcos relevantes | ≥ 0  
- `avg_participants` | `float64` | Média de investidores por rodada | ≥ 0  

## Localização (dummies de estado – binárias)
Representam o estado onde a startup está sediada.  
(Substituem `state_code`, removida para evitar redundância.)

- `is_CA`, `is_NY`, `is_MA`, `is_TX`, `is_otherstate` | `int64` | Estado (Califórnia, Nova Iorque, Massachusetts, Texas, Outros) | {0,1}  

## Setor/mercado (categórica + dummies)
- `category_code` | `object` | Setor principal declarado | string | Requer encoding (One-Hot/Target)  
- `is_software`, `is_web`, `is_mobile`, `is_enterprise`, `is_advertising`, `is_gamesvideo`, `is_ecommerce`, `is_biotech`, `is_consulting`, `is_othercategory` | `int64` | Indicadores de setor | {0,1}  

👉 Nota: você pode manter `category_code` (e fazer encoding) ou trabalhar apenas com as dummies já disponíveis.

## Sinalizadores de financiamento (rodadas e tipos)
- `has_VC` | `int64` | Recebeu venture capital? | {0,1}  
- `has_angel` | `int64` | Recebeu investimento angel? | {0,1}  
- `has_roundA`, `has_roundB`, `has_roundC`, `has_roundD` | `int64` | Teve a respectiva rodada? | {0,1}  

---

# Observações e Políticas de Dados

- **Faltantes (NaN):** principalmente em `age_*` e outliers de `funding_total_usd`.  
  → Tratar no pipeline (ex.: imputação por mediana ou uso de modelos robustos).  
- **Escalas:** variáveis como `funding_total_usd`, `relationships`, `funding_rounds` e `avg_participants` têm ordens de grandeza diferentes.  
  → Recomenda-se **normalização/padronização (StandardScaler)** em modelos lineares.  
- **Vazamento evitado:** colunas como `status`, `closed_at`, `is_top500`, datas cruas e identificadores foram removidas.  
- **Balanceamento:** verifique a proporção de labels ao treinar.  
  → Se necessário, use **class_weight**, threshold tuning ou métricas robustas (AUC/F1).  

Este conjunto de dados foi adaptado para fins educacionais e busca promover **aprendizado prático em empreendedorismo e modelagem preditiva**.

---

# Arquivos

- `train.csv` – dados de treino  
- `test.csv` – dados de teste  
- `sample_submission.csv` – exemplo de submissão em formato correto  
