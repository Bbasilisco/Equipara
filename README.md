# Equipara · Isolando o Gap Salarial de Gênero com Machine Learning

> **Acesse a aplicação em produção:** [https://bbasilisco.github.io/Equipara/](https://bbasilisco.github.io/Equipara/)

O **Equipara** é uma ferramenta interativa desenvolvida para transformar diagnósticos complexos sobre disparidade salarial de gênero no mercado de trabalho formal brasileiro em análises executáveis e acionáveis. 

Em conformidade com as exigências da **Lei nº 14.611/2023** (Relatório de Transparência Salarial), o projeto utiliza modelos de Machine Learning treinados sobre microdados da **RAIS (Relação Anual de Informações Sociais)** para separar o **gap bruto** (diferença direta de médias/medianas) do **gap controlado** (diferença real associada ao gênero quando todas as demais variáveis observáveis são mantidas constantes).

---

## 🎯 O Problema e a Abordagem

As análises estatísticas tradicionais frequentemente subestimam a desigualdade salarial por conta do **paradoxo da composição**: como as mulheres na base formal possuem maior nível de escolaridade média que os homens (24,9% com superior completo contra 14,3%), a média/mediana bruta mascara parte da disparidade real de tratamento.

Para isolar o efeito estrito do gênero, o projeto adota uma metodologia em quatro etapas:
1. **Descrever:** Diagnóstico exploratório e tratamento de anomalias operacionais (como códigos administrativos disfarçados em jornadas de trabalho e tratamentos específicos para cargos de diretoria).
2. **Testar:** Testes não paramétricos de hipóteses ($Mann-Whitney\ U$ e $rbc$) na base completa ($6,05$ milhões de vínculos).
3. **Controlar:** Modelagem preditiva multivariada isolando escolaridade, tempo de empresa, idade, jornada, tipo de vínculo, ocupação (CBO a 2 dígitos), ano e UF.
4. **Medir:** Experimento contrafactual em massa ($20.000$ perfis do conjunto de teste) alternando exclusivamente a variável gênero.

---

## 📊 Principais Achados Técnicos

* **Gap Bruto vs. Gap Controlado:** Enquanto o gap bruto mediano da base é de **9,4%**, o experimento contrafactual controlado com o modelo campeão registra um gap mediano de **16,08%** desfavorável às mulheres (presente em 91,6% dos perfis avaliados).
* **Escolaridade:** A qualificação reduz o gap percentual, mas não o elimina (oscilando de 30,9% no nível Superior Completo a 11,7% no Doutorado).
* **Tecnologia da Informação (TI):** Em TI, o gap bruto é menor (2,1%), mas a presença feminina é extremamente reduzida (21,5% vs. 43,1% do mercado). Entre profissionais de TI com ensino superior ou mais, o gap salarial volta a subir para 9,1%.
* **Cargos de Diretoria (Vínculo 80):** Analisados à parte em remuneração mensal, apresentam uma sub-representação acentuada (25,2% de mulheres) e um gap mensal mediano de **71,8%**.

---

## 🛠️ Arquitetura e Modelagem

### Pipeline de Machine Learning
* **Base de Dados:** Microdados da RAIS via BigQuery / Base dos Dados (amostra de 0,5% congelada via `TABLESAMPLE SYSTEM`, cobrindo os anos de 2009 a 2025).
* **Variável Alvo:** $\ln(\text{salario\_hora})$ — reduzindo o skewness de 6,85 para 1,13 e penalizando erros em proporção relativa.
* **Modelo Campeão:** **Random Forest Regressor** (120 árvores, `max_depth`=18, `min_samples_leaf`=5) incorporado em um `Pipeline` com `ColumnTransformer` (`StandardScaler` + `OneHotEncoder`).
* **Métricas do Modelo no Teste:**
  * **MAE (log):** `0,3498` | **MAE (R$):** `R$ 6,03`
  * **$R^2$ (log):** `0,6803`
  * **Erro Percentual Mediano:** `25,4%`

---

## 📁 Estrutura do Repositório

```text
.
├── index.html                  # Interface web (Equipara) hospedada via GitHub Pages
├── README.md                             # Documentação principal do repositório
└── [branch Analise_de_Dados]
    └── GlassCeiling_vf.ipynb             # Notebook com EDA, modelagem de ML e experimento contrafactual
├── TCC_Grupo2_Documentacao.docx# Documentação técnica detalhada do projeto
├── TCC_Grupo2_Disparidade...pptx# Apresentação executiva de slides
└── README.md                   # Documentação principal do repositório
