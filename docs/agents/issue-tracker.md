# Issue tracker: GitHub

As Issues e as especificações deste repositório vivem nas GitHub Issues. Use o CLI `gh` para todas as operações.

## Conventions

- **Create an issue**: `gh issue create --title "..." --body "..."`. Use um heredoc para corpos com várias linhas.
- **Read an issue**: `gh issue view <number> --json number,title,body,labels,comments`.
- **List issues**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` com os filtros `--label` e `--state` adequados.
- **Make an issue a sub-issue of a parent**: `gh issue create --parent <parent> ...`, ou `gh issue edit <parent> --add-sub-issue <child>` depois (`gh` 2.94+). Em versões antigas do `gh`: `gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>` (id numérico do banco, como em **Blocking** abaixo). Sem sub-issues, coloque `Part of #<parent>` no topo do corpo da Issue filha.
- **Comment on an issue**: `gh issue comment <number> --body "..."`
- **Apply / remove labels**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

O repositório é inferido a partir de `git remote -v`; o `gh` faz isso automaticamente quando executado dentro do clone.

## Pull requests as a triage surface

**PRs as a request surface: no.** _(Mude para `yes` se este repositório tratar PRs externos como pedidos de funcionalidade; a skill `/triage` lê esta opção.)_

Quando estiver em `yes`, PRs passam pelas mesmas etiquetas e estados das Issues, usando os equivalentes `gh pr`:

- **Read a PR**: `gh pr view <number> --comments` e `gh pr diff <number>` para ver o diff.
- **List external PRs for triage**: `gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`.
- **Comment / label / close**: `gh pr comment`, `gh pr edit --add-label`/`--remove-label`, `gh pr close`.

O GitHub compartilha a mesma numeração entre Issues e PRs, então um `#42` isolado pode ser qualquer um dos dois: resolva com `gh pr view 42` e, se falhar, use `gh issue view 42`.

## When a skill says "publish to the issue tracker"

Crie uma Issue no GitHub.

## When a skill says "fetch the relevant ticket"

Leia como descrito em **Read an issue** acima.

## Wayfinding operations

Usado pela skill `/wayfinder`. O **mapa** é uma única Issue cujas **Issues filhas** são os bilhetes.

- **Map**: uma única Issue com o rótulo `wayfinder:map`, guardando o corpo com Notes / Decisions-so-far / Fog. `gh issue create --label wayfinder:map`.
- **Child ticket**: uma Issue ligada ao mapa como sub-issue do GitHub (veja **Make an issue a sub-issue of a parent**). Onde sub-issues não estiverem habilitadas, coloque a Issue filha numa lista de tarefas no corpo do mapa e `Part of #<map>` no topo do corpo da filha. Rótulos: `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Depois de reivindicado, o bilhete fica atribuído a quem conduz o mapa.
- **Blocking**: as **dependências nativas de Issue** do GitHub, a representação canônica e visível na interface. Adicione a aresta com `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`, em que `<blocker-db-id>` é o **id numérico do banco** do bloqueador (`gh api repos/<owner>/<repo>/issues/<n> --jq .id`, _não_ o `#number` nem o `node_id`). O GitHub informa `issue_dependencies_summary.blocked_by` (apenas bloqueadores abertos, o portão real). Onde dependências não estiverem disponíveis, use como alternativa uma linha `Blocked by: #<n>, #<n>` no topo do corpo da filha. Um bilhete está liberado quando todos os bloqueadores estão fechados.
- **Frontier query**: liste as filhas abertas do mapa (`gh issue list --state open`, limitado às sub-issues / lista de tarefas do mapa), descarte as que têm bloqueador aberto (`issue_dependencies_summary.blocked_by > 0`, ou uma Issue aberta na linha `Blocked by`) ou responsável; a primeira na ordem do mapa vence.
- **Claim**: `gh issue edit <n> --add-assignee @me`, a primeira escrita da sessão.
- **Resolve**: `gh issue comment <n> --body "<answer>"`, depois `gh issue close <n>`, depois acrescente um ponteiro de contexto (resumo + link) em Decisions-so-far do mapa.
