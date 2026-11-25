# Próximos Passos — Retomada do Desenvolvimento

> Atualizado em **25/11/2026**, com o **PR #3** aberto. Leia este arquivo inteiro antes de continuar: estado do
> projeto, o que depende do usuário, o backlog, as decisões e as armadilhas já encontradas.

---

## 1. Retomada rápida (5 minutos)

```bash
cd ~/projetos/sgc-redes-bvg
git status && git log --oneline -6          # branch e2e-ampliado (a partir de upstream/main)
gh pr view 3 -R cctrc-bvg/sgc-redes-bvg     # PR aberto: estado, revisão e CI
./scripts/dev.sh testar                     # esperado: typecheck ok + 436 testes passando
```

Para ver o sistema com dados: `./scripts/dev.sh dev` num banco de desenvolvimento com
`npm run db:demo -- --confirmo` (usuários `@exemplo.edu.br`, senha `Demonstracao2026`; a `coordenacao@` também leciona).
Regras e arquitetura: [CONTRIBUTING.md](../CONTRIBUTING.md) e [Guia do contribuidor](desenvolvimento/GUIA_DO_CONTRIBUIDOR.md).
Fluxos de uso, com diagramas: [FLUXOS.md](FLUXOS.md). Requisitos e decisões: [FSD.md](FSD.md).

**Armadilhas desta máquina (4 GB, Pentium N3700):**
- Um processo pesado por vez: build (cerca de 1 a 3 min), E2E completo (15 a 20 min) e testes unitários não rodam juntos.
- Prefixe com `nice -n 15 ionice -c 2 -n 7`; Vitest com `--maxWorkers 1`.
- E2E: banco novo a cada rodada (`npx prisma dev stop/rm/-n sgc-teste -d`, depois `migrate deploy`, `db seed` e
  `db:demo -- --confirmo`), com `DATABASE_URL=...&connection_limit=1&pgbouncer=true`,
  `PLAYWRIGHT_HOST_PLATFORM_OVERRIDE=ubuntu24.04-x64` e `TMPDIR=~/.cache/sgc-tmp`.
- A página de acessibilidade da Coordenação leva cerca de 2,5 min; logo após subir o servidor, o login pode demorar:
  repita só aquela suíte antes de concluir que há erro.
- Os scripts de produção (`scripts/atualizar.sh`, `scripts/ensaiar-migracao.sh`) só foram conferidos com `bash -n`:
  esta máquina não tem Docker. O primeiro uso na VM deve ser acompanhado.
- Não passe o Prettier em arquivos antigos: ele reformata o arquivo inteiro e polui o diff.
- `.handoff/planilhas-exemplo/` tem nomes reais: nunca commitar nem copiar para testes.

---

## 2. Estado atual

**Repositórios:**
- `origin` = fork `isaacmiranda/sgc-redes-bvg` (público, admin). `upstream` = `cctrc-bvg/sgc-redes-bvg` (original, só leitura).
- **PR #1** (versão inicial) e **PR #2** (E2E, mensagens de PPI/PPE, guia de implantação e backup) foram **mesclados em
  24 e 25/11/2026**.
- **PR #3** (`e2e-ampliado` → `main`), aberto em 25/11/2026, commits `84846c9` e `96fded8`: tela inicial nova, docência
  acumulada ao cargo, revisão dos fluxos, ausência informada pelo docente, avaliações finais, produção segura, FSD e
  estudo do login Google. CI do primeiro commit: verde. Texto em `.handoff/github/pr-e2e-ampliado.md` (local).
- **Peça confirmação antes de cada push** (commits novos num branch com PR aberto entram no PR).

**Pronto (Fases 1 e 2 do FSD + extras):**
- Base: login/sessões, RBAC (5 perfis + docência acumulada), auditoria, tela inicial com prioridades, Meu contato.
- Cadastros: usuários (com "Também leciona"), turmas, disciplinas (matriz do PPC), ofertas.
- Operação: calendário acadêmico (avaliações finais fora dos dias letivos), grade e sábados letivos, ausências (período
  de até 15 dias; o docente também informa a própria), convites, alimentação, comunicação semiautomática por e-mail ou
  WhatsApp.
- Gestão: PPE/PEI (com encontros realizados e avisos de prazo), PPI, diários, processos SEI, relatórios.
- Planilhas e PDF: importação com prévia, exportação .xlsx/.csv, PDF do calendário, das grades, dos relatórios, do PEI
  (modelo do campus, logo oficial) e da ficha de PPI.
- Produção: `/api/saude`, healthcheck, limites, PostgreSQL ajustado, `atualizar.sh` com ensaio das migrações e volta
  automática, política de migrações testada no CI, backups ao longo do dia no fim do semestre.
- Documentação: Ajuda no sistema (14 tópicos, diagramas coloridos por responsável, com "Onde:"), manual gerado,
  `FLUXOS.md`, `IMPLANTACAO.md` (dimensionamento medido), FSD com RNF01–RNF05 e decisões G15–G24.

**Verificado em 25/11/2026:** 436 testes unitários, typecheck e build; E2E de 50 testes (48 na rodada completa e as
duas falhas corrigidas e repetidas, mais os testes da Alimentação) num banco novo.

---

## 3. Pendências que dependem do usuário

- [ ] **Acompanhar a revisão do PR #3** e o CI do segundo commit.
- [ ] **Decisões abertas:**
  - **G20–G22:** login Google institucional (`docs/estudos/SSO_GOOGLE.md`): adiado pelo usuário.
  - **Reposição de aulas, diário aberto e prazo da avaliação final** (oportunidades do ROD, na seção 4).
- [ ] **Com a TI (G17–G19 já aceitos: "o que for solicitado será concedido"):**
  - pedir a VM (4 vCPU, 8 GB, 80 GB SSD), o endereço https (`APP_URL`) e o certificado;
  - destino do backup fora da VM;
  - monitoramento externo de `/api/saude`;
  - HA do Proxmox, se houver armazenamento compartilhado.
- [ ] **Cadastrar em Comunicação** o e-mail de cada grupo/setor (CAE, nutricionista, CTP, Gestão, líderes) e a CGP
      (`cgp.boaviagem@ifce.edu.br`, já no texto das mensagens).
- [ ] **Marcar "Também leciona"** em Usuários para quem acumula cargo e docência (Direção, CTP, AE), se houver.
- [ ] Testar as planilhas de exemplo com dados reais (`.handoff/planilhas-exemplo/`, fora do git).
- [ ] Logo: trocar o PNG pelo SVG oficial, se a Comunicação do IFCE fornecer.

---

## 4. Backlog priorizado

### P1 — próximos passos (em ordem)

1. **PR #3:** acompanhar CI e revisão; corrigir em commits novos (push com confirmação).
2. **Implantação em produção** (depende da TI e do endereço): seguir `docs/IMPLANTACAO.md`.
   - Fazer o **primeiro uso acompanhado** de `./scripts/atualizar.sh` e `./scripts/ensaiar-migracao.sh`, que nunca
     rodaram com Docker.
   - Fazer o **teste de restauração** do backup e agendar o cron com 3 backups por dia.
3. **Oportunidades do ROD** (sugestões, sem decisão pendente além do "sim"):
   - prazo de reposição de aulas, 15 dias letivos ou o fim da etapa (Art. 37, § 2º): mostrar nas ausências com turma
     dispensada e avisar na tela inicial;
   - diário aberto por 7 dias letivos após a aula (Art. 43): lembrete ao docente antes de o registro fechar;
   - avaliação final no mínimo 3 dias letivos após o registro da média parcial (Art. 105, § 1º): conferência ao
     cadastrar o período de avaliações finais.
4. **Diários por planilha:** importar a exportação do sistema acadêmico, em vez de digitar as aulas ministradas.
   Precisa de um arquivo de exemplo, com dados fictícios ou anonimizados.
5. **Login Google (SSO)**, quando o usuário decidir G20–G22 e a TI entregar as credenciais OAuth e o domínio: cerca de
   2 dias de desenvolvimento (`docs/estudos/SSO_GOOGLE.md`).
6. **Redefinição de senha por e-mail:** só com SMTP institucional; até lá, a Coordenação gera nova senha provisória.
7. **Desempenho, se o log de consultas lentas apontar:** agrupar ou guardar em cache por 1 minuto as cerca de 25
   contagens da tela inicial da Coordenação. Se o pico crescer, duas réplicas do app atrás do Caddy.
8. **Prisma 7/8** (o `latest` do npm era o 8.0.0-rc em 24/11): esperar o 8 estável; o `prisma.config.ts` já existe.
9. **Ampliar o E2E aos poucos:** levar ao Playwright só o que depende do navegador; os roteiros Python de
   `.handoff/e2e/` (sem JS, fora do git) cobrem mais regras.

### P2 — depois

- Integração com o SUAP (decidido: depois).
- Envio automático pela Evolution API: o código existe, desligado (decidido: semiautomático). O convite individual
  seria o primeiro candidato.
- **Fase 3 (FSD) — congelada (decisão de 25/11/2026):** laboratórios, estoque/DAP, atas e eventos ficam para
  implementação posterior; não iniciar sem nova decisão.

### Concluídos em 25/11/2026 (branch `e2e-ampliado`)

- **Tela inicial nova** (pedido do usuário): "Precisa da sua atenção" primeiro (ações por perfil, de urgente a
  acompanhar, só com quantidades — `src/lib/painel/acoes.ts`), "Todas as funções" num `<details>` de um clique (aberto
  quando não há pendências), agenda ao lado (semana do docente; refeições de hoje e amanhã para a AE; mês + próximos 30 dias para os demais, com as ausências de hoje para a Coordenação), ícones Lucide
  por função (`src/lib/navegacao.ts`, fonte única com o cabeçalho) e botão **Início** no cabeçalho.
- Aviso de prazo prorrogado da PPE; PEI impresso no modelo do campus; textos de PPI/PPE validados.

### Decisões de 25/11/2026 (sugestões aceitas pelo usuário)

- **Agenda da Coordenação:** mês + bloco **Hoje** com as ausências do dia (pendentes primeiro) — feito.
- **Agenda da Assistência Estudantil:** refeições previstas para hoje e amanhã, já com os avisos, mais os próximos
  eventos — feito (`src/lib/alimentacao/servidor.ts`, `totaisDoDia`).
- **"Minha grade" para a Coordenação:** mantida (coordenador também leciona).
- **Logo oficial:** `public/marca/ifce-vertical.png` substituído pela assinatura enviada pelo usuário (PNG; trocar por
  SVG se a Comunicação do IFCE fornecer).
- **PR #3** (`e2e-ampliado`): aberto em 25/11/2026, depois da fusão do PR #2.
- **Infraestrutura e comunicação (pedido do usuário, 25/11/2026):**
  - Dimensionamento medido para o pico de fim de semestre: 4 vCPU, 8 GB, 80 GB SSD (`docs/IMPLANTACAO.md`, 1.1).
  - Atualização segura com `scripts/atualizar.sh` (backup, ensaio das migrações numa cópia, volta automática) e
    política de migrações testada no CI (seção 8).
  - Healthcheck em `/api/saude`.
  - Orientação da Gestão de Pessoas nas mensagens ao docente ausente.
  - Estudo do login Google em `docs/estudos/SSO_GOOGLE.md`.
  - Decisões G15–G22 no FSD.
- **Decisões de 25/11/2026:** G15 aceito e implementado (o docente informa a própria ausência); G16 com o contato da
  CGP (`cgp.boaviagem@ifce.edu.br`) nas mensagens; G17–G19 aceitos (recursos da VM, janela de atualização, backups).
- **Calendário (25/11/2026, G23):** avaliações finais sempre fora dos dias letivos e da grade, só no fim do ano
  (ROD, Art. 32), com aviso se cadastradas antes do último dia letivo. ROD guardado em `docs/referencias/rod-ifce.pdf`.
- **G24 aceito (25/11/2026):** refeições nos dias de avaliação final por acréscimo lançado pela Coordenação; a agenda
  da AE mostra os acréscimos mesmo em dia sem aula.
- **Oportunidades do ROD:** viraram itens do backlog (seção 4, P1, item 3).
- **Análise dos fluxos** (pedido do usuário): `docs/FLUXOS.md` com diagramas por fluxo (responsável, tela e código)
  e a análise de gargalos. Feito: encontros do PEI, avisos de resultado e de lançamento da PPE, ausência por período (até
  15 dias), preparação do ano na tela inicial, processos a vencer e semana compacta. Pendentes (sugestões na seção 14 de
  FLUXOS.md): importação das aulas ministradas (backlog 4) e redefinição de senha por e-mail (backlog 6).
- **Cargo + docência** (pedido do usuário): quem tem cargo e leciona vê tudo junto, cargo em verde e docência em azul
  com rótulo; marca "Também leciona" no cadastro, ligada também ao receber disciplina pela planilha; só quem acumula vê
  as cores; agenda com Hoje + Minha semana e o mês recolhido. Feito — `User.leciona`, `acumulaDocencia` em
  `src/lib/auth/rbac.ts`, grupo "Docência" em `src/lib/navegacao.ts`.

### Concluídos em 24/11/2026

Calendário acadêmico e importação por planilha; grade por planilha; relatórios em PDF e planilha; exportação de todas
as listagens (.xlsx e .csv, com botões); textos-padrão editáveis; mensagem avulsa com vários destinatários e grupos de
docentes; nutricionista; comunicar alterações da alimentação e do calendário; Ajuda, manual e guia do contribuidor;
dados de demonstração; impressão do PEI; convites de substituição; CI. Depois do PR #1: E2E com Playwright no CI,
`prisma.config.ts`, mensagens prontas de PPI/PPE, guia de implantação e script de backup.

### Decisões de 24/11/2026 (sugestões aceitas pelo usuário)

- **Publicação:** testar no navegador, depois issue + PR do fork para o `cctrc-bvg` (com confirmação antes).
- **Escolhas da madrugada** (seção 5, "Implementação da Fase 1 e 2"): aceitas; levar à Coordenação para validar.
- **WhatsApp:** semiautomático; o sistema funciona **inteiro sem integração**. Toda comunicação também por **e-mail institucional**.
- **G05 no PEI:** continua manual (`/alimentacao/adicionar`) para não guardar a turma do estudante com PEI.
- **Playwright:** poucos roteiros de fumaça com JS, rodando no CI do GitHub (não na máquina de 4 GB).
- **`prisma.config.ts` / Prisma 7:** junto com o teste do Docker, sem pressa.
- **Aviso ao convidado:** já sai na caixa de mensagens da ausência (e-mail ou WhatsApp, com link de "Minha grade").
- **Login pelo SUAP:** segunda fase, depois do sistema em uso (pedir cadastro do app à TI).

### Feito (referência)

Catálogo + matriz, consulta ao catálogo, ofertas, grade (RF01) com sábados letivos, importação em lote (disciplinas,
usuários, ofertas, sábados), turmas, ausências (RF02), alimentação (RF03/RF04), comunicação (RF05), PPE/PEI (RF06),
PPI (RF07), diários (RF08), Kanban SEI (RF09), relatórios, auditoria, limpeza de sessões. Detalhes nos commits da seção 2
e nas decisões da seção 5.

**Documentação (24/11/2026):** módulo **Ajuda** no sistema (`/ajuda`, tópicos por perfil com capacidades, fluxograma,
passo a passo, capturas e dúvidas; manual para PDF em `/ajuda/manual`), fonte única em `src/lib/ajuda/topicos.ts`,
`docs/MANUAL_DO_USUARIO.md` gerado por `npm run manual`, capturas por `npm run capturas` (Playwright) sobre dados
fictícios de `npm run db:demo -- --confirmo`. Para contribuidores: `CONTRIBUTING.md`,
`docs/desenvolvimento/GUIA_DO_CONTRIBUIDOR.md` e modelos de issue/PR em `.github/`. O teste da Ajuda falha se um módulo
do painel ficar sem tópico, se faltar imagem ou se o manual estiver desatualizado.

---

## 5. Decisões já tomadas (não rediscutir sem motivo novo)

**Curso e calendário (confirmadas pela Coordenação):**
- **Disciplinas técnicas são semestrais** (CH da coluna "Semestre" do PPC) e têm **metade da carga prática**
  (`exigePratica` = sim); **propedêuticas e diversificadas são anuais**. Validado em `DisciplinaSchema`.
- **Matriz:** códigos repetidos entre anos recebem sufixo do ano (BIO.1, BIO.2…); técnicas do 3º ano: SEGR, EB, PRD, RSF;
  o total de 960h do 3º ano impresso no PPC tem erro de soma (correto: 1.080h). Dados em `src/lib/disciplinas/matriz-curricular.ts`.
- **G01:** 4 blocos de 60 min por turno — manhã 07:30–11:45 (intervalo 09:30–09:45), tarde 13:00–17:20 (intervalo
  15:00–15:20). Coincidem com os horários de refeição do motor (09:30, 12:00, 15:00).
- **G14:** há **sábados letivos**, variáveis com o calendário civil; cada um segue a grade de um dia da semana
  (`SabadoLetivo`). Use `diaDaGradeNaData()` para saber qual grade vale numa data.
- **Ofertas:** uma por ano letivo × turma × disciplina; anual → `semestreLetivo` null; técnica de semestre ímpar do curso
  → .1, par → .2; `aulasPrevistas` = CH. Ofertar disciplina de outro ano **não é necessário** (Artes no 3º ano foi caso único).
- **Grade:** por ano + semestre letivo; anuais nos dois semestres, técnicas só no seu. Janelas livres são **calculadas**
  (vago em turno com aula), não gravadas. Choque de professor entre turmas é recusado no servidor.
- **Acesso:** professores veem a grade de todas as turmas e a própria grade; Professor, CTP e Direção consultam o
  catálogo; só a Coordenação edita catálogo, ofertas, grade e sábados. Qualquer cargo pode também lecionar (`User.leciona`); só quem leciona recebe ofertas.
- **Turmas** valem para todos os anos letivos (as disciplinas de cada ano ficam nas ofertas). `Turma.anoLetivo` é legado:
  recebe o ano da criação e não aparece na tela. Médias de refeição ≤ estudantes ativos (≤ 80). Com ofertas lançadas, o
  ano do curso fica travado; turma usada (ofertas, grade, ausências, avisos) só é desativada, nunca excluída.
- **Importação em lote** por .xlsx ou .csv com modelo pré-preenchido para baixar, editar e enviar; prévia antes de gravar.
  Ofertas: chave ano + turma + disciplina, só cria ou troca professor (nunca remove). Sábados: dia da grade por extenso
  (números recusados: "2" seria 2ª-feira ou terça?).

**Implementação da Fase 1 e 2 (madrugada de 24/11/2026; revisar com a Coordenação se algo não bater com a prática):**
- **Ausência:** uma por oferta e dia (`@@unique([dataAula, ofertaId])`); decisão **por horário** em `CoberturaBloco`
  (substituição, adiantamento ou dispensa). Professor com aulas em 2 turmas no dia gera 2 registros.
- **Adiantamento:** traz uma aula **posterior** da mesma turma, no mesmo dia (inclusive do outro turno), de professor livre
  no horário vago; a origem fica vaga. Sugestões e validação consideram as coberturas das outras ausências do dia.
- **Motivo operacional** só de lista fechada (`MOTIVOS_OPERACIONAIS`); nada de texto livre (LGPD).
- **Refeições:** presença por turno = há pelo menos uma aula no turno (RN01; isso já atende G02). Avisos são
  recalculados por turma e dia a cada decisão (`recalcularAlertasDaTurma`): cria os que faltam, cancela (`canceladoEm`)
  os que deixaram de valer; refeição já servida não gera aviso (G13). Quantidade = média da turma; editável (G04), e
  mudar a quantidade pede nova ciência da AE. Acréscimos manuais (`manual = true`, G05) ficam fora do recálculo.
- **Emergência (< 4h):** exige justificativa na decisão e mostra "ligue para a AE" (G06).
- **Comunicação semiautomática por e-mail ou WhatsApp:** o sistema funciona sem integração com o WhatsApp. Cada mensagem
  (`MensagemPronta`, `src/lib/comunicacao/mensagem.ts`) tem Para (e-mail institucional do usuário ou do grupo/setor),
  assunto, texto editável e campos `{{...}}` para destinatários sem cadastro; a caixa (`src/components/caixa-mensagem.tsx`)
  copia texto/assunto, cola, substitui campos e abre no e-mail (`mailto:` sem a formatação `*negrito*`) ou no WhatsApp.
  O registro do envio pede o meio (E-mail, WhatsApp, Outro) e guarda o **texto gerado** (edições na caixa não vão ao banco).
  Mensagens individuais: convidado, substituto, docente da aula adiantada e docente ausente; CTP recebe aviso de dispensa.
  Canal **inativo** não gera mensagem. `User.usaWhatsapp = false` (em "Meu contato"): ninguém recebe link para o número
  da pessoa, e ela própria não vê botões nem a opção WhatsApp.
- **Mensagem avulsa:** vários destinatários (grupos/setores, grupos de docentes `docentes:todos|turma:X|ano:N` montados pelas
  ofertas do ano, pessoas); uma caixa por destinatário. Escolher um texto-padrão marca os destinatários sugeridos dele
  (`ModeloMensagem.destinos`). Campos `{{...}}` viram controles: turma e disciplina com lista dos cadastrados (`datalist`),
  `{{data...}}` com seletor de data (sai dd/mm/aaaa e preenche `{{dia}}`), `{{horário...}}` com seletor de hora,
  `{{horarios}}` com os blocos cadastrados. `{{nome}}`/`{{destinatarios}}` são preenchidos por destinatário.
- **Alimentação → comunicar:** dar ciência, ajustar quantidade ou acrescentar refeição leva quem tem `comunicacao:enviar`
  a `/alimentacao/comunicar?data=…&motivo=…`, com mensagens do dia para CAE, nutricionista, Gestão e turmas (turmas sem
  quantidade nem urgência). Registrar o envio à CAE marca os avisos do dia como enviados.
- **Calendário acadêmico** (`/calendario`, `src/lib/calendario/`): eventos (`EventoCalendario`, tipos fixos: férias docentes,
  matrícula, encontro pedagógico, avaliações finais/entrega de diários, feriado, ponto facultativo, acolhida, PITs/RITs,
  reunião, suspensão por força maior com motivo de lista fechada, outro), etapas `EtapaLetiva` N1–N4 e semestres
  (`PeriodoLetivo`, o mesmo dos diários). Sábados letivos continuam em `SabadoLetivo` (com o dia da grade). Dia letivo =
  seg–sex ou sábado letivo, dentro das etapas (ou semestres), fora de evento com `suspendeAulas`. Meta 200/ano, 50/etapa.
  `imprevisto` marca dias "a repor". Evento que suspende aulas zera a grade do dia em `carregarDia` (ausências e refeições).
  Professor, CTP, Direção e AE veem; só a Coordenação edita.
- **Impressão/PDF:** páginas `/calendario/imprimir` e `/grade/imprimir` (todas as turmas ou `?turma=`), PDF pelo "Salvar
  como PDF" do navegador (sem biblioteca); o cabeçalho do sistema some na impressão (`print:hidden`).
- **Exportação:** `/exportar/<listagem>?formato=xlsx|csv` (usuários, turmas, disciplinas, ofertas, grade, calendário,
  ausências), com a permissão de cada listagem; as que têm dados pessoais registram `EXPORTOU_LISTAGEM` na auditoria.
- **Nutricionista:** canal `NUTRICIONISTA`, recebe as mesmas alterações de refeição que a CAE (ausência e Alimentação).
- **Textos-padrão** (conjunto inicial em `src/lib/comunicacao/modelos.ts`): modelos por tipo de interação (convite, substituição,
  adiantamento, turma, CAE, CTP, Gestão, docente ausente, diário, reunião, comunicado, PPI, PPE, processo SEI), com variáveis
  `{{turma}}`, `{{data}}`, `{{horarios}}`, `{{nome}}` etc. Cada mensagem gerada traz `tipo` e `variaveis`; o select
  "Texto-padrão" da caixa troca assunto e texto, e o que o sistema não sabe vira campo a preencher. Na mensagem avulsa
  o select lista todos os modelos (sem JS, o servidor aplica o modelo quando o texto vem vazio). Editar modelos pela
  tela ficou para depois (backlog).
- **Mensagens:** sempre regeneradas no servidor; Gestão só recebe dispensa, impacto alimentar ou aula sem cobertura (G08);
  líderes de turma sem nome de docente; AE sem docente nem disciplina.
- **Diários:** espelho do sistema acadêmico (a Coordenação atualiza); amarelo quando ministradas < esperadas − 10%
  (mín. 2), com esperadas proporcionais ao tempo decorrido do semestre (`PeriodoLetivo`; anuais usam os dois semestres).
- **PPI:** tetos de quantidade e de horas **acumulados por categoria** (as atividades mais antigas consomem primeiro);
  deferir exige 40h; indeferir exige motivo; deferida trava lançamentos.
- **PPE:** o docente que define é quem tem oferta da disciplina no ano do semestre; CTP só lê; lançamento no sistema
  acadêmico (Art. 125) só pela Coordenação; prorrogação só para PEI preenchido.
- **Kanban:** item sigiloso não existe para quem não tem `kanban:ver-sigiloso` (404, nem aparece no payload).

**Técnicas:**
- **Prisma 6.x**, não 7 (schema usa `url = env(...)`).
- **Sessões próprias no banco**, não NextAuth. `APP_URL` no lugar de `NEXTAUTH_URL`. Futuro: SSO SUAP via OIDC.
- **Senha provisória** gerada pelo sistema, exibida uma vez, troca obrigatória no 1º acesso.
- **Cookie de sessão** sem `Expires`; 8h de inatividade no banco.
- **Verde oficial `#2f9e41` não é usado em texto** (3,45:1). Ações/links: `if-verde-700`. Status sempre com ícone + texto.
- **Formulários funcionam sem JavaScript** (Server Actions + `useActionState`) — requisito de acessibilidade.
- **ExcelJS** (não o pacote `xlsx` do npm, abandonado e vulnerável) para planilhas; fora do bundle (`serverExternalPackages`).
- **Dados pessoais fora do repositório:** a grade de exemplo (PDF aSc 2026) tem nomes de professores — não versionar;
  cadastrar pela tela ou por importação.
- Interface, mensagens, commits e documentação **em português**.

---

## 6. Convenções do código

Movidas para o **[Guia do contribuidor](desenvolvimento/GUIA_DO_CONTRIBUIDOR.md)** (seções 4 a 10): acesso e auditoria,
campos ocultos em vez de `acao.bind`, formulários, lote/importação, migrações, testes e a receita de módulo novo. Regras
de contribuição em [CONTRIBUTING.md](../CONTRIBUTING.md). Funcionalidade nova exige tópico na Ajuda
(`src/lib/ajuda/topicos.ts` + `npm run manual`).

---

## 7. Ambiente local e armadilhas conhecidas

- Máquina com **4 GB de RAM e HDD**: tudo com `nice -n 15 ionice -c 2 -n 7`, um processo pesado por vez. O build leva
  ~1–2 min; a bateria completa de E2E (14 roteiros, cada um com banco recriado) leva ~40 min — rode em segundo plano.
- **npm 9.2 do Ubuntu** falha com `reading 'edgesOut'`: use `npx -y npm@10 install` (o `dev.sh` faz isso).
- **Postgres de teste sem Docker:** `npx prisma dev -n sgc-teste -d`. Exige `&connection_limit=1&pgbouncer=true` na
  `DATABASE_URL`, senão `42P05 prepared statement already exists`.
- **`prisma migrate reset` é bloqueado** para assistentes de IA: recrie a instância (`npx prisma dev stop|rm sgc-teste`).
- **Servidor standalone de teste:** copie `.next/static` para `.next/standalone/.next/static`; localize o processo pela
  porta (`ss -ltnp | grep :3123`). Evite `pkill -f <texto>` (pode matar o próprio shell).
- **Processo a 100% de CPU:** `kill -USR1 <pid>` e `node .handoff/e2e/inspecionar-cpu.mjs` mostram a pilha.
- **Roteiros E2E (Python, sem JS):** `e2e_alimentacao.py`, `e2e_processos.py`, `e2e_diarios.py`, `e2e_ppi.py` e `e2e_ppe.py`
  reaproveitam os utilitários de `e2e_ausencias.py` (cortado em `# --- Data alvo`); `e2e_importacao2.py` reaproveita
  `e2e_importacao.py`. O parser de formulários **ignora `<select>`** e só envia campos ocultos: passe selects e
  checkboxes em `valores` (ou `enviar_multi` para campos repetidos). Dados de teste entram por SQL (`sql()`).
  - o 1º formulário com Server Action em toda página logada é o **"Sair"** do cabeçalho — escolha o formulário pelo
    campo (`form_com(pag, "campo")`); na grade do 2º semestre há dois formulários com os mesmos campos ocultos;
  - o React separa textos adjacentes com `<!-- -->` (troque por "" antes de comparar) e o `<strong>` vira espaço;
  - para **contar** ocorrências, remova os blocos `<script>…</script>` (o payload RSC repete os textos);
  - consultas ao banco no roteiro: arquivo `.ts` temporário **dentro do projeto** + `npx tsx` (para achar `@prisma/client`).
- **Auditoria de dependências:** alertas altos vêm do Prisma (`effect`/`deepmerge-ts`, pré-existentes); o `uuid`
  moderado vem do ExcelJS e não se aplica (só v3/v5/v6 com buffer).

---

## 8. Verificação antes de cada commit

```bash
./scripts/dev.sh testar                                         # typecheck + testes unitários
NEXT_TELEMETRY_DISABLED=1 nice -n 15 ionice -c 2 -n 7 npx next build
# Se mexeu em telas/ações: E2E sem JavaScript, CADA roteiro num banco recriado (eles alteram senhas e dados)
for s in e2e_login e2e_usuarios e2e_disciplinas e2e_ofertas e2e_grade e2e_importacao e2e_turmas \
         e2e_ausencias e2e_alimentacao e2e_processos e2e_diarios e2e_ppi e2e_ppe e2e_importacao2; do
  bash .handoff/e2e/e2e_ambiente.sh >/dev/null 2>&1          # recria banco, migra, seed, sobe servidor na 3123
  echo "$s: $(python3 -I .handoff/e2e/$s.py 2>&1 | grep -E 'FALHA|TUDO CERTO' | tr '\n' ' ')"
done
```

Depois do E2E, desligue o servidor e o banco:
`ss -ltnp | grep :3123` (matar o pid) e `npx prisma dev stop sgc-teste && npx prisma dev rm sgc-teste`.
