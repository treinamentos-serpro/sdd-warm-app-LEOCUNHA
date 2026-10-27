# Backlog de Implementação: Weather App

**Entrada:** [Plano técnico](../plans/weather-app-plan.md)  
**Escopo:** tarefas do MVP, ordenadas por camada e dependência. Gates externos continuam bloqueantes para release conforme a spec.

Cada tarefa separa uma responsabilidade implementável. Os arquivos são prováveis; cada implementação fica em um arquivo e cada teste em uma suíte focada sempre que possível.
## Prioridade, tamanho e sequência sugerida

Prioridade expressa a ordem recomendada de execução: **P0** viabiliza a primeira fatia funcional visível; **P1** completa o MVP e sua validação funcional; **P2** cobre hardening e gates operacionais. P2 não significa dispensável: requisitos da spec marcados como gates continuam bloqueando o release até aprovação.

Tamanho relativo: **P** pequeno e localizado; **M** envolve coordenação/moderada complexidade; **G** cruza plataformas, operação ou vários cenários de validação. São estimativas, não prazos.

| ID | Prioridade | Tamanho | ID | Prioridade | Tamanho |
|---|---|---|---|---|---|
| T-01 | P0 | P | T-29 | P1 | M |
| T-02 | P0 | M | T-30 | P0 | P |
| T-03 | P1 | M | T-31 | P1 | M |
| T-04 | P1 | P | T-32 | P1 | P |
| T-05 | P1 | P | T-33 | P0 | P |
| T-06 | P0 | P | T-34 | P1 | P |
| T-07 | P1 | M | T-35 | P1 | P |
| T-08 | P1 | P | T-36 | P1 | P |
| T-09 | P0 | M | T-37 | P0 | P |
| T-10 | P1 | M | T-38 | P1 | M |
| T-11 | P0 | M | T-39 | P1 | M |
| T-12 | P1 | M | T-40 | P2 | P |
| T-13 | P1 | P | T-41 | P2 | M |
| T-14 | P0 | P | T-42 | P2 | M |
| T-15 | P0 | M | T-43 | P2 | G |
| T-16 | P1 | M | T-44 | P2 | P |
| T-17 | P1 | M | T-45 | P2 | M |
| T-18 | P1 | P | T-46 | P2 | M |
| T-19 | P0 | P | T-47 | P2 | M |
| T-20 | P0 | M | T-48 | P2 | M |
| T-21 | P0 | P | T-49 | P2 | M |
| T-22 | P0 | P | T-50 | P2 | G |
| T-23 | P1 | P | T-51 | P2 | P |
| T-24 | P1 | P | T-52 | P2 | M |
| T-25 | P1 | P | T-53 | P1 | M |
| T-26 | P1 | P | T-54 | P1 | M |
| T-27 | P1 | P | T-55 | P1 | P |
| T-28 | P0 | M | T-56 | P1 | M |

### Fatias verticais recomendadas

| Ordem | Resultado demonstrável | Tarefas na sequência sugerida |
|---|---|---|
| 1 | Busca utilizável: enviar consulta, ver resultados e selecionar cidade; clima ainda não é solicitado antes da seleção. | T-01, T-02, T-06, T-09, T-11, T-14, T-15, T-19, T-20, T-21, T-22, T-28, T-30, T-33, T-37 |
| 2 | Selecionar cidade exibe condições atuais, timestamp/cache e estados de falha; forecast usa a resposta já normalizada. | T-03, T-05, T-07, T-08, T-10, T-12, T-16, T-23, T-25, T-27, T-29, T-31, T-34, T-53 |
| 3 | Previsão de cinco dias aparece junto às condições, com lacunas e ausência de IANA tratadas. | T-17, T-26, T-35, T-54 |
| 4 | Celsius/Fahrenheit atualiza current e todos os dias sem nova chamada; preferência persiste. | T-04, T-13, T-18, T-24, T-32, T-36, T-55, T-56 |
| 5 | Fluxo completo validado em navegadores e viewport mobile, com APIs interceptadas. | T-38, T-39 |
| 6 | Hardening e gates operacionais, mantendo bloqueios de release explícitos. | T-40–T-52 |

Executar uma fatia até seu critério de aceite antes de avançar; os IDs são referências estáveis, e esta tabela é a ordem sugerida de entrega.

## Entrega 1 — Tipos

### T-01 — Definir contratos compartilhados
**Descrição:** Declarar os modelos de domínio e estados assíncronos.
**Critérios de aceite:**
- Exporta `Unit`, `City`, `CurrentWeather`, `ForecastDay`, `WeatherData` e `AsyncStatus`.
- Campos opcionais de API são `null`; existem cinco slots diários e status independentes para busca, current e forecast.
**Dependências:** —
**Arquivos prováveis:** `src/types/weather.ts`
**Tipo:** Data
**Rastreabilidade:** FR-01–FR-04, AC-01.13, AC-02.5, AC-03.4–6, AC-04.1–7.

## Entrega 2 — Funções puras

### T-02 — Normalizar resultado de geocoding
**Descrição:** Mapear um resultado bruto para `City`.
**Critérios de aceite:**
- Mapeia id, nome, região, país/código, coordenadas e timezone.
- Campos opcionais ausentes viram `null`; coordenadas não finitas não habilitam seleção.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/openMeteoMappers.ts`
**Tipo:** Data
**Rastreabilidade:** FR-01, AC-01.2, AC-01.6, AC-01.12–13.

### T-03 — Normalizar payload de forecast
**Descrição:** Mapear current e arrays daily em modelos de domínio.
**Critérios de aceite:**
- Mapeia temperatura/código atuais e alinha arrays diários por data.
- Mantém os cinco slots; data ausente preserva os campos meteorológicos como `null`.
- Valores parciais não são inferidos nem copiados de outra data.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/openMeteoMappers.ts`
**Tipo:** Data
**Rastreabilidade:** FR-02–FR-03, AC-02.5, AC-03.2, AC-03.4, AC-03.6.

### T-04 — Converter e formatar temperaturas
**Descrição:** Criar funções puras de conversão e formatação pt-BR.
**Critérios de aceite:**
- Converte conforme as fórmulas da spec, mantendo Celsius como valor de origem.
- Exibe uma casa decimal e arredonda empate para longe de zero.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/temperature.ts`
**Tipo:** Data
**Rastreabilidade:** FR-04, AC-04.1–5.

### T-05 — Mapear códigos WMO
**Descrição:** Converter códigos conhecidos em descrições de condição pt-BR.
**Critérios de aceite:**
- Códigos suportados retornam descrições pt-BR definidas.
- Código ausente ou desconhecido resulta em indisponibilidade, sem inferência.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/weatherCodes.ts`
**Tipo:** Data
**Rastreabilidade:** FR-02–FR-03, AC-02.1, AC-02.5, AC-03.2, AC-03.6, NFR-09.

### T-06 — Localizar nomes de países
**Descrição:** Formatar país em pt-BR a partir do código ISO.
**Critérios de aceite:**
- Código ISO reconhecido é localizado em pt-BR.
- Código ausente/inválido não gera exceção e permite mostrar o contexto restante.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/localization.ts`
**Tipo:** Data
**Rastreabilidade:** FR-01, AC-01.2, NFR-09.

### T-07 — Gerar datas civis locais
**Descrição:** Criar hoje e os quatro dias seguintes usando timezone IANA explícito.
**Critérios de aceite:**
- Recebe instante e timezone como argumentos e retorna cinco datas em ordem.
- Cobre viradas de dia/mês/ano; timezone ausente/inválido nunca usa o timezone do dispositivo.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/forecastDates.ts`
**Tipo:** Data
**Rastreabilidade:** FR-03, AC-03.1, AC-03.3–4, AC-03.9, NFR-09.

### T-08 — Calcular atualidade do cache
**Descrição:** Calcular idade e frescor recebendo `receivedAt` e instante atual.
**Critérios de aceite:**
- Até 60 minutos inclusive é atual; acima de 60 minutos é desatualizado.
- Função não consulta relógio global nem inicia requests.
**Dependências:** T-01
**Arquivos prováveis:** `src/lib/freshness.ts`
**Tipo:** Data
**Rastreabilidade:** FR-02, AC-02.3–4, AC-02.8, AC-02.11, NFR-08.

## Entrega 3 — Services

### T-09 — Implementar service de geocoding
**Descrição:** Encapsular URL, fetch e tratamento da busca Open-Meteo.
**Critérios de aceite:**
- Envia consulta aparada/codificada, `count=10`, idioma pt e JSON; preserva a ordem.
- Trata HTTP, rede, payload inválido, timeout de 8 segundos e `AbortSignal`.
- Retorna dados normalizados via T-02 sem controlar estado React.
**Dependências:** T-02, T-06
**Arquivos prováveis:** `src/services/openMeteoService.ts`
**Tipo:** Data
**Rastreabilidade:** FR-01, AC-01.1–14, NFR-09–10.

### T-10 — Implementar service de forecast
**Descrição:** Encapsular consultas de condições atuais e previsão diária.
**Critérios de aceite:**
- Com IANA, solicita current/daily em Celsius, timezone da cidade e cinco dias.
- Sem IANA, solicita somente current em UTC, sem daily.
- Aplica timeout de 8 segundos, valida HTTP/payload e retorna `WeatherData` via T-03 com `receivedAt` local.
**Dependências:** T-03, T-05, T-07, T-08
**Arquivos prováveis:** `src/services/openMeteoService.ts`
**Tipo:** Data
**Rastreabilidade:** FR-02–FR-03, AC-02.1–13, AC-03.1–9, NFR-08–10.

## Entrega 4 — Hooks

### T-11 — Orquestrar busca e seleção
**Descrição:** Implementar busca explícita e seleção vigente no hook.
**Critérios de aceite:**
- Busca vazia não chama service; loading bloqueia envios duplicados.
- Expõe `idle`, `loading`, `success`, `empty` e `error`; preserva consulta em vazio/falha e permite retry.
- Só seleciona coordenadas finitas e ignora resposta obsoleta.
**Dependências:** T-01, T-09
**Arquivos prováveis:** `src/hooks/useWeatherApp.ts`
**Tipo:** UI
**Rastreabilidade:** FR-01, AC-01.3–5, AC-01.7–10, AC-01.13–14.

### T-12 — Orquestrar clima e cache por cidade
**Descrição:** Coordenar forecast, atualização explícita e respostas concorrentes.
**Critérios de aceite:**
- Current e forecast têm status independentes; sem IANA current pode ter sucesso e forecast fica indisponível.
- Falha mantém cache somente da mesma cidade e preserva `receivedAt`; não há refresh automático.
- Resposta tardia ou de outra cidade não altera dados vigentes.
**Dependências:** T-01, T-08, T-10, T-11
**Arquivos prováveis:** `src/hooks/useWeatherApp.ts`
**Tipo:** UI
**Rastreabilidade:** FR-02–FR-03, AC-02.8–13, AC-03.7–9, NFR-08.

### T-13 — Gerenciar unidade persistida
**Descrição:** Ler, alterar e persistir Celsius/Fahrenheit no perfil local.
**Critérios de aceite:**
- Usa Celsius sem preferência e restaura somente valores válidos do `localStorage`.
- Storage indisponível mantém alternância em memória; alternar não chama services.
**Dependências:** T-01, T-04
**Arquivos prováveis:** `src/hooks/useWeatherApp.ts`
**Tipo:** UI
**Rastreabilidade:** FR-04, AC-04.1–7.

## Entrega 5 — Componentes

### T-14 — Criar estilos base responsivos
**Descrição:** Definir a base Tailwind dark glassmorphism e foco global.
**Critérios de aceite:**
- Regras globais não geram overflow horizontal em 320 px.
- Foco de teclado permanece visível nos controles.
**Dependências:** T-01
**Arquivos prováveis:** `src/index.css`
**Tipo:** UI
**Rastreabilidade:** NFR-01, NFR-05.

### T-15 — Implementar busca de cidade
**Descrição:** Renderizar input, envio e resultados por props do hook.
**Critérios de aceite:**
- Exibe loading, vazio e erro; retry funciona por teclado.
- Mostra região/país; sem contexto, mostra coordenadas com duas casas.
- Resultado sem coordenadas é desabilitado; componente não acessa services.
**Dependências:** T-11, T-14
**Arquivos prováveis:** `src/components/CitySearch.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-01, AC-01.1–14, NFR-05, NFR-09.

### T-16 — Implementar condições atuais
**Descrição:** Renderizar localidade, condição, temperatura, timestamp e cache.
**Critérios de aceite:**
- Campo atual ausente exibe `Indisponível` sem ocultar campos disponíveis.
- Timestamp usa fuso da cidade ou UTC identificado sem IANA.
- Cache é identificado, informa horário e fica desatualizado somente acima de 60 minutos.
- Controle acessível `Atualizar agora` solicita nova consulta para a cidade selecionada.
**Dependências:** T-04, T-05, T-08, T-12, T-14
**Arquivos prováveis:** `src/components/CurrentWeather.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-02, AC-02.1–9, AC-02.11–13, NFR-08–09.

### T-17 — Implementar previsão diária
**Descrição:** Renderizar cinco slots de datas locais e estados indisponíveis.
**Critérios de aceite:**
- Exibe cinco datas em ordem com mínimo, máximo e condição associados.
- Slot ausente exibe `Previsão indisponível`; campo ausente exibe `Indisponível`.
- Cidade selecionada e horário de recebimento ficam visíveis junto à previsão.
- Sem IANA informa indisponibilidade e não usa timezone do dispositivo.
**Dependências:** T-05, T-07, T-12, T-14
**Arquivos prováveis:** `src/components/DailyForecast.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-03, AC-03.1–9, NFR-09.

### T-18 — Implementar controle de unidade
**Descrição:** Renderizar controle acessível Celsius/Fahrenheit.
**Critérios de aceite:**
- Mouse, Enter e Espaço alteram unidade e estado acessível.
- Temperaturas são identificadas na unidade selecionada; datas/condições não mudam.
- Alternância não dispara request.
**Dependências:** T-04, T-13, T-14
**Arquivos prováveis:** `src/components/TemperatureUnit.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-04, AC-04.1–7, NFR-05.

### T-19 — Implementar divulgação do provedor
**Descrição:** Apresentar informação de privacidade e atribuição aprovada.
**Critérios de aceite:**
- Informa envio do nome da cidade e metadados de conexão ao Open-Meteo.
- Não afirma localização automática, conta ou transmissão de dados pessoais adicionais.
- Atribuição segue os termos confirmados no gate de release.
**Dependências:** T-14
**Arquivos prováveis:** `src/components/ProviderDisclosure.tsx`
**Tipo:** UI
**Rastreabilidade:** NFR-07, NFR-10.

## Entrega 6 — Integração

### T-20 — Montar e conectar a tela React
**Descrição:** Montar a entrada React e entregar a primeira fatia visível de busca/seleção.
**Critérios de aceite:**
- Vite monta `App` sem erro; campo, envio, loading, resultados, vazio e erro/retry de busca estão conectados.
- Geocoding ocorre após envio explícito; forecast não é chamado antes da seleção de cidade.
- A tela de busca segue responsiva e acessível por teclado.
**Dependências:** T-11, T-14, T-15, T-19
**Arquivos prováveis:** `src/main.tsx`, `src/App.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-01, AC-01.3–5, AC-01.7–14, NFR-01, NFR-05.

### T-53 — Integrar condições atuais ao App
**Descrição:** Conectar seleção, service de forecast e componente de condições à primeira fatia de busca.
**Critérios de aceite:**
- Selecionar cidade inicia forecast e exibe suas condições atuais com timestamp.
- Sem IANA, condições atuais aparecem com horário UTC e a previsão permanece indisponível.
**Dependências:** T-20, T-12, T-16
**Arquivos prováveis:** `src/App.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-02, AC-02.1–9, AC-02.12–13.

### T-54 — Integrar previsão diária ao App
**Descrição:** Conectar os cinco slots da previsão à tela após a fatia de condições atuais.
**Critérios de aceite:**
- Previsão aparece para a cidade selecionada com cinco datas no timezone IANA.
- Slot/dado ausente e cidade sem IANA apresentam os estados previstos, sem usar timezone do dispositivo.
**Dependências:** T-53, T-17
**Arquivos prováveis:** `src/App.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-03, AC-03.1–9.

### T-55 — Integrar alternância de unidade ao App
**Descrição:** Conectar o controle persistido à apresentação de todas as temperaturas.
**Critérios de aceite:**
- Alternar Celsius/Fahrenheit atualiza condições atuais e todos os dias sem nova chamada.
- Datas, descrições e seleção permanecem inalteradas; a preferência persiste após recarga.
**Dependências:** T-54, T-18
**Arquivos prováveis:** `src/App.tsx`
**Tipo:** UI
**Rastreabilidade:** FR-04, AC-04.1–7.

## Entrega 7 — Testes

### T-21 — Completar setup do Vitest
**Descrição:** Criar o setup referenciado pela configuração Vite.
**Critérios de aceite:**
- Registra `jest-dom`, cleanup e reset de mocks.
- `pnpm exec vitest run --passWithNoTests` inicia sem erro de setup.
**Dependências:** T-01
**Arquivos prováveis:** `tests/setup.ts`
**Tipo:** Infra
**Rastreabilidade:** suporte às verificações automatizadas de AC-01–AC-04.

### T-22 — Testar mapper de geocoding
**Descrição:** Cobrir normalização de resultados de cidade.
**Critérios de aceite:**
- Fixtures verificam todos os campos, opcionais ausentes e coordenadas inválidas.
- Resultado não selecionável e contexto geográfico permanecem corretos.
**Dependências:** T-02, T-21
**Arquivos prováveis:** `tests/unit/geocodingMapper.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-01.2, AC-01.6, AC-01.12–13.

### T-23 — Testar mapper de forecast
**Descrição:** Cobrir transformação de current/daily em `WeatherData`.
**Critérios de aceite:**
- Fixtures verificam alinhamento por data, cinco slots e campos `null` para lacunas.
- Dados ausentes não são deslocados, inferidos ou copiados.
**Dependências:** T-03, T-21
**Arquivos prováveis:** `tests/unit/forecastMapper.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-02.5, AC-03.2, AC-03.4, AC-03.6.

### T-24 — Testar conversão e arredondamento
**Descrição:** Validar conversão e apresentação das temperaturas.
**Critérios de aceite:**
- Testa os exemplos da spec, incluindo negativos e `12,25 °C`.
- Verifica uma casa decimal e empate para longe de zero.
**Dependências:** T-04, T-21
**Arquivos prováveis:** `tests/unit/temperature.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-04.1–4.

### T-25 — Testar códigos WMO e localização
**Descrição:** Verificar condições e nomes de país pt-BR.
**Critérios de aceite:**
- Casos cobrem código conhecido/desconhecido e ISO reconhecido/ausente.
- Campo desconhecido resulta em indisponibilidade sem exceção.
**Dependências:** T-05, T-06, T-21
**Arquivos prováveis:** `tests/unit/weatherLocalization.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-01.2, AC-02.1, AC-03.2, NFR-09.

### T-26 — Testar geração das cinco datas
**Descrição:** Validar calendário local e lacunas por data.
**Critérios de aceite:**
- Relógio fixo cobre virada de dia, mês e ano no IANA especificado.
- Retorna exatamente cinco datas ordenadas e não usa timezone do dispositivo.
**Dependências:** T-07, T-21
**Arquivos prováveis:** `tests/unit/forecastDates.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-03.1, AC-03.3–4, AC-03.9.

### T-27 — Testar atualidade do cache
**Descrição:** Verificar cálculo de frescor com relógio injetado.
**Critérios de aceite:**
- 60 minutos é atual; 60 minutos e 1 segundo é desatualizado.
- Cálculo não cria timer nem request.
**Dependências:** T-08, T-21
**Arquivos prováveis:** `tests/unit/freshness.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-02.3–4, AC-02.11, NFR-08.

### T-28 — Testar service de geocoding
**Descrição:** Validar requisições e falhas usando `fetch` mockado.
**Critérios de aceite:**
- Testa texto especial, count 10, ordem e resposta vazia.
- Testa HTTP/rede/JSON inválido, timeout de 8 segundos, abort e resposta tardia.
**Dependências:** T-09, T-21
**Arquivos prováveis:** `tests/unit/geocodingService.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-01.1, AC-01.3–5, AC-01.8–11, AC-01.14.

### T-29 — Testar service de forecast
**Descrição:** Validar modos IANA/current-only UTC usando `fetch` mockado.
**Critérios de aceite:**
- Verifica parâmetros current/daily/five-day e modo UTC sem daily.
- Testa HTTP 503, rede, payload inválido, timeout, resposta parcial e captura local de `receivedAt`.
**Dependências:** T-10, T-21
**Arquivos prováveis:** `tests/unit/forecastService.test.ts`
**Tipo:** Test
**Rastreabilidade:** AC-02.1–8, AC-02.13, AC-03.6–9.

### T-30 — Testar hook de busca
**Descrição:** Cobrir estados e concorrência do fluxo de geocoding.
**Critérios de aceite:**
- Testa input vazio, estados explícitos, bloqueio de duplicatas, retry e texto preservado.
- Resposta antiga não substitui resultado da tentativa atual.
**Dependências:** T-11, T-28
**Arquivos prováveis:** `tests/unit/useWeatherSearch.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-01.3–5, AC-01.7, AC-01.10, AC-01.14.

### T-31 — Testar hook meteorológico
**Descrição:** Cobrir seleção, refresh, cache e identidade de cidade.
**Critérios de aceite:**
- Testa refresh manual, ausência de refresh automático, cache mesma cidade e status separados sem IANA.
- Resposta tardia da cidade anterior não altera o estado vigente.
**Dependências:** T-12, T-29, T-27
**Arquivos prováveis:** `tests/unit/useWeatherData.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-02.8–13, AC-03.7–9, NFR-08.

### T-32 — Testar preferência de unidade no hook
**Descrição:** Verificar persistência e fallback em memória.
**Critérios de aceite:**
- Testa default Celsius, restauração Fahrenheit e storage indisponível.
- Alternância não chama os services.
**Dependências:** T-13, T-24
**Arquivos prováveis:** `tests/unit/useTemperatureUnit.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-04.1–4, AC-04.7.

### T-33 — Testar componente de busca
**Descrição:** Validar estados e interação acessível de `CitySearch`.
**Critérios de aceite:**
- Renderiza loading, vazio, erro/retry, resultados ambíguos e item sem coordenadas.
- Teclado aciona busca/retry e item inválido não aciona seleção.
**Dependências:** T-15, T-30
**Arquivos prováveis:** `tests/unit/CitySearch.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-01.1–7, AC-01.12–14, NFR-05.

### T-34 — Testar componente de condições atuais
**Descrição:** Validar valores, horário, cache e retry na UI.
**Critérios de aceite:**
- Testing Library cobre `idle`, `loading`, `success`, `empty` e `error` do componente.
- Campo ausente mostra `Indisponível`; horário sem IANA aparece identificado como UTC.
- Cache vencido não é rotulado como atual; `Atualizar agora` e retry são acessíveis e disparam as ações correspondentes.
**Dependências:** T-16, T-31
**Arquivos prováveis:** `tests/unit/CurrentWeather.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-02.1–8, AC-02.13, NFR-08–09.

### T-35 — Testar componente de previsão
**Descrição:** Validar cinco slots e indisponibilidades por campo/data.
**Critérios de aceite:**
- Testing Library cobre `idle`, `loading`, `success`, `empty` e `error` do componente.
- Valores pertencem à data correspondente; slot ausente mostra `Previsão indisponível`.
- Cidade e horário de recebimento aparecem junto aos cinco slots.
- Sem IANA informa indisponibilidade sem usar datas do dispositivo.
**Dependências:** T-17, T-23, T-26
**Arquivos prováveis:** `tests/unit/DailyForecast.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-03.1–6, AC-03.9.

### T-36 — Testar controle de unidade
**Descrição:** Validar interação acessível e atualização dos valores.
**Critérios de aceite:**
- Mouse, Enter e Espaço atualizam a unidade e seu estado acessível.
- Temperaturas mudam sem alterar datas/condições nem chamar service.
**Dependências:** T-18, T-32
**Arquivos prováveis:** `tests/unit/TemperatureUnit.test.tsx`
**Tipo:** Test
**Rastreabilidade:** AC-04.2–6, NFR-05.

### T-37 — Testar integração da busca
**Descrição:** Validar a primeira fatia vertical de busca com services simulados.
**Critérios de aceite:**
- Loading, sucesso, vazio e erro/retry da busca chegam aos componentes corretos.
- Busca não chama forecast antes da seleção explícita; seleção válida dispara o callback esperado.
**Dependências:** T-20, T-30, T-33
**Arquivos prováveis:** `tests/unit/App.test.tsx`
**Tipo:** Test
**Rastreabilidade:** FR-01, AC-01.3–7, AC-01.14.

### T-56 — Testar composição final do App
**Descrição:** Cobrir current, forecast e unidade integrados com services simulados.
**Critérios de aceite:**
- Testa carregamento, vazio, erro, parcial e sucesso completo na experiência composta.
- Sem IANA mantém condições atuais em UTC e mostra previsão indisponível.
- Alternar unidade não chama services; respostas tardias não trocam a cidade exibida.
**Dependências:** T-53, T-54, T-55, T-31, T-34–T-36
**Arquivos prováveis:** `tests/unit/App.test.tsx`
**Tipo:** Test
**Rastreabilidade:** FR-01–FR-04, AC-02.9–13, AC-03.4–9, AC-04.2–7.

### T-38 — Cobrir o fluxo principal com Playwright
**Descrição:** Validar ponta a ponta com as APIs Open-Meteo interceptadas.
**Critérios de aceite:**
- Busca/seleção exibem atuais e cinco dias; atualização e unidade funcionam.
- Vazio, erro/retry, timeout e resposta tardia preservam estado correto.
**Dependências:** T-37, T-56
**Arquivos prováveis:** `tests/e2e/weather-flow.spec.ts`
**Tipo:** Test
**Rastreabilidade:** US-01–US-07, AC-01–AC-04.

### T-39 — Validar responsividade mobile
**Descrição:** Verificar fluxo essencial nos viewports de referência.
**Critérios de aceite:**
- Playwright repete o fluxo E2E principal de busca, seleção, clima, previsão e unidade em 320 px, 768 px e 1920 px, incluindo projeto/viewport mobile real.
- Em 320 px não há rolagem horizontal nem controles essenciais ocultos.
**Dependências:** T-38
**Arquivos prováveis:** `tests/e2e/responsive.spec.ts`
**Tipo:** Test
**Rastreabilidade:** NFR-01.

## Entrega 8 — Hardening

### T-40 — Adicionar integração axe ao Playwright
**Descrição:** Instalar dependência de desenvolvimento para auditoria axe.
**Critérios de aceite:**
- `@axe-core/playwright` consta em `devDependencies` e lockfile.
- Um teste de fumaça consegue analisar a página.
**Dependências:** T-38
**Arquivos prováveis:** `package.json`, `pnpm-lock.yaml`
**Tipo:** Infra
**Rastreabilidade:** NFR-05.

### T-41 — Auditar acessibilidade
**Descrição:** Automatizar axe e registrar revisão manual dos fluxos essenciais.
**Critérios de aceite:**
- axe não reporta violações críticas/sérias nos fluxos principais.
- Evidência manual cobre teclado, foco, nomes/estados, contraste e zoom 200%; exceções têm correção documentada.
**Dependências:** T-39, T-40
**Arquivos prováveis:** `tests/e2e/accessibility.spec.ts`, `docs/release/accessibility.md`
**Tipo:** Test
**Rastreabilidade:** NFR-05.

### T-42 — Configurar matriz de navegadores
**Descrição:** Preparar Playwright para a matriz de suporte da spec.
**Critérios de aceite:**
- Projetos/configuração cobrem Chrome, Edge, Firefox, Safari e Chrome Android nas plataformas indicadas por NFR-06.
- Matriz pode executar os fluxos smoke sem remover projetos desktop/mobile atuais.
**Dependências:** T-38
**Arquivos prováveis:** `playwright.config.ts`
**Tipo:** Infra
**Rastreabilidade:** NFR-06.

### T-43 — Validar compatibilidade de navegadores
**Descrição:** Executar fluxos essenciais na matriz aprovada e registrar resultados.
**Critérios de aceite:**
- Busca, seleção, condições, previsão e unidade passam nas versões/plataformas exigidas.
- Versões, ambiente, falhas e exceções ficam documentados.
**Dependências:** T-42
**Arquivos prováveis:** `docs/release/browser-compatibility.md`
**Tipo:** Test
**Rastreabilidade:** NFR-06.

### T-44 — Preparar protocolo de usabilidade
**Descrição:** Definir roteiro neutro e registro de falhas por etapa.
**Critérios de aceite:**
- Roteiro cobre busca, consulta atual e localização da previsão sem instruir a solução.
- Define cinco participantes, limite de 30 segundos e registro de sucesso/falha por etapa.
**Dependências:** T-38
**Arquivos prováveis:** `docs/release/usability-test-plan.md`
**Tipo:** Test
**Rastreabilidade:** NFR-02.

### T-45 — Executar teste moderado de usabilidade
**Descrição:** Aplicar o protocolo e registrar resultados observados.
**Critérios de aceite:**
- Pelo menos quatro de cinco participantes concluem em até 30 segundos sem ajuda.
- Tempos e falhas por etapa são registrados; resultado abaixo da meta gera correções antes do release.
**Dependências:** T-44
**Arquivos prováveis:** `docs/release/usability-results.md`
**Tipo:** Test
**Rastreabilidade:** NFR-02.

### T-46 — Preparar benchmark de desempenho
**Descrição:** Criar execução reproduzível para medir seleção até exibição.
**Critérios de aceite:**
- Executa 100 medições e calcula p95 incluindo latência do provedor.
- Usa rede de 10 Mbps down, 5 Mbps up e 100 ms RTT, em dispositivo móvel intermediário e desktop de referência.
**Dependências:** T-38
**Arquivos prováveis:** `tests/e2e/performance.spec.ts`
**Tipo:** Test
**Rastreabilidade:** NFR-03.

### T-47 — Executar benchmark e registrar resultado
**Descrição:** Rodar medições no ambiente de referência.
**Critérios de aceite:**
- Relatório contém 100 medições, p95, latência observada, modelo e versão dos dispositivos.
- Meta é p95 de até 3 segundos; desvio bloqueia release até plano de correção aprovado.
**Dependências:** T-46
**Arquivos prováveis:** `docs/release/performance-results.md`
**Tipo:** Test
**Rastreabilidade:** NFR-03.

### T-48 — Fechar gates de termos e cobertura Open-Meteo
**Descrição:** Confirmar uso permitido, campos, cobertura e limites do provedor.
**Critérios de aceite:**
- Termos, atribuição, limites, retenção/metadados e cobertura permitida estão documentados.
- Current, daily, timezone e geocoding são verificados nas localidades-alvo; condição impeditiva bloqueia tráfego.
**Dependências:** T-19, T-38
**Arquivos prováveis:** `docs/release/open-meteo-gates.md`
**Tipo:** Infra
**Rastreabilidade:** NFR-10, AC-01–AC-03.

### T-49 — Verificar privacidade e HTTPS
**Descrição:** Fechar divulgação e transporte seguro no ambiente de publicação.
**Critérios de aceite:**
- Aviso corresponde à consulta e aos metadados realmente transmitidos; não há localização ou dados pessoais adicionais.
- Publicação usa HTTPS e verificação do ambiente é registrada; falha impede release.
**Dependências:** T-19, T-48
**Arquivos prováveis:** `docs/release/privacy-and-https.md`, configuração de hospedagem
**Tipo:** Infra
**Rastreabilidade:** NFR-07.

### T-50 — Configurar monitoramento sintético
**Descrição:** Agendar verificação meteorológica a cada cinco minutos.
**Critérios de aceite:**
- Só conta sucesso quando current e forecast utilizáveis respeitam a atualidade definida.
- Falha do provedor/manutenção conta como indisponibilidade e resultados podem ser agregados mensalmente.
**Dependências:** T-38, T-48, T-49
**Arquivos prováveis:** `.github/workflows/weather-synthetic.yml`
**Tipo:** Infra
**Rastreabilidade:** NFR-04, NFR-08.

### T-51 — Documentar cálculo e resposta operacional
**Descrição:** Registrar cálculo mensal, meta e procedimento de falha sintética.
**Critérios de aceite:**
- Documento define como calcular a disponibilidade mensal de 99,5% a partir das verificações.
- Falhas, manutenção e responsável pelo acompanhamento estão descritos.
**Dependências:** T-50
**Arquivos prováveis:** `docs/operations/monitoring.md`
**Tipo:** Infra
**Rastreabilidade:** NFR-04.

### T-52 — Executar gate final de qualidade
**Descrição:** Consolidar build, lint, testes e gates antes do release.
**Critérios de aceite:**
- `pnpm lint`, `pnpm build`, `pnpm test` e `pnpm test:e2e` passam.
- Resultados de NFR-01–NFR-10 estão registrados; gate externo pendente permanece bloqueante, não presumido como aprovado.
**Dependências:** T-21–T-51, T-53–T-56
**Arquivos prováveis:** `docs/release/quality-gates.md`
**Tipo:** Infra
**Rastreabilidade:** NFR-01–NFR-10.

## Matriz de rastreabilidade dos requisitos funcionais

| Requisito da spec | Tarefas de implementação | Tarefas de teste |
|---|---|---|
| **FR-01 — Buscar e selecionar cidade** | T-02, T-06, T-09, T-11, T-15, T-20 | T-22, T-28, T-30, T-33, T-37–T-39 |
| **FR-02 — Consultar condições atuais** | T-03, T-05, T-08, T-10, T-12, T-16, T-53 | T-23, T-25, T-27, T-29, T-31, T-34, T-56, T-38–T-39 |
| **FR-03 — Consultar previsão diária de cinco dias** | T-03, T-05, T-07, T-10, T-12, T-17, T-54 | T-23, T-25, T-26, T-29, T-31, T-35, T-56, T-38–T-39 |
| **FR-04 — Alternar unidade de temperatura** | T-04, T-13, T-18, T-55 | T-24, T-32, T-36, T-56, T-38–T-39 |

**Requisitos funcionais sem tarefa correspondente:** nenhum. AC-02.10 e AC-03.5 estão explicitamente cobertos pelos critérios de T-16/T-34 e T-17/T-35, respectivamente.