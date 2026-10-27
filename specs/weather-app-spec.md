# Especificação de Produto: Aplicação de Previsão do Tempo

**Status:** Baseline de implementação do MVP; gates externos de lançamento descritos em Open Questions  
**Fonte:** [discovery.md](discovery.md)  
**Idioma da interface:** português do Brasil (pt-BR)

## Overview

Aplicação web responsiva, em pt-BR, para pesquisar e selecionar uma cidade, consultar temperatura e condição atuais e ver a previsão diária de hoje mais os quatro dias seguintes. Todas as temperaturas podem ser exibidas em Celsius ou Fahrenheit. O MVP consulta diretamente o Open-Meteo, sem chave de API; termos, atribuição e cobertura efetiva devem ser confirmados antes do release.

O produto atende inicialmente a consultas rápidas no celular e ao planejamento em telas maiores. Camila, Rafael e Marina, descritos no discovery, são personas hipotéticas para orientar a validação, não segmentos confirmados por pesquisa. A experiência deve informar a cidade consultada e o horário da atualização, evitar confundir localidades homônimas e comunicar indisponibilidade sem apresentar dados antigos como atuais.

Esta especificação define os defaults de implementação do MVP. Os gates em Open Questions são verificações externas necessárias para o release e não bloqueiam o desenvolvimento com o baseline descrito.

## Functional Requirements

### FR-01 — Buscar e selecionar cidade

A pessoa usuária informa um nome de cidade e envia a busca explicitamente. Enquanto aguarda, a aplicação exibe `Buscando cidades...` e impede requisições duplicadas. O geocodificador retorna até 10 resultados na ordem de relevância fornecida; cada resultado exibe nome e, quando disponíveis, região administrativa e país. Se faltar contexto geográfico, exibir as coordenadas com duas casas decimais. Coordenadas são obrigatórias para selecionar qualquer resultado e consultar o serviço meteorológico; sem elas, o resultado não pode ser selecionado, mesmo que tenha nome, região e país. A consulta meteorológica só começa após a seleção. A busca é global dentro da cobertura do provedor. Busca por CEP, coordenadas e sugestões durante a digitação estão fora do escopo.

### FR-02 — Consultar condições atuais

Após selecionar uma cidade, a aplicação exibe `Carregando informações do clima...` enquanto consulta temperatura, condição atual e previsão. A tela informa o momento em que a aplicação recebeu a última resposta meteorológica válida, apresentado na zona local da cidade; se essa zona não estiver disponível, apresenta o horário em UTC, identificado como tal. Esse horário descreve a consulta à fonte, não a hora de geração dos dados pelo provedor. A pessoa usuária pode atualizar os dados por ação explícita; não há atualização automática em segundo plano. A idade de cache é calculada desde a recepção bem-sucedida da resposta; dados com mais de 60 minutos são identificados como desatualizados e nunca como atuais.

### FR-03 — Consultar previsão diária de cinco dias

Após selecionar uma cidade, a aplicação apresenta a previsão diária para a data local de hoje e os quatro dias seguintes. Cada dia inclui temperatura mínima, máxima e condição meteorológica. Se não for possível determinar o fuso IANA da cidade, a aplicação informa a indisponibilidade da previsão e não usa o fuso do dispositivo.

### FR-04 — Alternar unidade de temperatura

A pessoa usuária deve poder alternar entre Celsius e Fahrenheit. Celsius é a unidade inicial. A alternância afeta todas as temperaturas exibidas sem alterar as condições ou datas. A unidade escolhida é salva localmente no mesmo navegador e perfil; não há sincronização entre dispositivos.

### Baseline de implementação

Este baseline orienta o desenvolvimento e os testes do MVP. Condições atuais incluem temperatura e condição meteorológica em pt-BR; cada registro diário inclui data local, temperaturas mínima e máxima e condição em pt-BR. A idade do dado em cache começa quando a aplicação recebe com sucesso a resposta do provedor: até 60 minutos, inclusive, é atual; acima de 60 minutos é desatualizado. Requisições externas expiram após 8 segundos. Converter com `°F = (°C × 9/5) + 32` e `°C = (°F − 32) × 5/9`; exibir uma casa decimal e arredondar empates para longe de zero. O MVP persiste a unidade no armazenamento local do navegador, consulta diretamente o Open-Meteo e não faz refresh automático.

## User Stories

- **US-01 (FR-01) — Encontrar uma cidade:** Como Camila, a pessoa que consulta antes de sair, quero buscar uma cidade pelo nome e selecionar o resultado correto para consultar o clima da cidade que desejo.
- **US-02 (FR-02) — Ver as condições atuais:** Como Camila, a pessoa que consulta antes de sair, quero ver as condições meteorológicas atuais e o horário da atualização para decidir como me preparar antes de sair.
- **US-03 (FR-03) — Consultar os próximos dias:** Como Camila, a pessoa que consulta antes de sair, quero ver a previsão de hoje e dos quatro dias seguintes para planejar o que vestir e se preciso levar guarda-chuva.
- **US-04 (FR-01) — Distinguir localidades:** Como Rafael, o profissional que trabalha ao ar livre, quero comparar o contexto geográfico dos resultados de busca para selecionar a cidade correta e evitar planejar minhas atividades com dados de outra localidade.
- **US-05 (FR-02) — Conferir a atualização:** Como Rafael, o profissional que trabalha ao ar livre, quero consultar as condições atuais e identificar quando os dados foram atualizados para avaliar se ainda são úteis para organizar meu trabalho.
- **US-06 (FR-03) — Planejar uma viagem:** Como Marina, a pessoa que planeja uma viagem, quero consultar a previsão diária de cinco dias para o destino para planejar roupas e atividades.
- **US-07 (FR-04) — Escolher a unidade:** Como Marina, a pessoa que planeja uma viagem, quero alternar as temperaturas entre Celsius e Fahrenheit para interpretar a previsão na unidade que prefiro.

As histórias refletem necessidades hipotéticas do discovery e devem ser validadas com usuários representativos.

## Rastreabilidade

A tabela liga cada User Story aos critérios de aceite que a verificam e aos requisitos não funcionais aplicáveis. Uma mesma referência pode aparecer em mais de uma história quando o mesmo fluxo atende a objetivos diferentes.

| User Story | Acceptance Criteria relacionados | Requisitos não funcionais relevantes |
|---|---|---|
| **US-01 — Encontrar uma cidade** | AC-01.1–AC-01.14 | NFR-01, NFR-02, NFR-05–NFR-07, NFR-09–NFR-10 |
| **US-02 — Ver as condições atuais** | AC-02.1–AC-02.9, AC-02.12–AC-02.13 | NFR-01–NFR-10 |
| **US-03 — Consultar os próximos dias** | AC-03.1–AC-03.9 | NFR-01–NFR-10 |
| **US-04 — Distinguir localidades** | AC-01.2, AC-01.6, AC-01.12–AC-01.13 | NFR-01, NFR-02, NFR-05–NFR-07, NFR-09–NFR-10 |
| **US-05 — Conferir a atualização** | AC-02.2–AC-02.4, AC-02.8–AC-02.11, AC-02.13 | NFR-01–NFR-10 |
| **US-06 — Planejar uma viagem** | AC-03.1–AC-03.9 | NFR-01–NFR-10 |
| **US-07 — Escolher a unidade** | AC-04.1–AC-04.7 | NFR-01, NFR-02, NFR-05–NFR-07, NFR-09 |

## Acceptance Criteria

Cada cenário usa `Given / When / Then` com relógio e respostas controlados por fixtures. Os critérios cobrem o baseline de implementação do MVP; variações por fornecedor só são aceitas se preservarem estes resultados observáveis.

### FR-01 — Buscar e selecionar cidade

- **AC-01.1:** **Given** uma resposta de busca com três localidades correspondentes à consulta `Lisboa`, **When** a pessoa usuária envia essa consulta, **Then** a lista exibe os três resultados e cada um pode ser selecionado.
- **AC-01.2:** **Given** dois resultados chamados `Springfield`, um com região `Illinois` e país retornado `United States`, e outro com região `Massachusetts` e país retornado `United States`, **When** os resultados são exibidos, **Then** cada item mostra a região correspondente e o país localizado como `Estados Unidos`.
- **AC-01.3:** **Given** uma resposta bem-sucedida do geocodificador sem localidades para a consulta `Atlantida`, **When** a pessoa usuária envia a consulta, **Then** a aplicação informa que não encontrou a cidade, preserva a consulta no campo e não envia requisição meteorológica.
- **AC-01.4:** **Given** uma busca com falha de rede ou resposta HTTP de erro, **When** a falha é recebida, **Then** a aplicação exibe `Não foi possível buscar cidades.` e o controle acessível `Tentar novamente`.
- **AC-01.5:** **Given** a mensagem de falha e `Tentar novamente` visíveis, **When** a pessoa usuária aciona o controle, **Then** uma nova requisição de geocodificação é iniciada.
- **AC-01.6:** **Given** um resultado selecionável com identificador e coordenadas conhecidos, **When** a pessoa usuária o seleciona, **Then** a localidade exibida corresponde ao resultado e a consulta meteorológica usa as coordenadas desse mesmo resultado.
- **AC-01.7:** **Given** o campo de busca vazio ou contendo apenas espaços, **When** a pessoa usuária tenta pesquisar, **Then** a aplicação solicita um nome de cidade e não envia requisição de geocodificação.
- **AC-01.8:** **Given** o texto `  São João d'El-Rei  ` no campo de busca, **When** a pessoa usuária pesquisa, **Then** o geocodificador recebe, após decodificação do parâmetro, exatamente `São João d'El-Rei`, preservando acentos, espaços internos, hífen e apóstrofo.
- **AC-01.9:** **Given** uma requisição de geocodificação sem resposta, **When** o relógio controlado avança 8 segundos, **Then** a aplicação encerra a espera, exibe `A consulta demorou mais que o esperado.` e `Tentar novamente`.
- **AC-01.10:** **Given** a primeira requisição de geocodificação expirada e uma segunda tentativa já concluída com resultados, **When** chega a resposta tardia da primeira requisição, **Then** os resultados exibidos continuam sendo os da segunda tentativa.
- **AC-01.11:** **Given** mais de 10 resultados de geocodificação, **When** a busca é exibida, **Then** a aplicação mostra apenas os 10 primeiros na ordem de relevância recebida.
- **AC-01.12:** **Given** um resultado com coordenadas e sem região administrativa ou país, **When** a busca é exibida, **Then** a aplicação mostra as coordenadas com duas casas decimais.
- **AC-01.13:** **Given** um resultado sem coordenadas, ainda que tenha nome, região e país, **When** a busca é exibida, **Then** o resultado não pode ser selecionado.
- **AC-01.14:** **Given** uma busca de cidade pendente, **When** a interface aguarda a resposta, **Then** exibe `Buscando cidades...` e envios repetidos não iniciam requisições adicionais.

### FR-02 — Consultar condições atuais

- **AC-02.1:** **Given** uma cidade selecionada e uma resposta válida com temperatura `21 °C` e condição `Ensolarado`, **When** as condições atuais são carregadas, **Then** a tela exibe `21,0 °C` e `Ensolarado`.
- **AC-02.2:** **Given** uma resposta meteorológica válida recebida em `2026-10-27T14:30:00Z` para uma cidade no fuso `America/Sao_Paulo`, **When** as condições são carregadas, **Then** a tela exibe a cidade e `Dados obtidos em 27/10/2026 11:30`.
- **AC-02.3:** **Given** o relógio controlado em `2026-10-27T15:00:00Z` e uma resposta válida recebida em `2026-10-27T14:00:00Z`, **When** as condições são carregadas, **Then** a aplicação identifica os dados como atuais.
- **AC-02.4:** **Given** o relógio controlado em `2026-10-27T15:00:01Z` e uma resposta válida recebida em `2026-10-27T14:00:00Z`, **When** as condições são carregadas, **Then** a aplicação identifica os dados como desatualizados e não os identifica como atuais.
- **AC-02.5:** **Given** uma resposta de condições atuais sem temperatura ou sem condição meteorológica, **When** os dados são carregados, **Then** o campo ausente exibe `Indisponível`, os demais campos disponíveis são exibidos e nenhum valor é inferido.
- **AC-02.6:** **Given** uma resposta HTTP `503` ou inválida para condições atuais, **When** a resposta é recebida, **Then** a aplicação informa indisponibilidade e exibe `Tentar novamente`.
- **AC-02.7:** **Given** uma requisição de condições atuais sem resposta, **When** o relógio controlado avança 8 segundos, **Then** a aplicação exibe `A consulta demorou mais que o esperado.` e `Tentar novamente`.
- **AC-02.8:** **Given** o relógio controlado em `2026-10-27T15:01:00Z`, dados em cache recebidos em `2026-10-27T14:00:00Z` e uma falha na nova requisição, **When** a falha é apresentada, **Then** os dados mantidos na tela são identificados como cache e desatualizados e exibem `Dados obtidos em 27/10/2026 11:00`.
- **AC-02.9:** **Given** uma consulta meteorológica pendente para a cidade A e dados atuais e previsão já exibidos para a cidade B, **When** a resposta tardia da cidade A chega, **Then** a localidade e todos os dados exibidos permanecem associados à cidade B.
- **AC-02.10:** **Given** condições e previsão exibidas para uma cidade, **When** a pessoa usuária aciona `Atualizar agora`, **Then** a aplicação inicia uma nova consulta de condições e previsão para as coordenadas da cidade selecionada e atualiza o horário após respostas válidas.
- **AC-02.11:** **Given** condições recebidas há 60 minutos e a tela mantida aberta, **When** o relógio controlado avança 1 segundo sem ação da pessoa usuária, **Then** a aplicação não inicia requisições e marca os dados como desatualizados.
- **AC-02.12:** **Given** uma consulta meteorológica pendente, **When** a aplicação aguarda condições e previsão, **Then** exibe `Carregando informações do clima...` até a conclusão ou timeout.
- **AC-02.13:** **Given** uma resposta meteorológica válida recebida em `2026-10-27T14:30:00Z` para uma cidade sem zona local conhecida, **When** o horário é exibido, **Then** aparece `Dados obtidos em 27/10/2026 14:30 UTC`.

### FR-03 — Consultar previsão diária de cinco dias

- **AC-03.1:** **Given** uma cidade com fuso `America/Sao_Paulo`, um relógio fixo em `2026-10-27T02:00:00Z` e cinco registros diários completos, **When** a previsão é carregada, **Then** são exibidas exatamente as cinco datas locais de `26/10/2026` a `30/10/2026`, uma vez cada.
- **AC-03.2:** **Given** cinco registros com datas, temperaturas mínima/máxima e condições distintos, **When** a lista é exibida, **Then** cada data exibe exatamente a mínima, a máxima e a condição do registro associado àquela data.
- **AC-03.3:** **Given** uma cidade com fuso `America/Sao_Paulo`, um relógio fixo em `2026-12-30T15:00:00Z` e registros diários de `30/12/2026` a `03/01/2027`, **When** a previsão é carregada, **Then** são exibidas exatamente essas cinco datas em ordem, uma vez cada.
- **AC-03.4:** **Given** uma resposta de previsão sem um registro diário, **When** a lista é exibida, **Then** a data correspondente exibe `Previsão indisponível` e nenhum valor de outro dia é repetido para preenchê-la.
- **AC-03.5:** **Given** uma cidade selecionada e uma resposta meteorológica válida recebida em horário conhecido, **When** a previsão é exibida, **Then** a localidade selecionada e o momento de recebimento da resposta ficam visíveis junto à previsão.
- **AC-03.6:** **Given** um registro diário sem temperatura mínima, máxima ou condição meteorológica, **When** a previsão é exibida, **Then** cada campo ausente exibe `Indisponível`, os campos disponíveis permanecem visíveis e nenhum valor é inferido.
- **AC-03.7:** **Given** uma resposta HTTP `503` ou inválida para previsão, **When** a resposta é recebida, **Then** a aplicação informa indisponibilidade e exibe `Tentar novamente`.
- **AC-03.8:** **Given** uma requisição de previsão sem resposta, **When** o relógio controlado avança 8 segundos, **Then** a aplicação exibe `A consulta demorou mais que o esperado.` e `Tentar novamente`.
- **AC-03.9:** **Given** uma localidade selecionada sem fuso horário IANA disponível, **When** a pessoa usuária solicita a previsão, **Then** a aplicação informa `Não foi possível determinar o fuso horário da cidade.` e não calcula as cinco datas usando o fuso do dispositivo.

### FR-04 — Alternar unidade de temperatura

- **AC-04.1:** **Given** uma sessão sem preferência de unidade previamente definida e temperaturas retornadas em Celsius, **When** a tela de condições e previsão é carregada, **Then** todas as temperaturas exibidas estão identificadas em Celsius (`°C`).
- **AC-04.2:** **Given** temperaturas de `0 °C` e `20 °C` nas condições atuais e na previsão, **When** a pessoa usuária seleciona Fahrenheit, **Then** todas são exibidas, respectivamente, como `32,0 °F` e `68,0 °F`.
- **AC-04.3:** **Given** temperaturas de `32,0 °F` e `14,0 °F` nas condições atuais e na previsão, **When** a pessoa usuária seleciona Celsius, **Then** todas são exibidas, respectivamente, como `0,0 °C` e `-10,0 °C`.
- **AC-04.4:** **Given** uma temperatura de `12,25 °C`, **When** a pessoa usuária seleciona Fahrenheit, **Then** a temperatura é exibida como `54,1 °F`.
- **AC-04.5:** **Given** uma previsão carregada com datas e descrições de condição, **When** a pessoa usuária alterna a unidade de temperatura, **Then** as datas e descrições permanecem iguais às anteriores à alternância.
- **AC-04.6:** **Given** o foco do teclado no controle de unidade, **When** a pessoa usuária o aciona com `Enter` ou `Espaço`, **Then** a unidade selecionada muda, todas as temperaturas exibidas passam a usar essa unidade e o estado selecionado é exposto à tecnologia assistiva.
- **AC-04.7:** **Given** a unidade Fahrenheit selecionada e salva no armazenamento local, **When** a mesma aplicação é reaberta no mesmo navegador e perfil, **Then** todas as temperaturas são exibidas em Fahrenheit sem nova seleção; em perfil sem preferência salva, a unidade inicial é Celsius.

## Non-Functional Requirements

- **NFR-01 — Responsividade:** fluxos essenciais devem funcionar em larguras de 320 px a 1920 px, sem rolagem horizontal. Validar em 320 px, 768 px e 1920 px, cobrindo busca, seleção, consulta e unidade.
- **NFR-02 — Usabilidade:** em teste moderado com cinco pessoas representativas, pelo menos quatro devem buscar uma cidade, consultar as condições e localizar a previsão em até 30 segundos, sem ajuda. Registrar falhas por etapa.
- **NFR-03 — Desempenho:** p95 de até 3 segundos da seleção da cidade à exibição de condições e previsão em 100 execuções, incluindo latência do provedor. Executar em um dispositivo móvel intermediário e um desktop de referência, com rede simulada de 10 Mbps down, 5 Mbps up e 100 ms RTT; registrar modelos e versões no relatório de release.
- **NFR-04 — Disponibilidade e resiliência:** manter disponibilidade mensal mínima de 99,5%, medida por verificações sintéticas a cada 5 minutos. Cada verificação conta como sucesso somente se condições e previsão utilizáveis forem exibidas dentro do limite de atualidade. Falhas do provedor e manutenção contam como indisponibilidade.
- **NFR-05 — Acessibilidade:** conformidade WCAG 2.2 nível AA nos fluxos essenciais. Gate: análise axe sem violações críticas/sérias e revisão manual de teclado, foco, nomes e estados acessíveis, contraste e zoom a 200%; documentar exceções antes do release.
- **NFR-06 — Compatibilidade:** suportar as duas versões estáveis mais recentes de Chrome, Edge e Firefox em Windows/macOS; Safari nas duas versões estáveis mais recentes de macOS e iOS; e Chrome nas duas versões estáveis mais recentes de Android. Executar os fluxos essenciais nessa matriz.
- **NFR-07 — Privacidade e segurança:** servir a aplicação por HTTPS. Não solicitar localização nem criar conta no escopo inicial. Não persistir buscas no servidor. Informar que o nome da cidade e metadados de conexão são enviados ao Open-Meteo e não transmitir dados pessoais adicionais.
- **NFR-08 — Atualidade dos dados:** exibir o momento de recebimento da última resposta válida em pt-BR. A idade de cache é calculada desde esse recebimento: até 60 minutos, inclusive, é atual; acima de 60 minutos, é desatualizado. Isso não afirma a idade de origem dos dados do provedor.
- **NFR-09 — Idioma e formatos:** interface, mensagens, condições meteorológicas, nomes de países e unidades em pt-BR; datas, horários e números em formato pt-BR. Previsão no fuso IANA da cidade; sem fuso, aplicar AC-03.9.
- **NFR-10 — Integração do provedor:** requisições diretas do navegador ao Open-Meteo no MVP. Antes do release, validar limites, cobertura, disponibilidade dos campos requeridos, termos e atribuição; interromper o lançamento se qualquer condição impedir o uso previsto.

## Edge Cases
- **Cidade inexistente:** se o nome pesquisado não corresponder a uma cidade reconhecida, informar que a cidade não foi encontrada, manter a consulta editável e não iniciar uma consulta meteorológica.
- **Input vazio:** se o campo estiver vazio ou contiver apenas espaços, não enviar requisição e solicitar que a pessoa usuária informe uma cidade.
- **Caracteres especiais:** aceitar nomes com acentos, espaços, hífens e apóstrofos; remover espaços excedentes apenas nas extremidades e codificar o texto para a busca. Se não houver correspondência, exibir o estado de nenhum resultado.
- **Falha de API:** diante de erro HTTP, resposta inválida ou falha de rede, exibir indisponibilidade e oferecer nova tentativa. Não apresentar dados antigos como atuais; cache mantido deve mostrar seu horário e estado de atualidade.
- **Timeout:** ao exceder o limite provisório de 8 segundos, interromper a espera, informar que a consulta demorou demais e oferecer nova tentativa. Uma resposta tardia da tentativa expirada não deve substituir o resultado de uma tentativa mais recente.
- **Geocoding sem resultados:** quando o geocodificador responder com sucesso sem localidades, informar que nenhum resultado foi encontrado, preservar o texto pesquisado e não chamar o serviço de previsão.
- **Resposta parcial:** exibir os campos e dias disponíveis, identificar cada campo ou data ausente e não inferir, inventar ou repetir valores. Se não houver dados utilizáveis, exibir indisponibilidade e permitir nova tentativa.

- Cidades homônimas, resultado sem dados suficientes de região/país ou localidade sem cobertura meteorológica.
- Mudança de dia, mês ou ano no fuso local da cidade; diferença entre fuso da cidade e fuso do dispositivo; transição de horário de verão onde aplicável.
- Previsão ausente para uma data dentro do intervalo solicitado, ou provedor retorna menos de cinco datas.
- Temperatura negativa, conversão entre unidades e arredondamento; troca da unidade durante carregamento ou com consulta indisponível.
- Janela estreita ou conteúdo ampliado por zoom: controles e resultados devem continuar acessíveis sem rolagem horizontal da página.
- Navegação somente por teclado, foco durante carregamento/erro e anúncio acessível da mudança de resultados ou unidade.

## Assumptions

- A aplicação é web responsiva; PWA e aplicativos nativos não estão confirmados.
- A pessoa usuária pesquisa e seleciona uma cidade antes de consultar os dados.
- Open-Meteo é a fonte meteorológica escolhida para a primeira versão, condicionada à validação de cobertura e termos.
- A busca é global e só apresenta localidades retornadas pelo geocodificador; não há promessa de cobertura universal.
- A previsão é diária e abrange hoje mais os quatro dias seguintes.
- Celsius é a unidade inicial; alternar unidades muda a apresentação das temperaturas, não os dados de origem.
- A interface, mensagens e condições meteorológicas são apresentadas em pt-BR; as datas da previsão usam o fuso IANA fornecido para a cidade e nunca o fuso do dispositivo como substituto.
- Não haverá autenticação nem persistência de dados no servidor na primeira versão.
- A unidade escolhida é persistida localmente no mesmo navegador e perfil, sem sincronização entre dispositivos.
- A consulta de cidade é enviada ao Open-Meteo; essa transferência e o tratamento de dados pelo provedor devem ser informados na política de privacidade.
- Personas e métricas pessoais do discovery são hipóteses, não evidência de pesquisa com usuários.
- Localização, favoritos, histórico, alertas e operação offline não foram aprovados para o escopo inicial.

## Risks

| Risco | Probabilidade inicial | Impacto | Mitigação / condição de saída |
|---|---|---|---|
| Falha, limite ou mudança na API meteorológica | Média | Alto | Validar limites e versionamento; aprovar o timeout provisório, política de cache, tratamento de erro e monitoramento compatíveis com os termos do provedor. |
| Cobertura insuficiente ou dados de origem desatualizados na região atendida | Média | Alto | Validar a cobertura e os timestamps de origem fornecidos pelo provedor; o TTL de cache de 60 minutos não substitui essa validação. Exibir o momento de recebimento da resposta sem alegar que é o horário de geração do dado. |
| Busca ambígua seleciona a cidade errada | Média | Alto | Apresentar contexto geográfico nos resultados e validar cenários de homônimos e ausência de contexto. |
| Campos requeridos ausentes na cobertura do provedor | Média | Alto | Validar temperatura, condição, mínima/máxima diária e fuso na região atendida antes do release; não substituir campos ausentes por dados inferidos. |
| Falhas de rede deixam a tela sem orientação ou fazem cache parecer atual | Média | Médio/alto | Definir estados de erro, nova tentativa e identificação/expiração de cache. |
| Desempenho inadequado em redes ou dispositivos móveis | Média | Alto | Aprovar ambiente de referência e medir a meta de percentil após selecionar uma cidade. |
| Incompatibilidade em navegadores/dispositivos relevantes | Média | Alto | Aprovar matriz de suporte e testar fluxos essenciais nessa matriz. |
| Barreiras de acessibilidade | Média | Alto | Ratificar WCAG 2.2 AA como meta e validar fluxo por teclado, contraste e tecnologia assistiva. |
| Termos, atribuição ou custos do provedor incompatíveis com o produto | Baixa/média | Alto | Revisar condições de uso e atribuição antes do lançamento e definir alternativa caso não sejam compatíveis. |
| Coleta de localização ou telemetria sem necessidade clara | Baixa/média | Alto | Manter localização fora do escopo atual; se reconsiderada, exigir consentimento explícito e minimização de dados. |
| Formatos, unidades ou horários inconsistentes para pt-BR | Média | Médio | Aprovar unidade, escopo de conversão, formatos e regra de fuso; validar com usuários. |
| Ausência de métricas de sucesso e volume esperado | Alta | Médio/alto | Definir indicadores, volume e consultas simultâneas antes de decisões operacionais; instrumentar de forma proporcional e respeitando privacidade. |

As probabilidades são preliminares e devem ser revistas após as decisões pendentes.

## Out of Scope

- Aplicativos nativos para iOS/Android e recursos específicos de PWA.
- Detecção automática de localização; o acesso à localização do dispositivo não é solicitado.
- Contas, login, perfis, favoritos, histórico de buscas ou persistência/sincronização de cidades no servidor.
- Comparação simultânea de cidades, mapas, radar, exportação ou compartilhamento de previsões.
- Alertas meteorológicos, inclusive severos, notificações push e recomendações de segurança, saúde, roupas ou atividades.
- Previsões horárias, subdiárias ou para mais de cinco dias.
- Variáveis além de temperatura e condição atuais e data local, mínima, máxima e condição da previsão diária; por exemplo, umidade, vento, precipitação, pressão, visibilidade, índice UV, qualidade do ar, sensação térmica e nascer/pôr do sol.
- Atualização automática periódica ou em segundo plano; a atualização manual permanece incluída.
- Provedor alternativo, fallback para outra API ou agregação de múltiplas fontes.
- Operação offline garantida. O último resultado em cache pode ser exibido na falha de rede, sempre identificado com o momento de recebimento e como desatualizado quando exceder 60 minutos.
- Busca por CEP, coordenadas ou identificadores diferentes do nome da cidade; coordenadas retornadas pelo geocodificador são usadas internamente para a consulta meteorológica.
- Idiomas diferentes de pt-BR.
- Publicidade, rastreamento para fins publicitários e analítica de produto além das métricas do teste de usabilidade NFR-02.

## Open Questions

### Verificações antes do release

- Confirmar termos, atribuição, limites e política de retenção do Open-Meteo, inclusive tratamento do endereço IP e da consulta de cidade (A-16, A-26).
- Confirmar que os campos do baseline estão disponíveis para as localidades retornadas e que a busca global está dentro da cobertura permitida (A-03, A-11, A-13, A-14).
- Conferir consumo esperado contra os limites publicados pelo provedor e ativar monitoramento de erros/timeout (A-27).

Essas verificações não exigem novas decisões para implementar o MVP; são condições de conformidade e operação para liberar tráfego real. Busca global dentro dos resultados do provedor, sem promessa de cobertura universal, é o escopo de lançamento.
