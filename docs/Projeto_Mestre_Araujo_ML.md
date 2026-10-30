---
tags:
  - ibmec/pcalais
  - data-science/machine-learning
  - trabalho/drogaria-araujo
  - economia/gini
data: 2026-10-30
status: Em Progresso
integrantes:
  - Pedro Caram
  - Igor Blon
  - Dan Maia
  - Gabriel Henrique
---

# Projeto Mestre: Análise de Tickets Araujo & Case de ML

> [!ABSTRACT] Resumo do projeto
> Trabalho de Programação para Análise de Dados, com peso de 3/10 na AP2. Analisa **198.113 chamados criados em 2025 e presentes na extração de fechados em 2025**. Reúne análise descritiva e diagnóstica, aplicação do coeficiente de Gini à concentração da demanda, um experimento de previsão de atraso e o estudo de caso da Microsoft.
>
> **Situação:** notebook concluído e executado; preparação da apresentação em andamento. O complemento econômico usa um indicador descritivo de concentração. Não foi estimado um modelo econométrico causal.

**Entrega principal:** [Notebook completo e executado](../Analise_Tickets_Araujo.ipynb), com 52 células, incluindo 28 células de código executadas sem erro, 21 figuras e cinco treemaps interativos entre elas.

## 1. População, fontes e tratamentos

> [!IMPORTANT] Dois universos diferentes
> A extração contém **199.423 chamados fechados em 2025**. Destes, **198.113 foram criados em 2025**. As análises de abertura usam o segundo universo. Chamados criados em 2025 que continuaram abertos ao final do ano não aparecem nessa extração.

| Indicador | Extração completa | Criação em 2025, recorte adotado |
|---|---:|---:|
| Chamados únicos | 199.423 | **198.113** |
| Chamados com sentimento válido na pesquisa | 7.786 | **7.733** |
| Taxa de resposta por chamado | 3,90% | **3,90%** |
| Chamados com prazo de SLA preenchido | 97.010 | **96.251** |
| Cobertura de prazo preenchido | 48,65% | **48,58%** |
| Fechamentos em menos de 36 segundos | 76.835 | **76.835** |
| Participação dos fechamentos rápidos | 38,53% | **38,78%** |

| Arquivo | Tratamento aplicado |
|---|---|
| `Tickets TI Fechados_2025.xlsx` | Filtro pela coluna `Data de criação`; contagem de IDs únicos; duração calculada entre criação e fechamento; flag `Fechamento_Rapido` para menos de 36 segundos. |
| `Pesquisas de Satisfação_2025.xlsx` | Consolidação das 200.596 linhas em 199.423 IDs únicos. Há 1.173 linhas excedentes por ID. Sentimentos conflitantes seriam excluídos das taxas por sentimento; nenhum conflito foi encontrado. Indicadores textuais são consolidados por presença por chamado. |
| `SLA por Serviço.xlsx` | Cruzamento exato pela coluna `Serviço`, preservando todos os chamados. Conversão das durações para horas. `SLA_Mapeado` indica prazo preenchido; os demais recebem `Sem SLA Mapeado`. |
| `Anexo_Dicionario_Variaveis.docx` | Referência para significado, disponibilidade temporal e cuidados de privacidade das variáveis. As contagens efetivas vêm das planilhas. |

**Dados pessoais:** nomes, descrições livres dos problemas e notas de resolução não são carregados. Comentários da pesquisa são usados localmente para extrair temas de um vocabulário fechado e depois descartados. As saídas contêm agregações, sem reprodução de comentários ou registros individuais.

**Registros rápidos:** permanecem nas contagens de demanda e são separados na análise de duração e SLA. No ML, não são excluídos usando a duração final, pois essa informação só existe depois da abertura. São examinados como subgrupo na avaliação quando há observações elegíveis.

**Interpretação:** prazo ausente na tabela não prova ausência de contrato. Fechamento rápido pode refletir suporte pontual ou a maneira de registrar o atendimento, entre outras hipóteses. A baixa taxa de resposta não permite inferir o sentimento de quem não respondeu.

## 2. Questões obrigatórias e achados para a apresentação

| Questão | Entrega no notebook | Leitura principal |
|---|---|---|
| 1.1 | IDs únicos por data de criação | **198.113** chamados no recorte. |
| 1.2 | Evolução mensal; barras por dia da semana e hora, separando canais e sites | Setembro tem o maior volume, **20.918** chamados. A hora mais frequente começa às **10h**. Segunda-feira tem a maior média por dia de calendário. Um ano não comprova sazonalidade anual recorrente. |
| 1.3 | Cinco treemaps: LOJAS, ADM, CD, LAB e DIG | No CD, o tipo predominante é **Service Request**, com **4.993 chamados, 61,6%**. O serviço mais frequente é desbloqueio/alteração de senha via telefone, com **1.667**. Tipo e serviço são níveis diferentes. |
| 1.4 | Heatmap de dia da semana por hora | Concentração em dias úteis durante o dia; maior célula na **terça-feira às 10h**, com **3.128** aberturas no ano. |
| 1.5 | Barras empilhadas dos dez dias de maior demanda | Pico em **01/10/2025**, com **1.233** chamados. Dúvida/Apoio responde por **358**, ou **29,0%**, naquele dia. |
| 1.6 | Violinos por satisfação e canal, com e sem registros rápidos | Tempos típicos do Portal são maiores entre respondentes. A forma dos violinos depende da transformação do eixo e do tamanho da amostra; não demonstra, sozinha, maior variância na escala original. |
| 2.1 | Decomposição por serviço, site e canal; Gini e Lorenz | Média mensal de **15.100,8** em Jan/Jun para **20.875,5** em Set/Out: aumento de **5.774,7 chamados, 38,2%**. Dúvida/Apoio representa **55,1% da variação líquida** e Falha no Clique Retire, **20,1%**. |
| 2.2 | Satisfação por SLA, canal, duração e serviço; temas de comentários | No Portal com prazo conhecido e duração de pelo menos 36 segundos, insatisfação de **5,4% acima do prazo** contra **0,75% dentro dele**, entre respondentes. É associação, não efeito causal estimado. |

Nos comentários de insatisfeitos, o tema de problema mais frequente pelas regras é **problema não resolvido**, presente em **88 de 261 chamados com comentário, 33,7%**. Os temas podem se sobrepor e estão sujeitos a erros de contexto e negação.

O aumento de demanda é concentrado, mas isso não prova uma causa específica. Mudanças de sistema, de operação ou de registro são hipóteses a confrontar com informações da empresa.

## 3. Formulário matemático e conceitual

### Decomposição da variação de demanda

Para cada serviço $s$, a diferença entre as médias mensais é:

$$
\Delta_s = \frac{N_{s,\mathrm{set}} + N_{s,\mathrm{out}}}{2}
- \frac{1}{6}\sum_{m=1}^{6}N_{s,m}.
$$

Em que $N_{s,m}$ é o número de chamados criados no mês $m$ para o serviço $s$. Os meses sem chamados contam como zero. Janeiro a junho correspondem aos meses de 1 a 6.

$$
\sum_s\Delta_s = \overline{N}_{\mathrm{set/out}}-\overline{N}_{\mathrm{jan/jun}}.
$$

Essa identidade recompõe a variação total. Contribuições negativas representam redução. As decomposições por serviço, site e canal descrevem os mesmos chamados e **não devem ser somadas entre dimensões**.

### Coeficiente de Gini da concentração de demanda

Ordenando os volumes dos serviços de forma crescente, $0\leq x_{(1)}\leq\cdots\leq x_{(n)}$, com soma positiva:

$$
G = \frac{\sum_{i=1}^{n}(2i-n-1)x_{(i)}}{n\sum_{i=1}^{n}x_{(i)}}.
$$

O coeficiente mede concentração do volume entre serviços. Volumes iguais produzem $G=0$; o máximo desta versão empírica, sem correção de amostra finita, é $(n-1)/n$. A curva de Lorenz relaciona parcelas acumuladas de serviços e de chamados.

**Resultado anual:** $G\approx0{,}8909$. O cálculo usa **333 serviços e 193.622 chamados com serviço informado**; 4.491 chamados sem serviço ficam fora somente dessa análise. Os dez maiores serviços concentram **67,0%** desse volume. Nas comparações entre períodos, o universo de serviços é fixo e inclui zeros quando necessário.

Esse uso do Gini aplica uma medida de desigualdade à distribuição de demanda. Não mede desigualdade de renda, eficiência do suporte nem esforço de trabalho.

### Impureza de Gini da árvore

Se $p_t$ é a proporção de chamados com rótulo de atraso no nó $t$:

$$
I_{\mathrm{Gini}}(t)=1-p_t^2-(1-p_t)^2=2p_t(1-p_t).
$$

A impureza mede a mistura das classes. É zero em um nó com uma única classe e atinge $0{,}5$ quando as classes têm a mesma proporção. A árvore procura divisões que reduzam a média ponderada das impurezas dos nós filhos.

> [!WARNING] Três conceitos distintos
> O Gini econômico descreve concentração de volumes. A impureza de Gini orienta divisões de uma árvore de classificação. O treemap é uma visualização hierárquica e não é um modelo de machine learning.

## 4. Experimento de previsão de atraso

**Pergunta operacional aproximada:** usando somente informações da abertura, o chamado ultrapassará o prazo informado quando a duração for medida em horas corridas?

$$
y_i=\mathbb{1}(T_i>S_i),
$$

em que $T_i$ é o tempo entre criação e fechamento, em horas corridas, e $S_i$ é o prazo preenchido na tabela, em horas. A igualdade ao prazo conta como cumprimento.

- **População:** chamados do recorte com prazo preenchido e duração válida.
- **Entradas, com os nomes usados no notebook:** `Serviço`, `Canal`, `Hora`, `Dia_Semana` e `Site`.
- **Modelos:** árvore de classificação com impureza de Gini e KNN com 31 vizinhos, selecionado na validação.
- **Preparação:** categorias codificadas; hora e dia representados por ciclos e padronizados no KNN. Transformadores são ajustados somente no treino.
- **Desenvolvimento:** treino com aberturas de janeiro a agosto e fechamento anterior a setembro; validação com aberturas de setembro e outubro e fechamento anterior a novembro.
- **Treino final:** aberturas de janeiro a outubro, com fechamento anterior a novembro. **Teste:** aberturas de novembro e dezembro presentes na extração.
- **Seleção:** maior F1 na validação, com AUC-ROC como desempate. Limiar fixo de $0{,}5$, sem seleção pelo teste.

Fechamento, duração final, satisfação e comentários não entram como preditores. A planilha representa o estado final dos registros; seria necessário histórico para verificar se Serviço e Site foram alterados depois da abertura.

### Resultados e limites

| Modelo | Precision | Recall | AUC-ROC | Especificidade | Acurácia balanceada |
|---|---:|---:|---:|---:|---:|
| Referência simples | 0,996 | 1,000 | 0,500 | 0,000 | 0,500 |
| Árvore | 0,996 | 1,000 | 0,917 | 0,000 | 0,500 |
| KNN | 0,996 | 1,000 | 0,856 | 0,000 | 0,500 |

> [!WARNING] Métricas altas não demonstram uma classificação útil
> No teste há **17.034 chamados acima do prazo e 72 dentro**, totalizando 17.106. A classe de atraso representa **99,6%**. No limiar de $0{,}5$, os dois modelos e a referência simples classificam **todos como atrasados**.
>
> O recall é perfeito porque todos recebem alerta, enquanto a especificidade é zero. As AUCs indicam capacidade de ordenação das pontuações em relação aos rótulos observados, mas não validam o limiar nem a implantação. O subconjunto Phone tem apenas um caso dentro do prazo no teste, tornando sua avaliação particularmente frágil.

Reduzir falsos negativos, isoladamente, não resolve o problema: alertar para todos já elimina esses erros nesta amostra. Uma política operacional precisa considerar capacidade de atendimento, falsos alertas e custos de perder atrasos. Esses custos não foram definidos no trabalho.

**O rótulo é uma aproximação.** Não há calendário de horas úteis, histórico de pausas ou momento separado de resolução. Também faltam chamados ainda abertos ao final de 2025. O experimento não permite afirmar que 99,6% dos atendimentos descumpriram um SLA contratual.

## 5. Estudo de caso Microsoft

1. **Contexto:** suporte a clientes de produtos Microsoft, no qual a experiência de atendimento importa para o relacionamento com o cliente.
2. **Problema:** priorizar casos com risco de baixa satisfação, considerando mais informações do que regras manuais simples.
3. **Dados:** características do chamado, informações do cliente e produto, carga do atendente e respostas de pesquisas.
4. **Aplicação de ML:** modelo supervisionado para estimar risco de insatisfação. O artigo não identifica o algoritmo específico.
5. **Funcionamento:** pontuações apoiam a priorização de intervenções; avaliações das equipes alimentam melhorias do modelo.
6. **Resultados relatados:** AUC próxima de $0{,}80$ e melhoria aproximada de **10% nos resultados das pesquisas dos casos com intervenção**, na comparação apresentada. Isso não significa 80% de acurácia nem aumento de dez pontos percentuais; tampouco demonstra, por si só, um efeito causal. [Artigo original da Microsoft, Robsky e colaboradores, 2021](https://medium.com/data-science-at-microsoft/ml-and-customer-support-part-1-using-machine-learning-to-enable-world-class-customer-support-c90b3b02f6a3).

### Adaptação proposta para a Araujo

Esta é uma proposta do grupo, ainda não implementada:

1. **Validar os dados e o alvo:** revisar prazos, calendários, pausas, resolução versus encerramento e incluir chamados ainda abertos.
2. **Avaliar atraso prospectivamente:** registrar pontuações na abertura do Portal, em modo de observação, antes de mudar a fila.
3. **Definir uma política de intervenção:** escolher limiar e capacidade de atendimento com dados de desenvolvimento; avaliar em novos chamados, mantendo revisão humana.
4. **Estudar satisfação como problema separado:** um classificador de insatisfação exigiria outro alvo, avaliação do viés de resposta e dados adequados. O modelo atual de atraso não deve ser apresentado como preditor de insatisfação.

As AUCs do nosso experimento e da Microsoft não são diretamente comparáveis: problemas, rótulos e bases são diferentes. A adaptação aproveita a ideia de priorização baseada em risco, não um modelo pronto da Microsoft.

## 6. Divisão de responsabilidades para estudo e defesa

| Integrante | Conteúdo a dominar |
|---|---|
| **Pedro Caram** | Decomposição de Set/Out, interpretação econômica da concentração, fórmulas e diferença entre os dois Ginis. |
| **Igor Blon** | Leitura e tratamento dos dados, deduplicação, cruzamento exato de SLA, diferenças entre populações e prevenção de uso de informação futura. |
| **Dan Maia** | Séries temporais, heatmap, treemaps, violinos e limites das interpretações visuais. |
| **Gabriel Henrique** | Associação com insatisfação, análise textual, estudo Microsoft e proposta de adaptação. |

**Todos devem conseguir explicar:** por que foram usados 198.113 chamados; por que ausência de SLA não equivale a cumprimento; como foi feita a divisão temporal; e por que recall de 100% não torna o classificador adequado neste experimento.

## 7. Preparação da apresentação

- [x] Executar e verificar o notebook completo.
- [x] Reconciliar as contagens e documentar as limitações.
- [ ] Selecionar os gráficos que cabem em cinco minutos; manter os demais como apoio.
- [ ] Definir quem apresenta os modelos e quem encerra a apresentação.
- [ ] Ensaiar as transições e conferir o tempo total.

**Mensagem central sugerida:** a demanda é concentrada e o aumento pode ser localizado em serviços específicos. Os dados também mostram associações com insatisfação, mas a previsão operacional exige um alvo de SLA confiável e uma política de alertas validada.

## 8. Links internos sugeridos

Os links abaixo são sugestões de organização para futuras notas; esses arquivos complementares não fazem parte desta entrega:

- [[Metodologia_Gini_Vs_Impureza]]
- [[Roteiro_Apresentacao_AP2]]
- [[Analise_SLA_Araujo]]

**Rastreabilidade:** resultados numéricos conferidos nas saídas do notebook. O notebook registra nomes, abas e hashes das três planilhas e contém as fontes conceituais. Esta nota não altera os dados nem substitui as instruções do professor.
