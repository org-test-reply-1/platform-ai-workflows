# platform-ai-workflows

Repository contenente i workflow AI di supporto basati su GitHub Copilot CLI e Coding Agent. Questi workflow **non** sono template CI/CD — sono utility laterali che girano come supporto al Platform Team.

## Workflow disponibili

| Workflow | Trigger | Descrizione |
|----------|---------|-------------|
| `ai-runner-monitor.yml` | Cron (Lun-Ven 07:00) + manuale | Report stato self-hosted runner con analisi AI |
| `ai-daily-summary.yml` | Cron (Lun-Ven 17:30) + manuale | Riassunto giornaliero cambiamenti nei repository |
| `ai-security-scan.yml` | `workflow_call` (da pipeline app) | Scansione sicurezza AI con kill switch opzionale |
| `ai-scaffold-template.yml` | Manuale (`workflow_dispatch`) | Genera bozze di nuovi Reusable Workflows |
| `ai-provisioning-report.yml` | `workflow_run` su provisioning + manuale | Report AI post-esecuzione provisioner |
| `ai-provisioning-fix.yml` | `workflow_run` su provisioning (failure) | Crea issue per step falliti, assign `@copilot` |
| `ai-failure-handler.yml` | Cron ogni 2h + manuale | Scansiona **tutta l'org** per run falliti, crea issue nei repo di origine, assign `@copilot` — zero file per-repo |

## Prerequisiti

| Prerequisito | Descrizione |
|-------------|-------------|
| Copilot Pro (o superiore) | Funziona con PAT personale con permesso "Copilot Requests" |
| Coding Agent abilitato | Settings → Copilot → Features → "Only selected repositories" |
| `COPILOT_GITHUB_TOKEN` | Org secret — PAT con "Copilot Requests: Read" |
| `COPILOT_ASSIGN_PAT` | Org secret — PAT fine-grained con "Issues: Read/Write", "Actions: Read", "Repository: Read" |
| `platform-actions` v1+ | Composite Actions AI (`copilot-cli-setup`, `copilot-run-agent`, `create-copilot-issue`) |

## Zero file per-repo

Il failure handler è **completamente centralizzato**: scansiona l'API org per tutti i run falliti e crea issue nei rispettivi repository. Non serve aggiungere nessun workflow nelle repo applicative per l'auto-fix.

L'unico file per-repo è `copilot-setup-steps.yml` (richiesto dal Coding Agent per design GitHub), distribuito automaticamente come **workflow template dell'org** tramite la repo `.github`.

## Relazione con le altre repo

| Repo | Relazione |
|------|-----------|
| `platform-workflows` | Invariata. Si aggiungono solo prompt files in `.github/agents/` e `copilot-instructions.md` |
| `platform-actions` | Invariata. Si aggiungono 3 nuove Composite Actions AI (v1+) |
| App repos | **Nessun file necessario** per il failure handler. Solo `copilot-setup-steps.yml` per Coding Agent (via template org) |
