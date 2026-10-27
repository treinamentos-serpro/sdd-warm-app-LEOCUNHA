# Discovery: Aplicação de Previsão do Tempo

## Contexto

A empresa solicitou uma aplicação para consulta de informações meteorológicas. Usuários devem poder buscar cidades, consultar o clima atual e a previsão para cinco dias, alternar a unidade de temperatura entre Celsius e Fahrenheit e usar a aplicação em dispositivos móveis.

O briefing define as capacidades principais, mas não detalha o público-alvo, a fonte dos dados meteorológicos, as regras de seleção de cidades ou metas de qualidade. Esses pontos devem ser esclarecidos antes da especificação detalhada.

## Requisitos Funcionais

- **RF-01 — Buscar cidades:** permitir que o usuário pesquise cidades por nome.
- **RF-02 — Consultar clima atual:** exibir as condições meteorológicas atuais da cidade selecionada.
- **RF-03 — Consultar previsão:** exibir a previsão do tempo para cinco dias da cidade selecionada.
- **RF-04 — Alternar unidade de temperatura:** permitir alternar entre Celsius e Fahrenheit para as temperaturas exibidas.
- **RF-05 — Usar em dispositivos móveis:** disponibilizar as funcionalidades principais em dispositivos móveis.

## Requisitos Não-Funcionais

Os itens abaixo são necessidades de qualidade a validar com as partes interessadas; o briefing não define metas específicas.

- **RNF-01 — Responsividade:** a interface deve se adaptar a telas móveis e manter as funções principais utilizáveis.
- **RNF-02 — Usabilidade:** busca, seleção da cidade e leitura das informações meteorológicas devem ser compreensíveis.
- **RNF-03 — Desempenho:** estabelecer um tempo máximo aceitável para buscar uma cidade e carregar os dados meteorológicos.
- **RNF-04 — Disponibilidade e resiliência:** definir o comportamento quando a fonte de dados estiver indisponível ou a conexão do usuário falhar.
- **RNF-05 — Acessibilidade:** definir os requisitos de acessibilidade aplicáveis, incluindo navegação por teclado e compatibilidade com tecnologias assistivas.

## Riscos

- Nomes de cidades podem ser ambíguos ou corresponder a mais de uma localidade.
- Dados meteorológicos podem estar indisponíveis, atrasados ou variar conforme a fonte e a localização.
- Falhas de rede ou indisponibilidade do provedor podem impedir a consulta.
- Fusos horários e a definição de “próximos cinco dias” podem gerar interpretações diferentes na apresentação da previsão.
- Aplicar a unidade escolhida apenas a parte das temperaturas pode resultar em informações inconsistentes.
- Sem metas de desempenho, disponibilidade e acessibilidade, será difícil avaliar objetivamente a qualidade da solução.

## Perguntas em Aberto

- Quais fontes ou provedores de dados meteorológicos são aprovados?
- A busca deve aceitar apenas cidades ou também estados, países, códigos postais e localização atual?
- Como o usuário deve escolher quando a busca retornar cidades homônimas?
- A previsão de cinco dias deve incluir o dia atual? Deve ser apresentada por dia, por período ou em ambos os formatos?
- Quais informações devem aparecer no clima atual e na previsão, além da temperatura?
- A unidade escolhida deve ser mantida entre sessões ou dispositivos?
- Qual deve ser o comportamento quando uma busca não encontrar resultados, houver erro ou os dados estiverem indisponíveis?
- Há requisitos para idioma, formatos de data e hora, localização ou conversão entre unidades?
- Quais navegadores, sistemas operacionais e tamanhos de tela devem ser suportados?
- Quais são as metas esperadas de desempenho, disponibilidade e acessibilidade?

## Suposições

- A busca retorna resultados para o usuário selecionar uma cidade antes de consultar a previsão.
- O clima atual e a previsão de cinco dias são obtidos de uma fonte externa de dados meteorológicos.
- Celsius e Fahrenheit afetam a apresentação das temperaturas, não os dados meteorológicos subjacentes.
- A aplicação deve oferecer as mesmas funções essenciais em dispositivos móveis e em telas maiores.
- Critérios de desempate entre cidades, persistência de preferências e detalhes dos dados serão definidos durante a especificação.