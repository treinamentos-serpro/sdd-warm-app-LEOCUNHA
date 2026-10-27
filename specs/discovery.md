# Discovery: Aplicação de Previsão do Tempo

## Contexto

A empresa solicitou uma aplicação web para consulta de informações meteorológicas. Usuários devem poder buscar cidades, consultar o clima atual e a previsão para cinco dias e alternar a unidade de temperatura entre Celsius e Fahrenheit. O produto também deve ser utilizável em dispositivos móveis.

O briefing define capacidades principais, mas não detalha público-alvo, cobertura geográfica, regras para seleção de cidades ou metas de qualidade. O Open-Meteo foi escolhido como fonte, mas cobertura, termos de uso e forma de integração ainda precisam ser validados. As metas numéricas sugeridas abaixo são propostas iniciais para discussão, não compromissos já aprovados.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** permitir que o usuário pesquise cidades por nome.
- **RF-02 — Consultar clima atual:** exibir as condições meteorológicas atuais da cidade selecionada.
- **RF-03 — Consultar previsão:** exibir a previsão do tempo para hoje e os quatro dias seguintes na cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** permitir alternar entre Celsius e Fahrenheit para as temperaturas exibidas.

O antigo RF-05 (“usar em dispositivos móveis”) foi reclassificado: disponibilidade em diferentes tamanhos de tela é um atributo de qualidade, não uma ação independente do usuário. Está coberto pelo RNF-01 e pelas perguntas sobre compatibilidade.

## Requisitos Não-Funcionais

Os requisitos abaixo descrevem qualidades do produto. Onde há uma meta candidata, ela precisa ser validada com as partes interessadas e com as limitações do provedor.

- **RNF-01 — Responsividade:** manter as funções principais utilizáveis em larguras de tela de 320 px a 1920 px, sem rolagem horizontal da página.
- **RNF-02 — Usabilidade:** permitir que usuários concluam busca, seleção de cidade e consulta meteorológica sem orientação externa; validar o fluxo com usuários representativos antes do aceite.
- **RNF-03 — Desempenho:** como meta candidata, renderizar clima atual e previsão em até 3 segundos no percentil 95 após a seleção da cidade, sob condições de rede e dispositivo de referência a definir.
- **RNF-04 — Disponibilidade e resiliência:** definir meta mensal de disponibilidade; como referência inicial, avaliar 99,5%. Tratar falhas do provedor ou da rede sem apresentar dados antigos como atuais.
- **RNF-05 — Acessibilidade:** adotar WCAG 2.2 nível AA como meta candidata, cobrindo navegação por teclado, foco visível, contraste e nomes acessíveis para controles.
- **RNF-06 — Compatibilidade:** suportar a matriz acordada de navegadores, sistemas operacionais e dispositivos móveis; a matriz ainda precisa ser definida.
- **RNF-07 — Privacidade e segurança:** usar HTTPS e, caso localização seja incluída, solicitá-la somente com permissão explícita e limitar coleta e retenção ao necessário.
- **RNF-08 — Atualidade dos dados:** informar o horário da última atualização e definir a idade máxima aceitável para apresentar dados como atuais.

## Ambiguidades e Perguntas em Aberto (PM Cético)

| ID | Pergunta em aberto | Impacto de seguir sem resposta |
|---|---|---|
| A-01 | Quem são os usuários principais e em quais situações consultarão o clima? | Prioridades de conteúdo e fluxos podem não atender ao público real. |
| A-02 | O produto será um site responsivo, um PWA ou também terá aplicativos nativos? | Afeta arquitetura, custo, cronograma e capacidades disponíveis. |
| A-03 | Quais países e regiões devem ser cobertos? | Pode haver localidades sem dados ou com qualidade insuficiente. |
| A-04 | Quais métricas definirão sucesso do produto? | Não haverá critério objetivo para avaliar adoção e resultado de negócio. |
| A-05 | Há marca, design system ou diretrizes editoriais a seguir? | Pode haver retrabalho visual e inconsistência com produtos existentes. |
| A-06 | A busca aceitará apenas nomes de cidades ou também estado, país, CEP e coordenadas? Deve sugerir resultados enquanto o usuário digita? | Contrato de busca e integração de geocodificação ficam indefinidos. |
| A-07 | Como distinguir cidades homônimas nos resultados? | Usuários podem consultar a previsão da localidade errada. |
| A-08 | O app deve detectar localização atual do usuário? | Pode ampliar escopo e introduzir permissões e tratamento de dados pessoais. |
| A-09 | Será possível salvar favoritas ou consultar várias cidades? | Decisão tardia pode exigir mudanças em navegação e persistência. |
| A-10 | O que exibir quando a busca não encontrar resultados ou falhar? | A jornada pode terminar sem orientação e não haverá critério completo de aceite. |
| A-11 | Quais campos compõem “clima atual” e previsão além da temperatura? | Layout, dados necessários e integração podem ser implementados de forma incompatível com a expectativa. |
| A-12 | Qual idade máxima os dados podem ter e qual horário local deve ser mostrado? | Dados atrasados ou horários ambíguos podem induzir decisões equivocadas. |
| A-13 | Como a previsão diária deve ser delimitada por fuso horário local e exibida nos cinco dias já definidos como hoje mais os quatro seguintes? | Sem regra de fuso e de agregação diária, os dados podem aparecer em datas incorretas ou divergir do provedor. |
| A-14 | A previsão será diária, horária ou ambas? Quais métricas serão exibidas? | Afeta volume de dados, interface e escopo de implementação. |
| A-15 | Alertas meteorológicos severos fazem parte do produto? | A ausência pode ser uma lacuna relevante ou sua inclusão pode ampliar escopo sem aprovação. |
| A-16 | Para o Open-Meteo, quais cobertura, limites aplicáveis, termos de uso e exigências de atribuição devem ser considerados? | A decisão do provedor está tomada, mas restrições de cobertura ou uso ainda podem exigir mudança no produto ou na integração. |
| A-17 | Com que frequência atualizar dados e haverá atualização manual? | Cache, custo de chamadas e percepção de atualidade permanecem indefinidos. |
| A-18 | Celsius/Fahrenheit afeta somente temperaturas ou também outras grandezas meteorológicas? | A unidade inicial Celsius está definida; sem definir o escopo da conversão, a interface pode misturar convenções de forma confusa. |
| A-19 | A preferência de unidade será mantida entre visitas ou dispositivos? | A preferência pode desaparecer ou se comportar de modo imprevisível. |
| A-20 | Como datas, horários e números devem ser formatados para a UI pt-BR, incluindo fuso horário da cidade consultada? | O idioma está definido, mas formatos ou horários inconsistentes podem confundir usuários e deslocar a previsão entre dias. |
| A-21 | Quais navegadores, sistemas operacionais e tamanhos de tela serão suportados? Haverá paridade entre celular e desktop? | Compatibilidade e cobertura de testes ficam sem limite verificável. |
| A-22 | Como tratar carregamento, erro, perda de conexão, dados em cache e operação offline? | Falhas podem deixar telas vazias ou apresentar dados antigos sem contexto. |
| A-23 | Qual padrão e nível de acessibilidade serão exigidos? | A acessibilidade não poderá ser validada consistentemente e pode excluir usuários. |
| A-24 | Quais metas de desempenho e condições de referência serão adotadas? | “Rápido” permanece subjetivo; não há critério para testar ou dimensionar o sistema. |
| A-25 | Qual disponibilidade é esperada e qual contingência haverá para falha do provedor? | Não haverá expectativa operacional nem estratégia de continuidade definida. |
| A-26 | Quais dados serão coletados, especialmente se houver localização, e por quanto tempo serão retidos? | Há risco de coleta excessiva, exposição de dados e descumprimento de obrigações de privacidade. |
| A-27 | Qual volume de usuários e consultas simultâneas é esperado? | Custo, limites do provedor e desempenho sob carga podem ser descobertos tarde. |
| A-28 | As chamadas ao Open-Meteo serão feitas diretamente pelo navegador ou por uma camada intermediária? | Define fronteiras de rede, cache, observabilidade e tratamento de falhas; decidir tarde pode exigir mudança arquitetural. |

## Mapa de Riscos

Probabilidades preliminares, baseadas somente no briefing; revisar quando as perguntas em aberto forem respondidas.

| Tipo | Risco | Probabilidade | Impacto | Estratégia de mitigação |
|---|---|---|---|---|
| Técnico | Indisponibilidade, limite de requisições ou mudança na API meteorológica. | Média | Alto: consultas podem falhar ou deixar de funcionar. | Avaliar SLA, limites e versionamento; implementar timeout, tratamento de erros, cache e monitoramento. |
| Técnico | Dados atrasados ou cobertura insuficiente em algumas regiões. | Média | Alto: reduz confiança e pode induzir decisões incorretas. | Validar cobertura e frequência por região; exibir atualização e definir idade máxima aceitável. |
| Produto/técnico | Busca ambígua seleciona a cidade errada. | Média | Alto: previsão incorreta para a localidade desejada. | Exibir contexto como estado e país; testar homônimos e ausência de resultados. |
| Produto | Público, escopo móvel e definição da previsão não validados. | Alta | Alto: funcionalidades e experiência podem não corresponder à necessidade, gerando retrabalho. | Validar casos de uso e protótipos; fechar definições de plataforma e previsão antes do plano técnico. |
| Técnico | Lentidão em redes ou dispositivos móveis. | Média | Alto: abandono durante busca e carregamento. | Aprovar metas e ambiente de referência; medir percentis, otimizar payload e usar cache apropriado. |
| Técnico | Incompatibilidade ou baixa usabilidade em determinados celulares e navegadores. | Média | Alto: funções principais podem ficar inacessíveis. | Definir matriz de suporte e testar fluxos principais em dispositivos e navegadores representativos. |
| Técnico | Falha de rede sem recuperação clara ou cache contextualizado. | Média | Médio/alto: tela vazia ou dados antigos confundem o usuário. | Projetar estados de erro e nova tentativa; identificar claramente dados em cache e seu horário. |
| Técnico/produto | Acessibilidade insuficiente. | Média | Alto: usuários podem não conseguir completar busca e consulta. | Adotar WCAG 2.2 AA como meta candidata e incluir testes com teclado e tecnologias assistivas. |
| Técnico/comercial | Custos, licenças ou atribuição do provedor incompatíveis com o produto. | Baixa/média | Alto: aumento de custo, restrição de uso ou troca de provedor. | Revisar termos e custos antes da escolha; acompanhar consumo e avaliar alternativa. |
| Técnico/produto | Uso de localização sem consentimento ou necessidade clara, caso esse recurso seja incluído. | Baixa/média | Alto: risco de privacidade e perda de confiança. | Confirmar escopo; pedir permissão explícita e minimizar coleta e retenção. |
| Produto | Unidades, idioma ou formatos locais inconsistentes. | Média | Médio: valores podem ser confusos ou interpretados incorretamente. | Definir padrão, escopo das conversões, localização e persistência de preferências; validar com usuários. |
| Produto | Ausência de métricas de sucesso. | Alta | Médio/alto: decisões de evolução ficam baseadas em impressões. | Definir indicadores antes do lançamento e instrumentar coleta proporcional, respeitando privacidade. |

## Suposições

- A busca retorna resultados para o usuário selecionar uma cidade antes de consultar a previsão.
- O clima atual e a previsão de cinco dias são obtidos de uma fonte externa de dados meteorológicos.
- Celsius e Fahrenheit afetam a apresentação das temperaturas, não os dados meteorológicos subjacentes.
- A aplicação é web e deve oferecer as funções essenciais em dispositivos móveis e em telas maiores; necessidade de PWA ou apps nativos está em aberto.
- Localização atual, alertas, favoritas e funcionamento offline não estão confirmados como parte do escopo.
- Critérios de desempate entre cidades, persistência de preferências e detalhes dos dados serão definidos durante a especificação.

## Personas (Hipóteses para Validação)

As personas abaixo representam necessidades plausíveis para orientar o discovery. Não são resultados de pesquisa; devem ser validadas com usuários reais antes de orientar decisões definitivas de produto.

### 1. Camila, a pessoa que consulta antes de sair

- **Objetivo principal:** saber rapidamente como está o tempo e o que esperar nos próximos dias para decidir o que vestir e se precisa levar guarda-chuva.
- **Contexto de uso:** principalmente no celular, em deslocamentos ou pouco antes de sair; consultas curtas e frequentes.
- **Métrica de sucesso do ponto de vista dela:** encontra a cidade e entende as condições atuais em até 30 segundos, sem confundir a previsão de hoje com a dos dias seguintes.

### 2. Rafael, o profissional que trabalha ao ar livre

- **Objetivo principal:** antecipar condições que possam afetar atividades externas e organizar o trabalho com base no clima atual e na previsão.
- **Contexto de uso:** celular durante o expediente; consultas rápidas entre atividades, possivelmente em rede instável.
- **Métrica de sucesso do ponto de vista dele:** consegue consultar a previsão da localidade correta e identificar quando os dados foram atualizados, sem perder tempo com uma busca ambígua.

### 3. Marina, a pessoa que planeja uma viagem

- **Objetivo principal:** consultar o clima do destino nos próximos cinco dias para planejar roupas e atividades.
- **Contexto de uso:** desktop ou celular durante o planejamento, e celular perto ou durante a viagem; pode alternar entre a cidade de origem e o destino.
- **Métrica de sucesso do ponto de vista dela:** localiza o destino, compreende a previsão para cada dia e consegue usar a unidade de temperatura que prefere antes de tomar decisões de planejamento.

## Decisões

| Decisão | Justificativa | Perguntas em aberto resolvidas |
|---|---|---|
| **Fonte de dados: Open-Meteo, sem API key.** | Evita exigir uma chave de API para a integração inicial e define o provedor meteorológico a ser considerado na especificação. | Resolve parcialmente A-16 ao selecionar o provedor. Cobertura geográfica, limites aplicáveis, termos de uso e atribuição ainda devem ser verificados. |
| **“Cinco dias” significa hoje mais os quatro dias seguintes.** | Define um intervalo de cinco dias de calendário, incluindo a data atual. | Resolve a inclusão de hoje em A-13. A granularidade da previsão e o tratamento do fuso horário ainda precisam ser definidos. |
| **Unidade padrão: Celsius.** | Estabelece uma apresentação inicial consistente com o idioma e o público inicial definidos para a interface. | Resolve qual é o padrão inicial em A-18. Ainda falta confirmar se outras grandezas além da temperatura terão unidades configuráveis. |
| **Sem autenticação e sem persistência de servidor.** | Mantém o escopo inicial simples e evita exigir contas ou armazenamento de dados associado a usuários no servidor. | Define que autenticação e persistência de dados no servidor não fazem parte do escopo. Restringe A-09 quanto a favoritos sincronizados entre dispositivos; persistência local da unidade em A-19 continua em aberto. Também reduz, mas não elimina, questões de privacidade de A-26. |
| **Idioma da interface: português do Brasil (pt-BR).** | Define o idioma inicial da experiência e a localização principal do produto. | Resolve o idioma em A-20. Formatos específicos de data e hora devem seguir pt-BR, mas precisam ser confirmados junto às regras de fuso horário. |

As decisões acima orientam a especificação seguinte. Pontos explicitamente indicados como pendentes permanecem perguntas em aberto e não devem ser tratados como requisitos aprovados.

## Crítica Arquitetural e Prontidão para Especificação

Esta seção registra lacunas que podem alterar contratos, arquitetura ou critérios de aceite. As prioridades indicam a ordem recomendada de resolução, não uma decisão de implementação.

### Lacunas com maior potencial de retrabalho

- **P0 — Recorte de produto:** público, objetivo principal, região inicial atendida e limites do MVP ainda não foram validados. As personas são hipóteses, e a persona de trabalho externo pode sugerir expectativas de confiabilidade ou alertas que não estão aprovadas. Definir o público prioritário, ao menos a região inicial, e o que fica explicitamente fora do MVP.
- **P0 — Contrato de dados meteorológicos:** RF-02 e RF-03 ainda não definem o conjunto mínimo de campos, se a previsão será diária ou horária, nem a regra de agregação por data e fuso local. Definir esses elementos, incluindo quais unidades serão exibidas, antes de fechar a spec funcional e o modelo de dados.
- **P0 — Integração Open-Meteo:** A-16 e A-28 deixam pendentes cobertura para a região-alvo, termos de uso, limites aplicáveis, atribuição e se as chamadas serão diretas do navegador ou via camada intermediária. Validar os requisitos do provedor e decidir a fronteira da integração. “Sem persistência de servidor” não implica, por si só, ausência de uma camada de backend/BFF.
- **P0 — Falhas, cache e atualidade:** RNF-04 e RNF-08 não determinam quando os dados são considerados antigos, se dados em cache podem ser mostrados nem como identificá-los. Definir frequência de atualização, limite de atualidade, horário apresentado e comportamento diante de falhas sem rotular dados antigos como atuais.
- **P1 — Busca e seleção:** A-06, A-07 e A-10 deixam indefinidos os campos aceitos, o contexto dos resultados, a desambiguação e os estados sem resultado. Isso condiciona geocodificação, modelo da cidade selecionada e fluxo da interface.
- **P1 — Persistência no cliente e escopo pessoal:** “Sem persistência de servidor” não decide se a unidade será salva localmente nem se favoritos/histórico ficam fora do escopo por completo. Registrar essa decisão e evitar pressupor sincronização entre dispositivos.

### Qualidade e critérios de aceite

- **Desempenho:** o limite candidato de 3 segundos precisa indicar dispositivo e rede de referência, início e fim da medição, percentual de sucesso e se inclui latência do provedor.
- **Disponibilidade:** a meta candidata de 99,5% precisa definir se mede a aplicação ou também a dependência meteorológica, bem como janela, cálculo e tratamento de manutenção.
- **Responsividade e compatibilidade:** a faixa de 320–1920 px não substitui a matriz de navegadores, sistemas operacionais e dispositivos suportados nem define paridade funcional.
- **Usabilidade e acessibilidade:** transformar “sem orientação externa” e a meta WCAG em critérios observáveis, jornadas e condições de validação.
- **Preferência de unidade:** decidir persistência local e comportamento ao reabrir a aplicação; a escolha de Celsius como padrão já está resolvida.

### Prontidão

A especificação pode começar como rascunho, mas deve manter explícitas as decisões pendentes. Antes de aprovar critérios de aceite ou fechar arquitetura, resolver os itens P0 acima: público e região inicial, escopo do MVP, contrato mínimo de dados e fuso, restrições do Open-Meteo e fronteira da integração, além da política de falhas/cache/atualidade. Também aprovar condições mensuráveis de desempenho, disponibilidade, compatibilidade e preferência local de unidade. Outros detalhes de cobertura, matriz final de navegadores e métricas de produto podem permanecer rastreados como pendências se não alterarem esses contratos.