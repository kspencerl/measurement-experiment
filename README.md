## Plano de Experimento – Scoping e Planejamento

### 1. Identificação básica

#### 1.1 Título do experimento

**Relação entre Cobertura de Código e Efetividade de Testes: Um Estudo Correlacional com Mutation Testing em Projetos Java**

#### 1.2 ID / código

plf-es-2025-2-tcci-0393100-pes-kimberly-lourenco

#### 1.3 Versão do documento e histórico de revisão

- **Versão atual:** v1.0
- **Histórico de revisões:**
  - **v1.0 (23/11/2025):** inclusão da identificação básica, contexto e problema do projeto de pesquisa.

#### 1.4 Datas (criação, última atualização)

- **Data de criação:** 23/11/2025
- **Última atualização:** 23/11/2025

#### 1.5 Autores (nome, área, contato)

- **Autora:** Kimberly Liz Spencer Lourenço
  - Área: Bacharelado em Engenharia de Software
  - Instituição: PUC Minas – Instituto de Informática e Ciências Exatas
  - E-mail: kimberly.liz@sga.pucminas.br

#### 1.6 Responsável principal (PI / dono do experimento)

- **Responsável principal (PI):** Kimberly Liz Spencer Lourenço

#### 1.7 Projeto / produto / iniciativa relacionada

- **Projeto:** Trabalho de Conclusão de Curso (TCC) em Engenharia de Software – PUC Minas
- **Tema:** Relação entre cobertura de código e efetividade de testes em projetos Java de código aberto
- **Produto esperado:**
  - Artigo científico no formato SBC;
  - Pipeline reproduzível de coleta e análise;
  - Base de dados consolidada de cobertura e _mutation score_ por projeto/módulo.
---

### 2. Contexto e problema

#### 2.1 Descrição do problema / oportunidade

Cobertura de código é amplamente utilizada como indicador de qualidade de testes, pois mostra quais linhas de código de produção são exercitadas. Porém, a cobertura não mede diretamente se os testes detectariam defeitos: é possível ter alta cobertura e, ainda assim, falhas passarem silenciosamente, especialmente em contextos críticos (pagamentos, prontuários, documentos fiscais).

Oportunidade: investigar empiricamente **em que medida a cobertura de código por linha reflete a efetividade dos testes**, aproximada neste estudo pelo _mutation score_ (proporção de mutantes mortos). Isso permite avaliar quando a cobertura pode ser usada como _proxy_ razoável de efetividade e quando ela é um indicador fraco ou enganoso.

#### 2.2 Contexto organizacional e técnico

- **Organizacional:**

  - Estudo conduzido em contexto acadêmico (TCC de Engenharia de Software – PUC Minas).
  - Base empírica formada por projetos Java de código aberto hospedados no GitHub.
  - Não há intervenção direta em times de desenvolvimento; o estudo é observacional sobre repositórios existentes.

- **Técnico:**
  - Linguagem principal: **Java**.
  - _Build tools_: **Maven** e **Gradle**, conforme cada repositório.
  - Ferramenta de cobertura: **JaCoCo** (cobertura por linha, relatórios XML/HTML).
  - Ferramenta de _mutation testing_: **PIT (pitest-maven-plugin)**, com operadores padrão.
  - Ambiente de execução: máquina/VM Linux com versões documentadas de JDK, Maven/Gradle, JaCoCo e PIT.
  - Análise de dados: scripts **Python** (pandas, scipy/statsmodels) para consolidar métricas em CSV e executar análises estatísticas.
  - Processo de coleta: pipeline automatizado (ex: _Makefile_ + _shell scripts_) contendo descoberta de repositórios, _clone_, execução de testes, coleta de cobertura, execução de mutação e parsing de relatórios.

#### 2.3 Trabalhos e evidências prévias (internos e externos)

Diversos estudos recentes dão suporte direto ao problema investigado neste trabalho, tanto ao questionar o uso isolado da cobertura de código como métrica de qualidade quanto ao consolidar o _mutation score_ como proxy de efetividade de testes.

- **Ivanković et al. (2024)** – mostram que apresentar apenas linhas não cobertas tende a saturar os autores mostram que simplesmente apresentar uma lista extensa de linhas não cobertas tende a sobrecarregar os desenvolvedores e nem sempre orienta ações concretas de melhoria. A abordagem _Productive Coverage_ busca tornar a cobertura mais útil ao priorizar trechos relevantes do código com base em sinais de uso em produção e similaridade com código bem testado. Esse resultado reforça que a cobertura “bruta” (apenas o percentual global ou o conjunto de linhas não cobertas) não é, sozinha, um bom guia de qualidade de testes.

- **Petrović et al. (2023)** – o estudo acompanha mutantes exibidos durante _code review_ e analisa o que acontece com eles ao longo de diversos _pull requests_. Em muitos casos, esses mutantes motivam mudanças concretas na base de código, como a inclusão de novos testes, refatorações e ajustes na lógica de produção. Esses achados sustentam a visão de que mutantes bem selecionados são capazes de induzir melhorias reais em código e testes, fortalecendo o uso de _mutation testing_ como sinal direto do poder de detecção da suíte de testes.

- **Straubinger et al. (2024)** – investigam mutantes semanticamente equivalentes, isto é, mutantes em que a alteração introduzida não muda o comportamento observável do programa em relação ao código original. Nesses casos, não há um “defeito real” a ser detectado: mesmo uma suíte de testes altamente efetiva nunca conseguirá matar esses mutantes. No entanto, no cálculo do _mutation score_, esses mutantes equivalentes são contabilizados no denominador (total de mutantes) e, por nunca serem mortos, reduzem artificialmente o valor da métrica, como se representassem defeitos não detectados. Assim, a presença de mutantes equivalentes não é um problema para o funcionamento do sistema em si, mas para a precisão da métrica de mutação, pois pode fazer a suíte de testes parecer menos efetiva do que realmente é. Esse efeito introduz ruído nas análises que relacionam cobertura e _mutation score_ e precisa ser considerado como ameaça à validade ao interpretar os resultados deste estudo.

- **Ashong et al. (2025)** – este trabalho compara diferentes estratégias de seleção de mutantes, incluindo amostragem aleatória e abordagens estratificadas por arquivo ou por operador, com o objetivo de aproximar o _mutation score_ obtido por mutação completa. Os resultados indicam que não há uma técnica universalmente superior em todos os cenários, mas que estratégias estratificadas tendem, com frequência maior, a produzir estimativas mais próximas do _mutation score_ real, especialmente quando a suíte de testes é razoavelmente robusta. Isso é particularmente relevante quando o consumo de recursos computacionais ultrapassaria o que é aceitável para o projeto.

- **Veloso & Hora (2022)** – os autores caracterizam métodos de teste de alta qualidade em projetos Java por meio de _mutation testing_ em nível de método. Eles observam que métricas simples, como tamanho dos métodos de teste ou número de _asserts_, não diferenciam adequadamente métodos de alta e baixa qualidade. Em contraste, a menor incidência de _test smells_ críticos aparece como um indicador mais consistente de qualidade. Esses resultados sugerem que métricas agregadas, incluindo cobertura global, podem mascarar variações locais de qualidade, enquanto o _mutation score_ e a análise de _test smells_ capturam melhor a efetividade real dos testes.

- **Teixeira et al. (2024)** – o trabalho apresenta a ferramenta METEOR, que utiliza _mutation testing_ (baseado em PIT) para monitorar a preservação de comportamento em refatorações de código de teste. A ferramenta compara o _mutation score_ antes e depois das refatorações, sinalizando potenciais perdas de poder de detecção. Esse estudo reforça o _mutation score_ como uma métrica prática e sensível para avaliar a efetividade dos testes em cenários reais de evolução de código.

#### 2.4 Referencial teórico e empírico essencial

O referencial deste estudo se organiza em quatro eixos principais: (i) cobertura de código e suas limitações como métrica de qualidade, (ii) _mutation testing_ e _mutation score_ como aproximação da efetividade dos testes, (iii) qualidade de testes e _test smells_, e (iv) métodos estatísticos para estudos correlacionais em Engenharia de Software.

**Cobertura de código.** Cobertura de código é uma métrica estrutural que indica qual fração do código de produção é exercitada pelos testes automatizados. Critérios como cobertura por linha, por ramo e por instrução são amplamente monitorados em pipelines de integração contínua e em políticas de qualidade, sobretudo em contextos industriais e _safety-critical_. No entanto, a cobertura registra apenas a execução de trechos de código, não garantindo que comportamentos relevantes sejam devidamente verificados por _asserts_ ou oráculos adequados. Trabalhos como Ivanković et al. reforçam que exibir simplesmente “linhas não cobertas” tende a saturar os desenvolvedores e não necessariamente direciona para ações que aumentem o poder de detecção de defeitos, sugerindo que a cobertura, isoladamente, é um indicador limitado da efetividade dos testes.

**Teste de mutação e _mutation score_.** Teste de mutação é uma técnica que avalia a qualidade da suíte de testes ao introduzir pequenas alterações sintáticas no código de produção (mutantes) e verificar se os testes conseguem “matar” esses mutantes, isto é, produzir falhas observáveis. O _mutation score_ é definido como a proporção de mutantes mortos em relação ao total de mutantes válidos. Essa métrica é amplamente utilizada como aproximação da efetividade dos testes, pois mede explicitamente se a suíte distingue entre o comportamento original e versões levemente alteradas do código. Estudos recentes com ferramentas como PIT e METEOR mostram que o _mutation score_ é sensível a mudanças na qualidade dos testes e pode indicar perda de poder de detecção após refatorações. Ao mesmo tempo, questões como mutantes semanticamente equivalentes e custo computacional exigem cuidado: identificação imperfeita de equivalentes pode distorcer o denominador do _mutation score_, e a execução de mutantes em larga escala demanda estratégias como amostragem.

**Qualidade de testes, _test smells_ e relação cobertura vs efetividade.** A literatura aponta que a qualidade de uma suíte de testes não pode ser reduzida a métricas simples como contagem de testes ou cobertura. Trabalhos como o de Veloso e Hora mostram que métodos de teste de alta qualidade tendem a apresentar menos _test smells_ críticos, mesmo quando tamanho e número de _asserts_ são similares aos de baixa qualidade. Além disso, estudos que acompanham mutantes durante _code review_ indicam que mutantes acionáveis levam a melhorias concretas no código e nos testes. Esses resultados convergem para a visão de que cobertura e _mutation score_ capturam dimensões diferentes: a primeira foca alcance estrutural, enquanto a segunda foca capacidade efetiva de detecção. Compreender a relação entre essas métricas em múltiplos projetos é essencial para orientar práticas de QA e decisões sobre quando a cobertura pode servir (ou não) como _proxy_ razoável de efetividade.

**Métodos estatísticos em estudos correlacionais.** Do ponto de vista metodológico, este estudo adota técnicas estatísticas típicas de investigações correlacionais em Engenharia de Software. Serão utilizados coeficientes de correlação (em especial o de Spearman, adequado para relações monotônicas possivelmente não lineares e menos sensível a _outliers_), modelos de regressão linear simples e análise de quadrantes de discrepância (por exemplo, casos de alta cobertura e baixo _mutation score_ e o inverso). Além disso, empregaremos técnicas de reamostragem do tipo _bootstrap_, nas quais são geradas múltiplas “amostras artificiais” a partir dos próprios dados, por sorteio com reposição, para estimar a distribuição das estatísticas e construir intervalos de confiança sem depender de suposições fortes de normalidade. Em conjunto, esses métodos permitem quantificar a associação entre cobertura por linha e _mutation score_, avaliar em que medida a cobertura explica a variabilidade observada e caracterizar padrões em subgrupos de projetos. A interpretação dos resultados considerará explicitamente ameaças à validade interna (influência de fatores não controlados), à validade de construto (quão bem as métricas de cobertura e *mutation score* realmente representam os conceitos de “alcance” e “efetividade” dos testes que este estudo pretende analisar), à validade de conclusão (limitações dos testes estatísticos utilizados) e à validade externa (possibilidade de generalizar os achados para outros contextos), reconhecendo que se trata de um estudo observacional com projetos de código aberto.

---

### 3. Objetivos e questões (Goal / Question / Metric)

#### 3.1 Objetivo geral

> **Analisar**, do ponto de vista de uma pesquisadora em Engenharia de Software, **projetos Java de código aberto** com testes automatizados, com o propósito de **investigar a relação entre cobertura de código por linha e *mutation score***, no contexto de um estudo quantitativo correlacional, a fim de avaliar em que medida a cobertura pode ser usada como *proxy* da efetividade dos testes.

#### 3.2 Objetivos específicos

- **O1:** Estimar a associação entre cobertura de código por linha e *mutation score* por projeto/módulo e verificar sua significância estatística.
- **O2:** Identificar e caracterizar casos em que há:
  - alta cobertura e baixo *mutation score*; e  
  - baixa/média cobertura e *mutation score* relativamente alto,  
  descrevendo padrões recorrentes em código e testes nesses cenários.
- **O3:** Ajustar modelos de regressão linear simples do *mutation score* em função da cobertura de código por linha e avaliar o quanto a cobertura explica a variabilidade do *mutation score*.
- **O4:** Compreender em que condições práticas a cobertura pode (ou não) ser usada como *proxy* da efetividade dos testes, combinando evidências de correlação, regressão e discrepâncias.


### 3.3 GQM (Goal — Question — Metric)

| Objetivo | Pergunta | Métricas |
|---|---|---|
| **O1: Associação cobertura–efetividade**<br>Investigar a relação entre cobertura de código por linha e *mutation score* em projetos Java de código aberto. | **Q1.1:** Qual é a força e a direção da associação entre cobertura de código por linha e *mutation score* em projetos Java de código aberto? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M3: Correlação de Spearman (ρ);<br>M4: Correlação de Pearson (r), quando aplicável |
| **O1: Associação cobertura–efetividade** | **Q1.2:** Essa associação se mantém estável quando removemos projetos extremos (por exemplo, *outliers* em cobertura ou *mutation score*)? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M3: Correlação de Spearman (ρ) com e sem *outliers* |
| **O1: Associação cobertura–efetividade** | **Q1.3:** A força da associação entre cobertura e *mutation score* varia de forma relevante entre faixas de tamanho de projeto ou de tamanho da suíte de testes? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M3: Correlação de Spearman (ρ) por estratos;<br>M7: LOC aproximado;<br>M8: Nº de testes automatizados |
| **O2: Casos de discrepância**<br>Identificar e caracterizar projetos em que cobertura e *mutation score* divergem de forma relevante. | **Q2.1:** Com que frequência surgem projetos com alta cobertura e baixo *mutation score* e o inverso (baixa/média cobertura e *mutation score* alto)? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M5: Quadrantes de discrepância (proporção por quadrante) |
| **O2: Casos de discrepância** | **Q2.2:** Que características de contexto (tamanho, número de testes, idade, operadores de mutação) aparecem com mais frequência nos projetos discrepantes? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M5: Quadrantes de discrepância;<br>M7: LOC aproximado;<br>M8: Nº de testes automatizados;<br>M9: Idade/atividade do projeto;<br>M10: Distribuição de operadores de mutação |
| **O2: Casos de discrepância** | **Q2.3:** Como se comportam exemplos representativos de projetos discrepantes em termos de estrutura de testes (por exemplo, presença de *test smells* ou padrões de *asserts*)? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M5: Quadrantes de discrepância;<br>M8: Nº de testes automatizados;<br>M10: Distribuição de operadores de mutação (por arquivo/classe selecionados) |
| **O3: Capacidade preditiva da cobertura**<br>Ajustar modelos de regressão linear simples do mutation score em função da cobertura de código por linha e avaliar o quanto a cobertura consegue explicar ou prever a variação observada no *mutation score*. | **Q3.1:** Em que medida a cobertura de código por linha explica a variabilidade do *mutation score* quando usamos modelos de regressão linear simples? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M6: Parâmetros de regressão (coeficiente angular, intercepto, R² ajustado, RMSE, MAE) |
| **O3: Capacidade preditiva da cobertura** | **Q3.2:** Como se comportam os erros de predição do modelo (por exemplo, RMSE e MAE) em diferentes faixas de cobertura (baixa, média, alta)? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M6: Parâmetros de regressão (R² ajustado, RMSE, MAE por faixa de cobertura) |
| **O3: Capacidade preditiva da cobertura** | **Q3.3:** Há relação sistemática entre os resíduos do modelo (diferença entre *mutation score* observado e previsto) e variáveis de contexto como tamanho do projeto, número de testes ou idade? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M6: Resíduos do modelo;<br>M7: LOC aproximado;<br>M8: Nº de testes automatizados;<br>M9: Idade/atividade do projeto |
| **O4: Uso da cobertura como *proxy***<br>Compreender em que condições a cobertura pode ser usada como *proxy* da efetividade dos testes. | **Q4.1:** Em quais condições práticas (faixas de cobertura, faixas de *mutation score*, contexto dos projetos) a cobertura parece funcionar como *proxy* razoável da efetividade dos testes? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M3: Correlação de Spearman (ρ);<br>M5: Quadrantes de discrepância;<br>M6: Parâmetros de regressão;<br>M7–M10: Variáveis de apoio (LOC, nº de testes, idade do projeto, operadores de mutação) |
| **O4: Uso da cobertura como *proxy*** | **Q4.2:** Em quais condições a cobertura se mostra um indicador limitado (por exemplo, alta cobertura com *mutation score* baixo) e que fatores adicionais precisam ser considerados na interpretação? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M5: Quadrantes de discrepância;<br>M7–M10: Variáveis de apoio (LOC, nº de testes, idade, operadores de mutação) |
| **O4: Uso da cobertura como *proxy*** | **Q4.3:** Que recomendações práticas podem ser derivadas para o uso combinado de cobertura e *mutation score* em políticas de qualidade (por exemplo, definição de metas, uso amostral de mutação)? | M1: Cobertura de código por linha (%);<br>M2: *Mutation score* (%);<br>M3: Correlação de Spearman (ρ);<br>M5: Quadrantes de discrepância;<br>M6: Parâmetros de regressão |

---

### 3.4 Tabela de métricas (descrição e unidade)

| Métrica | Descrição | Unidade |
|---|---|---|
| **M1 – Cobertura de código por linha** | Percentual de linhas de código de produção exercitadas pela suíte de testes automatizados, conforme relatórios JaCoCo gerados após a execução dos testes. | porcentagem (%) |
| **M2 – *Mutation score* global** | Percentual de mutantes mortos em relação ao total de mutantes válidos gerados pelo PIT para o projeto/módulo analisado. | porcentagem (%) |
| **M3 – Correlação de Spearman (ρ)** | Coeficiente de correlação de Spearman entre a cobertura de código por linha (M1) e o *mutation score* (M2), capturando associações monotônicas (não necessariamente lineares). | valor adimensional (−1 a 1) |
| **M4 – Correlação de Pearson (r)** | Coeficiente de correlação de Pearson entre M1 e M2, utilizado quando os pressupostos de linearidade e distribuição aproximadamente normal dos resíduos forem razoáveis. | valor adimensional (−1 a 1) |
| **M5 – Quadrantes de discrepância** | Classificação dos projetos em quadrantes, por exemplo: QH-L (cobertura ≥ 80% e *mutation score* < 60%) e QL-H (cobertura < 60% e *mutation score* ≥ 80%), e cálculo da proporção de projetos em cada quadrante. | porcentagem (%) de projetos por quadrante |
| **M6 – Parâmetros de regressão e ajuste** | Conjunto de estatísticas do modelo de regressão linear simples do *mutation score* (M2) em função da cobertura (M1): coeficiente angular, intercepto, R² ajustado, erro quadrático médio (RMSE) e erro absoluto médio (MAE). | diversos (coeficientes adimensionais; R² adimensional; RMSE e MAE em pontos de *mutation score* (%)) |
| **M7 – LOC (Lines of Code)** | Número aproximado de linhas de código de produção do projeto/módulo (métrica de tamanho utilizada como variável de contexto). | contagem (linhas) |
| **M8 – Número de testes automatizados** | Quantidade de casos de teste automatizados presentes no projeto/módulo (por exemplo, métodos de teste JUnit). | contagem |
| **M9 – Idade / atividade do projeto** | Indicador de maturidade/atividade do projeto, como tempo desde o primeiro *commit* ou data do último *commit* relevante dentro da janela analisada. | tempo (anos/meses) ou marca temporal |
| **M10 – Distribuição de operadores de mutação** | Proporção de mutantes gerados por cada tipo de operador de mutação (por exemplo, operadores aritméticos, relacionais, lógicos), usada para contextualizar o *mutation score* obtido. | porcentagem (%) por tipo de operador |

---

### 4. Escopo e contexto do experimento

#### 4.1 Escopo funcional / de processo (incluído e excluído)
- **Incluído:**
  - Projetos Java de código aberto hospedados no GitHub com:
    - configuração de build via Maven ou Gradle;
    - presença clara de testes automatizados (dependências JUnit etc.);
    - *build* e execução de testes bem-sucedidos em ambiente padronizado.
  - Coleta de cobertura de código por linha com JaCoCo.
  - Execução de *mutation testing* com PIT em nível de projeto/módulo.
  - Consolidação de métricas (cobertura, *mutation score*, variáveis de apoio) em CSV.
  - Análises estatísticas (correlação, regressão, quadrantes de discrepância).

- **Excluído:**
  - Projetos que não compilam ou não executam testes de forma determinística no ambiente definido.
  - Outras linguagens (Kotlin, Scala, C#, etc.).
  - Métricas de cobertura que não sejam cobertura por linha (e.g., branch, instruction, path), salvo uso auxiliar.
  - Testes manuais, testes exploratórios, testes não orquestrados via Maven/Gradle.
  - Intervenções diretas no processo de desenvolvimento de equipes (o estudo não altera políticas de CI/CD dos projetos).

#### 4.2 Contexto do estudo (tipo de organização, projeto, experiência)
- **Tipo de organização:** contexto acadêmico (TCC) analisando projetos de código aberto mantidos por comunidades diversas (empresas, indivíduos, fundações).
- **Tipo de projetos:** bibliotecas, frameworks e aplicações Java com base de usuários real; variando em tamanho, idade e popularidade.
- **Criticidade:** heterogênea – alguns projetos podem ser usados em contextos críticos, outros em aplicações comuns.
- **Perfil de experiência dos “participantes”:** não há participação direta de indivíduos; a “unidade de análise” é o projeto/módulo de software. Assume-se que projetos com maior popularidade tendem a ter mantenedores experientes, mas isso não é controlado.

#### 4.3 Premissas
- Os projetos selecionados conseguem ser *buildados* e ter seus testes executados com configurações razoavelmente padronizadas (JDK compatível, versões de Maven/Gradle).
- Os relatórios de JaCoCo e PIT são gerados de forma consistente e confiável para cada projeto.
- Os testes automatizados são determinísticos o suficiente para que múltiplas execuções não gerem variações significativas nas métricas.
- A API do GitHub permanece disponível e utilizável com limites de *rate* que permitem coletar o corpus planejado.
- O ambiente de execução (VM/máquina) dispõe de recursos suficientes (CPU/RAM/tempo) para executar testes e mutação nos projetos selecionados.

#### 4.4 Restrições
- **Tempo:** o estudo precisa ser executado dentro da janela do TCC II (fev–jun/2026), limitando tamanho do corpus e profundidade das análises.
- **Recursos computacionais:** execução de PIT é custosa; isso restringe o número de projetos/módulos e a granularidade (ex.: rodar por módulo em vez de por classe).
- **Ferramentas:** uso de JaCoCo e PIT, e versões específicas de JDK, Maven/Gradle, que podem introduzir incompatibilidades com alguns projetos mais antigos ou muito recentes.
- **Acessos:** dependência da disponibilidade pública dos repositórios e da integridade do histórico de *build* (por exemplo, dependências externas que podem ficar indisponíveis).
- **Escopo acadêmico:** não há acesso a métricas internas de empresas; a análise se limita ao que é observável em projetos de código aberto.

#### 4.5 Limitações previstas
- **Validade externa:** resultados são mais diretamente válidos para projetos Java de código aberto, com certas características de popularidade/atividade, não necessariamente para sistemas proprietários ou de outros domínios tecnologicamente específicos.
- **Amostra não aleatória:** a seleção de projetos com base em filtros de linguagem, popularidade e atividade recente faz com que a amostra tenda a incluir principalmente projetos mais maduros e bem mantidos. Isso pode introduzir viés de seleção, pois os resultados obtidos podem refletir melhor o comportamento desse tipo de projeto do que o de projetos menos populares, desatualizados ou com práticas de teste menos consolidadas.

- **Medição imperfeita:** presença de mutantes equivalentes e possíveis falhas de cobertura/relatórios podem distorcer parcialmente o *mutation score* e a cobertura medida.
- **Caráter observacional:** não há controle experimental de fatores como experiência das equipes, políticas de qualidade ou domínio de aplicação; isso limita inferências causais.

---

### 5. Stakeholders e impacto esperado

#### 5.1 Stakeholders principais
- Autora do TCC (pesquisadora).
- Orientadores e banca avaliadora do TCC.
- Comunidade acadêmica de Engenharia de Software (pesquisadores em teste de software, métricas e qualidade).
- Profissionais de qualidade e testes de software.
- Desenvolvedores e mantenedores de projetos Java de código aberto.
- Equipes que utilizam cobertura em pipelines de CI/CD.

#### 5.2 Interesses e expectativas dos stakeholders
- **Autora e orientadores:**  
  - Obter evidências empíricas sólidas para responder às questões de pesquisa.  
  - Entregar um TCC tecnicamente robusto, reprodutível e alinhado à literatura recente.  
- **Banca e comunidade acadêmica:**  
  - Avaliar a qualidade metodológica do estudo.  
  - Utilizar o protocolo e a base de dados como referência para pesquisas futuras em métricas de teste.
- **Profissionais de QA e desenvolvimento:**  
    - Subsidiar a definição e a revisão de políticas que usam cobertura como KPI (*Key Performance Indicator*), apresentando em quais contextos esse indicador é confiável e em quais requer cautela.  
    - Apoiar a priorização de esforços de teste, mostrando quando aumentar apenas a cobertura tende a ter baixo retorno em detecção de defeitos e quando a combinação com métricas de mutação é mais apropriada.  
    - Oferecer orientação básica para interpretar, em conjunto, relatórios de cobertura e *mutation score* em pipelines de CI/CD.
- **Mantenedores de projetos *OSS (Open Source Software)*:**  
    - Usar os resultados como insumo para revisar metas de cobertura e práticas de teste em seus repositórios.  
    - Avaliar se a adoção, mesmo que pontual ou amostral, de *mutation testing* faz sentido em projetos mais críticos ou populares.

#### 5.3 Impactos potenciais no processo / produto
- **Nos projetos OSS analisados:**  
  - Impacto direto mínimo, pois o estudo não altera código nem testes dos repositórios; apenas executa *builds* e ferramentas de análise em clones locais.  
- **Na prática de QA:**  
  - Possível revisão de políticas que tratam cobertura como meta absoluta.  
  - Estímulo à combinação de métricas (cobertura + *mutation score* ou outras evidências) em vez de foco exclusivo em cobertura.
- **Na formação acadêmica:**  
  - Disponibilização de um estudo de caso reprodutível para ensino de métricas de teste, correlação e ameaças à validade.

---

### 6. Riscos de alto nível, premissas e critérios de sucesso

#### 6.1 Riscos de alto nível (negócio, técnicos, etc.)
- **R1 – Baixo número de projetos válidos:** muitos repositórios podem falhar em *build/test*, reduzindo o tamanho efetivo da amostra.
- **R2 – Custo computacional da mutação:** PIT pode tornar inviável rodar mutação completa para todos os projetos dentro do tempo e recursos disponíveis.
- **R3 – Flakiness de testes:** existência de testes instáveis (que às vezes passam e às vezes falham sem mudança de código), o que pode gerar métricas inconsistentes de cobertura e *mutation score*.
- **R4 – Problemas de compatibilidade:** diferenças de versão de JDK, Maven/Gradle, dependências externas quebradas.
- **R5 – Erros na automação:** bugs nos scripts de coleta/análise podem introduzir erros silenciosos nas métricas.

#### 6.2 Critérios de sucesso globais (go / no-go)
O experimento será considerado bem-sucedido se:
- **CS1:** For obtida uma amostra mínima de projetos/módulos com métricas válidas (por exemplo, ≥ 30–40 unidades de análise com cobertura e *mutation score* confiáveis).
- **CS2:** As análises forem suficientes para responder Q1–Q3 com resultados estatisticamente interpretáveis (correlações com intervalos de confiança, regressão com diagnóstico básico).
- **CS3:** O pipeline de coleta e análise estiver documentado e reprodutível (scripts + *Makefile* + documentação de ambiente).
- **CS4:** As conclusões estiverem claramente conectadas às evidências, com discussão explícita de ameaças à validade.

#### 6.3 Critérios de parada antecipada (pré-execução)
O experimento poderá ser cancelado ou replanejado antes da execução completa se:
- **CP1:** Após uma fase piloto, menos de um número mínimo de projetos (por exemplo, 10) conseguirem gerar métricas válidas, mesmo após ajustes de ambiente razoáveis.
- **CP2:** O custo de mutação se mostrar impraticável (por exemplo, tempo total estimado incompatível com o cronograma do TCC), sem possibilidade de mitigação via amostragem estratificada.
- **CP3:** Problemas técnicos graves e persistentes com ferramentas (JaCoCo, PIT, ambiente) inviabilizarem a medição confiável.
- **CP4:** Mudanças significativas no escopo do TCC (por decisão acadêmica) tornarem o experimento incoerente com os novos objetivos.

---

### 7. Modelo conceitual e hipóteses

#### 7.1 Modelo conceitual do experimento

Explique, em texto ou esquema, como você acredita que os fatores influenciam as respostas (por exemplo, “técnica A reduz defeitos em relação a B”).

#### 7.2 Hipóteses formais (H0, H1)

Formule explicitamente as hipóteses nulas e alternativas para cada questão principal, incluindo a direção esperada do efeito quando fizer sentido.

#### 7.3 Nível de significância e considerações de poder

Defina o nível de significância (por exemplo, α = 0,05) e comente o que se espera em termos de poder estatístico, relacionando-o ao tamanho de amostra planejado.

---

### 8. Variáveis, fatores, tratamentos e objetos de estudo

#### 8.1 Objetos de estudo

Descreva o que será efetivamente manipulado ou analisado (módulos de código, requisitos, tarefas, casos de teste, issues, etc.).

#### 8.2 Sujeitos / participantes (visão geral)

Caracterize em alto nível quem serão os participantes (desenvolvedores, testadores, estudantes, etc.), sem ainda entrar em detalhes de seleção.

#### 8.3 Variáveis independentes (fatores) e seus níveis

Liste os fatores que serão manipulados (por exemplo, técnica, ferramenta, processo) e indique os níveis de cada um (A/B, X/Y, alto/baixo).

#### 8.4 Tratamentos (condições experimentais)

Descreva claramente cada condição de experimento (grupo controle, tratamento 1, tratamento 2, etc.) e o que distingue uma da outra.

#### 8.5 Variáveis dependentes (respostas)

Informe as medidas de resultado que você observará (por exemplo, número de defeitos, esforço em horas, tempo de conclusão, satisfação).

#### 8.6 Variáveis de controle / bloqueio

Liste fatores que você não está estudando diretamente, mas que serão mantidos constantes ou usados para formar blocos (por exemplo, experiência, tipo de tarefa).

#### 8.7 Possíveis variáveis de confusão conhecidas

Identifique fatores que podem distorcer os resultados (como diferenças de contexto, motivação ou carga de trabalho) e que você pretende monitorar.

---

### 9. Desenho experimental

#### 9.1 Tipo de desenho (completamente randomizado, blocos, fatorial, etc.)

Indique qual tipo de desenho será utilizado e justifique brevemente por que ele é adequado ao problema e às restrições.

#### 9.2 Randomização e alocação

Explique o que será randomizado (sujeitos, tarefas, ordem de tratamentos) e como a randomização será feita na prática (ferramentas, procedimentos).

#### 9.3 Balanceamento e contrabalanço

Descreva como você garantirá que os grupos fiquem comparáveis (balanceamento) e como lidará com efeitos de ordem ou aprendizagem (contrabalanço).

#### 9.4 Número de grupos e sessões

Informe quantos grupos existirão e quantas sessões ou rodadas cada sujeito ou grupo irá executar, com uma breve justificativa.

---

### 10. População, sujeitos e amostragem

#### 10.1 População-alvo

Descreva qual é a população real que você deseja representar com o experimento (por exemplo, “desenvolvedores Java de times de produto web”).

#### 10.2 Critérios de inclusão de sujeitos

Especifique os requisitos mínimos para um participante ser elegível (experiência, conhecimento, papel, disponibilidade, etc.).

#### 10.3 Critérios de exclusão de sujeitos

Liste condições que impedem participação (conflitos de interesse, falta de skills essenciais, restrições legais ou éticas).

#### 10.4 Tamanho da amostra planejado (por grupo)

Defina quantos participantes você pretende ter no total e em cada grupo, relacionando a decisão com poder, recursos e contexto.

#### 10.5 Método de seleção / recrutamento

Explique como os participantes serão escolhidos (amostra de conveniência, sorteio, convite aberto, turma de disciplina, time específico).

#### 10.6 Treinamento e preparação dos sujeitos

Descreva qual treinamento ou material preparatório será fornecido para nivelar entendimento e reduzir vieses por falta de conhecimento.

---

### 11. Instrumentação e protocolo operacional

#### 11.1 Instrumentos de coleta (questionários, logs, planilhas, etc.)

Liste todos os instrumentos que serão usados para coletar dados (arquivos, formulários, scripts, ferramentas), com uma breve descrição do papel de cada um.

#### 11.2 Materiais de suporte (instruções, guias)

Descreva as instruções escritas, guias rápidos, slides ou outros materiais que serão fornecidos a participantes e administradores do experimento.

#### 11.3 Procedimento experimental (protocolo – visão passo a passo)

Escreva, em ordem, o que acontecerá na operação (do convite ao encerramento), de modo que alguém consiga executar o experimento seguindo esse roteiro.

#### 11.4 Plano de piloto (se haverá piloto, escopo e critérios de ajuste)

Indique se um piloto será realizado, com que participantes e objetivos, e defina que tipo de ajuste do protocolo poderá ser feito com base nesse piloto.

---

### 12. Plano de análise de dados (pré-execução)

#### 12.1 Estratégia geral de análise (como responderá às questões)

Explique, em alto nível, como os dados coletados serão usados para responder cada questão de pesquisa ou de negócio.

#### 12.2 Métodos estatísticos planejados

Liste os principais testes ou técnicas estatísticas que pretende usar (por exemplo, t-teste, ANOVA, testes não paramétricos, regressão).

#### 12.3 Tratamento de dados faltantes e outliers

Defina previamente as regras para lidar com dados ausentes e valores extremos, evitando decisões oportunistas após ver os resultados.

#### 12.4 Plano de análise para dados qualitativos (se houver)

Descreva como você tratará dados qualitativos (entrevistas, comentários, observações), especificando a técnica de análise (codificação, categorias, etc.).

---

### 13. Avaliação de validade (ameaças e mitigação)

#### 13.1 Validade de conclusão

Liste ameaças que podem comprometer a robustez das conclusões estatísticas (baixo poder, violação de suposições, erros de medida) e como pretende mitigá-las.

#### 13.2 Validade interna

Identifique ameaças relacionadas a causas alternativas para os efeitos observados (history, maturation, selection, etc.) e explique suas estratégias de controle.

#### 13.3 Validade de constructo

Refleta se as medidas escolhidas realmente representam os conceitos de interesse e descreva como você reduzirá ambiguidades de interpretação.

#### 13.4 Validade externa

Discuta em que contextos os resultados podem ser generalizados e quais diferenças de cenário podem limitar essa generalização.

#### 13.5 Resumo das principais ameaças e estratégias de mitigação

Faça uma síntese das ameaças mais críticas e das ações planejadas, de preferência em forma de lista ou tabela simples.

---

### 14. Ética, privacidade e conformidade

#### 14.1 Questões éticas (uso de sujeitos, incentivos, etc.)

Descreva potenciais questões éticas (pressão para participar, uso de estudantes, incentivos, riscos de exposição) e como serão tratadas.

#### 14.2 Consentimento informado

Explique como os participantes serão informados sobre objetivos, riscos, benefícios e como registrarão seu consentimento.

#### 14.3 Privacidade e proteção de dados

Indique que dados pessoais serão coletados, como serão protegidos (anonimização, pseudoanonimização, controle de acesso) e por quanto tempo serão mantidos.

#### 14.4 Aprovações necessárias (comitê de ética, jurídico, DPO, etc.)

Liste órgãos ou pessoas que precisam aprovar o experimento (comitê de ética, jurídico, DPO, gestores) e o status atual dessas aprovações.

---

### 15. Recursos, infraestrutura e orçamento

#### 15.1 Recursos humanos e papéis

Identifique os membros da equipe do experimento e descreva brevemente o papel e responsabilidade de cada um.

#### 15.2 Infraestrutura técnica necessária

Liste ambientes, servidores, ferramentas, repositórios e integrações que devem estar disponíveis para executar o experimento.

#### 15.3 Materiais e insumos

Relacione materiais físicos ou digitais necessários (máquinas, licenças, formulários, dispositivos) que precisam estar prontos antes da operação.

#### 15.4 Orçamento e custos estimados

Faça uma estimativa dos principais custos envolvidos (horas de pessoas, serviços, licenças, infraestrutura) e a fonte de financiamento.

---

### 16. Cronograma, marcos e riscos operacionais

#### 16.1 Macrocronograma (até o início da execução)

Defina as principais datas e marcos (conclusão do plano, piloto, revisão, início da operação) com uma visão de tempo realista.

#### 16.2 Dependências entre atividades

Indique quais atividades dependem de outras para começar (por exemplo, treinamento após aprovação ética), deixando essas dependências claras.

#### 16.3 Riscos operacionais e plano de contingência

Liste riscos ligados a cronograma, disponibilidade de pessoas ou recursos, e descreva ações de contingência caso esses riscos se materializem.

---

### 17. Governança do experimento

#### 17.1 Papéis e responsabilidades formais

Defina quem decide, quem executa, quem revisa e quem apenas deve ser informado, deixando claro o fluxo de responsabilidade.

#### 17.2 Ritos de acompanhamento pré-execução

Descreva as reuniões, checkpoints e revisões previstos antes da execução, incluindo frequência e participantes.

#### 17.3 Processo de controle de mudanças no plano

Explique como mudanças no desenho ou no escopo do experimento serão propostas, analisadas, aprovadas e registradas.

---

### 18. Plano de documentação e reprodutibilidade

#### 18.1 Repositórios e convenções de nomeação

Indique onde o plano, instrumentos, scripts e dados (futuros) serão armazenados e quais convenções de nomes serão usadas.

#### 18.2 Templates e artefatos padrão

Liste os modelos (questionários, formulários, checklists, scripts) que serão usados e onde podem ser encontrados.

#### 18.3 Plano de empacotamento para replicação futura

Descreva o que será organizado desde já (documentos, scripts, instruções) para facilitar a replicação do experimento por outras equipes ou no futuro.

---

### 19. Plano de comunicação

#### 19.1 Públicos e mensagens-chave pré-execução

Liste os grupos que precisam ser comunicados e quais mensagens principais devem receber (objetivos, escopo, datas, impactos esperados).

#### 19.2 Canais e frequência de comunicação

Defina por quais canais (e-mail, reuniões, Slack/Teams, etc.) e com que frequência as comunicações serão feitas.

#### 19.3 Pontos de comunicação obrigatórios

Especifique os eventos que exigem comunicação formal (aprovação do plano, mudanças relevantes, adiamentos, cancelamentos).

---

### 20. Critérios de prontidão para execução (Definition of Ready)

#### 20.1 Checklist de prontidão (itens que devem estar completos)

Liste os itens que precisam estar finalizados e aprovados (plano, instrumentos, aprovação ética, recursos, comunicação) para autorizar o início da operação.

#### 20.2 Aprovações finais para iniciar a operação

Indique quem precisa dar o “ok final” (nomes ou cargos) e como esse aceite será registrado antes da execução começar.
