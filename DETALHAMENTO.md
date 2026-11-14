# Detalhamento da atividade — Service Desk

## Objetivo

Corrigir três falhas funcionais no aplicativo de chamados, concentrar as correções na branch `staging` e registrar o processo de análise assistida por IA e versionamento.

## Bugs identificados e correções

1. **Busca não localizava o solicitante.** O campo de busca indicava que aceitava ID, título ou solicitante, mas o filtro considerava somente ID e título. O filtro agora também compara o nome do solicitante, sem diferenciar maiúsculas de minúsculas.
2. **Alterações do analista desapareciam ao recarregar.** A função de atualização alterava status, prioridade, correção e histórico apenas em memória. A chamada a `saveTickets()` foi incluída após a atualização para persistir esses dados no `localStorage`, inclusive o fechamento como Resolvido.
3. **Abertura falhava quando a base estava vazia.** `Math.max()` sem valores retornava `-Infinity`, produzindo um ID inválido. O cálculo agora usa 1000 como piso, gerando o ID 1001 quando não há chamados.

## Prompts estruturados para reproduzir a análise com IA

Abaixo estão prompts reutilizáveis para orientar a análise e cada correção com a IA do VS Code. As conclusões deste relatório foram conferidas no código e no fluxo local.

### Análise

> Analise o aplicativo de Service Desk e procure falhas funcionais na abertura, busca e atualização/fechamento de chamados. Para cada problema, explique o comportamento esperado, a causa no código e um modo de reproduzir. Não altere arquivos nesta etapa e não afirme problemas sem evidência.

### Correção da busca

> Corrija somente a busca de chamados para que o campo anunciado como “ID, título ou solicitante” também localize pelo nome do solicitante, sem diferenciar maiúsculas de minúsculas. Preserve os demais critérios e faça uma alteração mínima. Explique como verificar o comportamento.

### Persistência do fechamento

> Corrija somente a atualização feita pelo analista para que status, prioridade, correção e histórico sejam persistidos no localStorage ao salvar. Preserve o fluxo atual e explique como verificar que o status Resolvido continua após recarregar.

### Base vazia

> Corrija somente a geração de ID para permitir abrir o primeiro chamado quando a lista armazenada estiver vazia. Mantenha IDs crescentes a partir do padrão atual e não altere outras funcionalidades.

### Relatório

> Resuma os bugs confirmados, causa e correção, os prompts usados e os commits realizados. Registre apenas verificações executadas; não invente resultados.

## Verificação

Foi executada uma verificação local do código JavaScript com armazenamento inicial vazio. O fluxo abriu um chamado com ID 1001, encontrou esse chamado por nome do solicitante e salvou o status Resolvido e a correção no `localStorage`. Resultado: **OK** para os três cenários.

## Versionamento

As correções foram isoladas em commits separados na branch `staging`:

- `41abec1` — `fix: permite buscar chamados por solicitante`
- `6278254` — `fix: persiste alterações dos chamados`
- `7553b17` — `fix: gera ID ao abrir chamado sem registros`

A branch foi criada a partir da `main`. O relatório foi adicionado em commit de documentação separado. Após revisar o diff, a branch `staging` foi integrada à `main` no commit `b1caa3c`, e a `main` foi enviada ao repositório remoto.


