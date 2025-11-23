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

#### 3.1 Objetivo geral (Goal template)

Preencha o objetivo geral usando um template claro (por exemplo, GQM), deixando explícito o que será analisado, com qual propósito, sob qual perspectiva e em qual contexto.

#### 3.2 Objetivos específicos

Decomponha o objetivo geral em metas mais focadas (O1, O2, etc.), que descrevam resultados concretos de aprendizado ou decisão que o experimento deve gerar.

#### 3.3 Questões de pesquisa / de negócio

Formule perguntas claras que o experimento deverá responder (Q1, Q2, etc.), em linguagem que faça sentido para os stakeholders técnicos e de negócio.

#### 3.4 Métricas associadas (GQM)

Associe a cada questão as métricas que serão usadas para respondê-la, com nome, definição, unidade e fonte dos dados, garantindo alinhamento entre G, Q e M.

---

### 4. Escopo e contexto do experimento

#### 4.1 Escopo funcional / de processo (incluído e excluído)

Explique claramente o que será coberto (atividades, artefatos, equipes, módulos) e o que ficará fora do experimento, para evitar interpretações divergentes.

#### 4.2 Contexto do estudo (tipo de organização, projeto, experiência)

Caracterize o contexto em que o estudo ocorrerá: tipo e tamanho de organização, tipo de projeto, criticidade e perfil de experiência dos participantes.

#### 4.3 Premissas

Liste as suposições consideradas verdadeiras para o plano funcionar (por exemplo, disponibilidade de ambiente, estabilidade do sistema), mesmo que não possam ser garantidas.

#### 4.4 Restrições

Registre limitações práticas como tempo, orçamento, ferramentas, acessos ou regras organizacionais que impõem limites ao desenho.

#### 4.5 Limitações previstas

Explique fatores que podem prejudicar a generalização dos resultados (validez externa), como contexto muito específico ou amostra pouco representativa.

---

### 5. Stakeholders e impacto esperado

#### 5.1 Stakeholders principais

Liste os grupos ou papéis que têm interesse ou serão impactados pelo experimento (por exemplo, devs, QA, produto, gestores, clientes internos).

#### 5.2 Interesses e expectativas dos stakeholders

Descreva o que cada grupo espera obter do experimento (insights, evidências, validação de decisão, mitigação de risco, etc.).

#### 5.3 Impactos potenciais no processo / produto

Antecipe como a execução do experimento pode afetar prazos, qualidade, carga de trabalho ou o próprio produto durante e após o estudo.

---

### 6. Riscos de alto nível, premissas e critérios de sucesso

#### 6.1 Riscos de alto nível (negócio, técnicos, etc.)

Identifique os principais riscos para negócio e tecnologia (atrasos, falhas de ambiente, indisponibilidade de dados, etc.) em nível macro.

#### 6.2 Critérios de sucesso globais (go / no-go)

Defina as condições sob as quais o experimento será considerado útil e viável, inclusive critérios que sustentem uma decisão de seguir ou não com mudanças.

#### 6.3 Critérios de parada antecipada (pré-execução)

Descreva situações em que o experimento deve ser adiado ou cancelado antes de começar (falta de recursos críticos, reprovação ética, mudanças de contexto).

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
