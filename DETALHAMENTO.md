# Detalhamento da atividade — Service Desk

## Objetivo

Corrigir cinco falhas funcionais no aplicativo de chamados, concentrar as correções na branch `staging` e registrar o processo de análise assistida por IA e versionamento.

## Bugs identificados e correções

1. **Busca não localizava o solicitante.** O campo de busca indicava que aceitava ID, título ou solicitante, mas o filtro considerava somente ID e título. O filtro agora também compara o nome do solicitante, sem diferenciar maiúsculas de minúsculas.
2. **Alterações do analista desapareciam ao recarregar.** A função de atualização alterava status, prioridade, correção e histórico apenas em memória. A chamada a `saveTickets()` foi incluída após a atualização para persistir esses dados no `localStorage`, inclusive o fechamento como Resolvido.
3. **Abertura falhava quando a base estava vazia.** `Math.max()` sem valores retornava `-Infinity`, produzindo um ID inválido. O cálculo agora usa 1000 como piso, gerando o ID 1001 quando não há chamados.
4. **Dados locais em formato inválido derrubavam o aplicativo.** `loadTickets()` aceitava qualquer JSON válido como lista de chamados. Um objeto ou uma lista com registros inválidos causava erro ao filtrar ou renderizar. Agora o formato e os campos dos registros são validados antes do uso; dados inválidos geram erro explícito no console e a tela usa os dados de demonstração.
5. **“Limpar base local” restaurava chamados de demonstração.** A ação substituía os chamados existentes pelos exemplos em vez de deixar a base vazia. Agora salva uma lista vazia e a mantém vazia após recarregar.

## Prompts estruturados para reproduzir a análise com IA

Abaixo estão prompts reutilizáveis para orientar a análise e cada correção com a IA do VS Code. As conclusões deste relatório foram conferidas no código e no fluxo local.

### Análise

> Analise o aplicativo de Service Desk e procure falhas funcionais na abertura, busca, atualização/fechamento, carregamento do localStorage e limpeza da base. Para cada problema, explique o comportamento esperado, a causa no código e um modo de reproduzir. Não altere arquivos nesta etapa e não afirme problemas sem evidência.

### Correção da busca

> Corrija somente a busca de chamados para que o campo anunciado como “ID, título ou solicitante” também localize pelo nome do solicitante, sem diferenciar maiúsculas de minúsculas. Preserve os demais critérios e faça uma alteração mínima. Explique como verificar o comportamento.

### Persistência do fechamento

> Corrija somente a atualização feita pelo analista para que status, prioridade, correção e histórico sejam persistidos no localStorage ao salvar. Preserve o fluxo atual e explique como verificar que o status Resolvido continua após recarregar.

### Base vazia

> Corrija somente a geração de ID para permitir abrir o primeiro chamado quando a lista armazenada estiver vazia. Mantenha IDs crescentes a partir do padrão atual e não altere outras funcionalidades.

### Formato dos dados locais

> Corrija o carregamento de chamados para que JSON válido que não corresponda a uma lista de registros válidos não derrube a aplicação. Preserve o fallback atual dos dados de demonstração, registre o erro explicitamente e teste com um objeto, uma lista com item inválido e JSON malformado.

### Limpeza da base

> Corrija somente a ação “Limpar base local” para deixar a lista de chamados vazia, persistir esse estado e mantê-lo após recarregar. Não restaurar os dados de demonstração.

### Relatório

> Resuma os cinco bugs confirmados, causa e correção, os prompts usados e os commits realizados. Registre apenas verificações executadas; não invente resultados.

## Verificação

`node --check service-desk/app.js` passou. Um teste local em Node.js com DOM e `localStorage` simulados verificou: ID 1001 na primeira abertura; busca por ID, título e solicitante; persistência de status, prioridade, correção e histórico após recarga; limpeza persistente da base; e recuperação com erro no console para objeto, lista com registro inválido e JSON malformado. Resultado: **OK** nos cinco cenários.

## Versionamento

As correções foram isoladas em commits separados na branch `staging`:

- `41abec1` — `fix: permite buscar chamados por solicitante`
- `6278254` — `fix: persiste alterações dos chamados`
- `7553b17` — `fix: gera ID ao abrir chamado sem registros`

As três correções iniciais foram integradas à `main` no commit `b1caa3c`. As duas correções adicionais foram registradas no commit `f23756a` (`fix: corrige validação e limpeza da base local`), e a atualização deste relatório foi registrada no commit `299529c` (`docs: atualiza status do merge de staging`). Ambos foram publicados em `origin/staging` e integrados à `main` nesta revisão. Antes desta integração, a `main` estava no commit `8dfeb59`.
