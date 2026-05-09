# Dossiê Técnico - Grupo ORKA

## Escopo e status
- Objetivo: consolidar avaliação técnica para suporte à proposta de entrada societária.
- Status atual: análise concluída para 6 repositórios.
- Repositórios analisados até aqui:
  - `ORKA` (mono-repo com backend, dashboard e ativos de marketing)
  - `orka-home` (SaaS multi-tenant para construtoras: NestJS + Next.js)
  - `orka-save` (SaaS de automação WhatsApp + CRM: NestJS + Next.js + Vite)
  - `orka-closer` (ferramenta de IA para corretores: Next.js full-stack)
  - `Leads-Orka` (geração de leads via Google Places: Express.js + React)
  - `orka-pages` (plataforma de landing pages imobiliárias: Next.js full-stack)

## Metodologia aplicada
- Revisão arquitetural de alto nível.
- Levantamento de riscos técnicos com foco em segurança, operação e escalabilidade.
- Classificação por severidade: Crítico, Alto, Médio, Baixo.
- Recomendações orientadas a execução e priorização por risco.

---

## Diagnóstico transversal do grupo (processo, entrega e operação)

### Lifecycle Software (1-12)
1. **Source Control & Collaboration**
   - Será feito: padronizar branch strategy, PR template, CODEOWNERS, branch protection e vínculo PR x task board.
   - Será aproveitado: repositórios já existentes e histórico de código atual.
2. **Local Development**
   - Será feito: unificar padrão de setup local (README executável, `.env.example`, version pinning, pre-commit hooks).
   - Será aproveitado: `docker-compose` e exemplos de ambiente já presentes em parte dos projetos.
3. **Code Quality**
   - Será feito: aplicar lint/typecheck/testes mínimos obrigatórios em todo PR; criar um **Design System ORKA** — biblioteca de componentes, tokens de design (cores, tipografia, espaçamento) e diretrizes visuais compartilhadas por todos os produtos do grupo, garantindo identidade visual consistente e reduzindo retrabalho de frontend.
   - Será aproveitado: base TypeScript/Nest/React já estruturada, testes existentes e componentes UI já presentes em projetos como `orka-closer` (shadcn/ui) e `orka-pages`.
4. **CI (Continuous Integration)**
   - Será feito: criar pipeline padrão por PR (instalação com cache, lint, typecheck, testes e build).
   - Será aproveitado: scripts de build/teste já definidos nos projetos.
5. **CD (Continuous Deployment)**
   - Será feito: automatizar deploy por ambiente, com política de promoção e rollback simples.
   - Será aproveitado: plataformas já usadas em produção (Railway e Vercel).
6. **Infrastructure**
   - Será feito: consolidar compute/hosting no Railway (backend + frontend), eliminando Vercel; manter R2 exclusivamente para armazenamento de objetos; documentar arquitetura alvo e responsabilidades por serviço, reduzindo operação ad hoc.
   - Será aproveitado: Railway (já em uso para backend, Postgres e Redis) e R2 (já em uso para uploads) como base de transição.
7. **Configuration & Secrets**
   - Será feito: implantar governança de segredos por ambiente e política de rotação.
   - Será aproveitado: variáveis de ambiente e estrutura de configuração já adotadas pelos serviços.
8. **Observability**
   - Será feito: implantar monitoramento mínimo (logs centralizados, métricas, alertas, error tracking e uptime).
   - Será aproveitado: pontos críticos já mapeados (backend, integrações, uploads, frontend).
9. **Security**
   - Será feito: padronizar baseline de segurança (OWASP, scanning, hardening de CORS, segredo e auditoria).
   - Será aproveitado: validações e controles já existentes em partes da aplicação.
10. **Data**
   - Será feito: institucionalizar rotina de backup/restore drill e padrão de migrações em deploy.
   - Será aproveitado: migrações versionadas e modelo de dados já em produção.
11. **Release & Operations**
   - Será feito: criar runbooks, fluxo de incidentes, on-call simplificado e SLOs operacionais.
   - Será aproveitado: conhecimento tácito atual da equipe sobre operação dos serviços.
12. **Compliance & Legal**
   - Será feito: mapear requisitos aplicáveis (LGPD, contratos com vendors, políticas mínimas de retenção).
   - Será aproveitado: contratos e estruturas já existentes, quando disponíveis.

---

## Repositório: ORKA

### 1) Contexto do repositório
- Estrutura de mono-repositório com:
  - `orka-pro-backend` (NestJS, PostgreSQL, Redis, filas)
  - `orka-pro-dashboard` (React + Vite)
  - Pasta `MARKETING` e diversos binários (imagens, vídeos, áudios)
- Indício de mistura entre código de produto e ativos não técnicos no mesmo controle de versão.
- Infra/hosting relacionada (reportado): Railway (backend + Postgres + Redis), Vercel (frontend) e R2/Cloudflare (uploads).

### 2) Arquitetura atual (visão macro)
- Backend modular por domínio (auth, leads, organizations, integrations, dashboard etc.).
- Camada de dados centralizada em schema/migrações Prisma.
- Infra local com `docker-compose` para Postgres e Redis.
- Frontend SPA desacoplado do backend.

### 2.1) Avaliação lifecycle do repositório ORKA (1-12)
- **1. Source Control & Collaboration:** **Deficiente** (**Nota: 2/10**)  
  Evidência: sem workflows de colaboração/PR formal reportados; branch hygiene comprometida por artefatos versionados.
- **2. Local Development:** **Parcial** (**Nota: 6/10**)  
  Evidência: `docker-compose` no backend e `.env.example`; lacunas de hygiene e padronização multi-projeto.
- **3. Code Quality:** **Parcial/Deficiente** (**Nota: 4/10**)  
  Evidência: existem testes, mas cobertura é baixa para o tamanho do domínio; sem enforcement por CI.
- **4. CI:** **Ausente** (**Nota: 2/10**)  
  Evidência: sem pipeline operacional de PR reportada.
- **5. CD:** **Ausente** (**Nota: 2/10**)  
  Evidência: sem deploy automatizado por ambiente reportado.
- **6. Infrastructure:** **Parcial** (**Nota: 6/10**)  
  Evidência: backend/db/cache e frontend em provedores adequados, porém operação distribuída e sem padrão de governança.
- **7. Configuration & Secrets:** **Crítico** (**Nota: 1/10**)  
  Evidência: `.env` versionado no backend.
- **8. Observability:** **Ausente** (**Nota: 1/10**)  
  Evidência: ausência de ferramenta de monitoramento reportada.
- **9. Security:** **Deficiente** (**Nota: 4/10**)  
  Evidência: CORS permissivo e falhas de hygiene de segredos/versionamento.
- **10. Data:** **Parcial** (**Nota: 8/10**)  
  Evidência: schema e migrações versionadas; estratégia de backup/restore e drills não comprovada.
- **11. Release & Operations:** **Deficiente** (**Nota: 4/10**)  
  Evidência: sem runbook, SLO, trilha de release automatizada.
- **12. Compliance & Legal:** **Hipótese a validar** (**Nota: 2/10**)  
  Evidência: não avaliado nesta rodada inicial.

### 3) Pontos fortes
- Organização backend por módulos de negócio.
- Uso de validação global de entrada no backend.
- Existência de migrações versionadas de banco.
- Presença de stack moderna para frontend e backend.

### 4) Deficiências técnicas

#### [Crítico] Exposição de segredo em versionamento
- `.env` do backend está rastreado por git, com risco imediato de vazamento de credenciais.

#### [Crítico] Artefatos e dependências versionados
- `node_modules` e `dist` estão versionados, gerando risco de drift, baixa reprodutibilidade e repositório inflado.

#### Resumo dos demais achados por severidade
- **Alto:** 4 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco de segurança reputacional por eventual exposição de segredos.
- Risco de atraso em roadmap por baixa previsibilidade de deploy e testes.
- Risco de custo oculto de manutenção por dívida técnica em governança de repositório.
- Risco de indisponibilidade silenciosa por ausência de observabilidade e alertas operacionais.

### 6) Impacto esperado
- Segurança: Alto
- Estabilidade operacional: Alto
- Velocidade de evolução: Médio/Alto

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **3,5 / 10**
- **Justificativa:** base técnica com potencial e boa estrutura modular, mas maturidade operacional/lifecycle ainda baixa por lacunas críticas em segredos, observabilidade, CI/CD e governança de release.

---

## Repositório: orka-home

### 1) Contexto do repositório
- Repositório independente com backend e frontend no mesmo repositório:
  - Backend: NestJS (`orka-home-api`) com PostgreSQL via Prisma e integração R2 (S3-compatible).
  - Frontend: Next.js 15 + Tailwind CSS (subpasta `/web`).
- Produto: SaaS multi-tenant para construtoras — gestão de empreendimentos, unidades, contratos, parcelas, clientes, documentos, atualizações de obra e portal do cliente.
- Diferencial: parsing inteligente de PDFs (boletos, tabelas de vendas) via OpenAI.
- Infra reportada: Railway (backend), R2/Cloudflare (uploads); frontend sem configuração de deploy explícita observada.
- Ponto positivo imediato: `.env` não está versionado e `node_modules`/`dist` não rastreados — falhas críticas do repo ORKA ausentes aqui.

### 2) Arquitetura atual (visão macro)
- Backend modular por domínio (auth, customers, developments, units, contracts, installments, construction-updates, notifications, documents, dashboard, customer-portal, sales-mirror, admin).
- Multi-tenancy por design: entidade `Construtora` (mapeada para "Organization" no banco) com isolamento via middleware (`ConstructorScopeMiddleware`) aplicado globalmente.
- JWT com dois tipos de token distintos: interno (ADMIN/STAFF) e cliente (CUSTOMER).
- Armazenamento de objetos via R2 com AWS SDK S3-compatible (`r2.service.ts`).
- Espelho de vendas público com dois modos: grade de fachada (prédio) e mapa de lotes (imagem + marcadores).
- Dockerfile multi-stage (builder + runner) bem estruturado; `railway.toml` configurado.
- Frontend Next.js 15 (App Router) no subdiretório `/web`, com `.nvmrc` e ESLint configurados.

### 2.1) Avaliação lifecycle do repositório orka-home (1-12)
- **1. Source Control & Collaboration:** **Deficiente** (**Nota: 4/10**)
  Evidência: sem branch strategy, PR templates ou CODEOWNERS observados; commits informais ("ajuste valores", "salvando logo"); `.gitignore` correto.
- **2. Local Development:** **Parcial** (**Nota: 6/10**)
  Evidência: `.env.example` presente e documentado; `.env` não versionado; `.nvmrc` no frontend mas ausente no backend; sem `docker-compose` para Postgres local.
- **3. Code Quality:** **Parcial/Deficiente** (**Nota: 4/10**)
  Evidência: TypeScript em uso; ESLint configurado no frontend; sem testes unitários ou de integração observados (nenhum arquivo `.spec.ts` / `.test.ts`).
- **4. CI:** **Muito fraco** (**Nota: 2/10**)
  Evidência: sem pipeline de CI observado; ausência de `.github/workflows`.
- **5. CD:** **Parcial** (**Nota: 4/10**)
  Evidência: `railway.toml` e Dockerfile multi-stage presentes para o backend; `prisma migrate deploy` no start command é boa prática; sem configuração de deploy para o frontend Next.js; sem ambiente staging evidenciado.
- **6. Infrastructure:** **Parcial** (**Nota: 6/10**)
  Evidência: Railway + R2 configurados; uploads em disco local (`/uploads/`) incompatível com filesystem ephemeral do Railway — risco de perda de dados.
- **7. Configuration & Secrets:** **Parcial** (**Nota: 6/10**)
  Evidência: `.env` não versionado; `.env.*` coberto pelo `.gitignore`; CORS fallback para `origin: true` quando `FRONTEND_ORIGIN` não está definida — risco imediato em produção.
- **8. Observability:** **Inexistente** (**Nota: 1/10**)
  Evidência: health endpoint presente (`/health`); sem logging estruturado, error tracking, métricas ou alertas observados.
- **9. Security:** **Parcial** (**Nota: 6/10**)
  Evidência: auth JWT com bcrypt, separação clara de roles e isolamento multi-tenant; `ValidationPipe` global com whitelist; CORS permissivo por fallback; Swagger exposto em `/docs` sem auth guard — hipótese a validar se acessível em produção.
- **10. Data:** **Bom** (**Nota: 8/10**)
  Evidência: schema Prisma rico e bem modelado; migrações versionadas; `prisma migrate deploy` automático no deploy; estratégia de backup/restore não comprovada.
- **11. Release & Operations:** **Muito fraco** (**Nota: 2/10**)
  Evidência: scripts de manutenção ad hoc em `src/` (`fix-installment-amounts.ts`, `verify-scenario.ts`); sem runbook, SLO ou processo de incidente.
- **12. Compliance & Legal:** **Hipótese a validar** (**Nota: 2/10**)
  Evidência: processa CPF, e-mail e telefone de clientes; LGPD aplicável; não avaliado nesta rodada.

### 3) Pontos fortes
- Segredos não versionados — falha crítica do repo ORKA corrigida aqui.
- Arquitetura multi-tenant por design com isolamento em middleware global.
- Autenticação robusta: JWT com dois contextos (interno/cliente), bcrypt, separação de roles.
- Modelo de dados coeso e rico, com migrações versionadas e deploy automático.
- Dockerfile multi-stage bem construído; Railway configurado para o backend.
- Parsing inteligente de PDFs via OpenAI — diferencial de produto relevante.
- R2 integrado via SDK S3-compatible, com URL pública configurável.

### 4) Deficiências técnicas

#### [Crítico] CORS permissivo por fallback em produção
- Quando `FRONTEND_ORIGIN` não está definida, `origin: true` permite qualquer origem — risco direto em SaaS com dados financeiros e PII (`src/main.ts:36-41`).

#### [Crítico] Uploads em disco local incompatíveis com Railway
- Backend cria e serve diretório `uploads/` localmente (`src/main.ts:12-19`); filesystem do Railway é ephemeral — arquivos são perdidos em restart/redeploy.

#### Resumo dos demais achados por severidade
- **Alto:** 3 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco de perda de dados de clientes por uploads em filesystem ephemeral (Railway).
- Risco de acesso cross-origin irrestrito a contratos, parcelas e documentos por CORS permissivo.
- Risco de atraso em roadmap por ausência de testes e CI — bugs chegam à produção sem barreira.
- Risco de exposição da superfície de API via Swagger público em produção.
- Risco de indisponibilidade silenciosa por ausência de observabilidade e alertas.

### 6) Impacto esperado
- Segurança: Alto
- Estabilidade operacional: Alto
- Velocidade de evolução: Médio/Alto

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **4,3 / 10**
- **Justificativa:** avanço relevante em relação ao repo ORKA — secrets higiene correta, arquitetura multi-tenant sólida e stack moderna bem utilizada. Nota limitada por ausência total de testes e CI, dois achados críticos de operação/segurança, e ausência de observabilidade.

---

---

## Repositório: orka-save

### 1) Contexto do repositório
- Repositório com três projetos independentes:
  - `backend`: NestJS (`orka-revive-api`) com PostgreSQL, Redis, BullMQ, Stripe, Twilio, Meta WhatsApp e OpenAI.
  - `frontend`: Next.js 16 + Tailwind CSS — painel principal do produto.
  - `outreach-panel`: Vite + React — painel operacional de disparo de outreach.
- Produto: SaaS de automação WhatsApp + CRM para qualificação de leads com IA (engagement engine com auto-reply, campanhas, follow-ups e espelho de conversas).
- Infra reportada: Railway/R2 hipótese a validar — nenhuma configuração de deploy observada no repositório.
- Repositório público no GitHub (`felbranns-oss/orka-save`) — agrava criticamente qualquer exposição de segredo.

### 2) Arquitetura atual (visão macro)
- Backend modular por domínio: auth, accounts, users, campaigns, leads, conversations, product-knowledge, lead-qualification, integrations, automation, billing, outreach, crm-integration, engagement-engine.
- Processamento assíncrono via BullMQ + Redis — arquitetura adequada para automação de WhatsApp em escala.
- Integração multi-provedor: Twilio WhatsApp, Meta WhatsApp Cloud API e device-session (WAHA/bridge) — suporte a QR code e API oficial.
- Billing via Stripe com checkout público e planos mensais/anuais.
- R2 para armazenamento com fallback para disco local em desenvolvimento.
- CORS com whitelist explícita e rejeição por `Error('origin_not_allowed')` — implementação mais robusta que os repos anteriores.
- Verificação proativa de flags perigosas no boot (`assertProductionEnvironmentSafety`).

### 2.1) Avaliação lifecycle do repositório orka-save (1-12)
- **1. Source Control & Collaboration:** **Deficiente** (**Nota: 4/10**)
  Evidência: commits informais ("ajuste crm integração", "retirando ola"); sem branch strategy, PR templates ou CODEOWNERS; arquivos sensíveis versionados (ver seção 4).
- **2. Local Development:** **Parcial** (**Nota: 6/10**)
  Evidência: `.env.example` extensamente documentado; `.env` não versionado; `docker-compose.yml` para Redis + backend; sem `.nvmrc` em nenhum dos três projetos; três projetos sem setup unificado.
- **3. Code Quality:** **Parcial** (**Nota: 6/10**)
  Evidência: TypeScript em todos os projetos; ESLint em frontend e outreach-panel; 34 arquivos de teste no backend (concentrados em `engagement-engine` e `integrations`); 1 arquivo de teste no frontend; cobertura ausente nos demais módulos do backend.
- **4. CI:** **Muito fraco** (**Nota: 2/10**)
  Evidência: Jest (backend) e Vitest (frontend) configurados, mas sem pipeline que os execute automaticamente; ausência de `.github/workflows`.
- **5. CD:** **Ausente** (**Nota: 1/10**)
  Evidência: sem Dockerfile para nenhum dos três projetos; `docker-compose.yml` referencia `build: .` sem Dockerfile correspondente — não funcional; sem `railway.toml` ou configuração de deploy observada.
- **6. Infrastructure:** **Deficiente** (**Nota: 4/10**)
  Evidência: `docker-compose` com Redis presente para local; sem configuração de hospedagem para nenhum dos três projetos; R2 configurável com fallback para disco local.
- **7. Configuration & Secrets:** **Crítico** (**Nota: 1/10**)
  Evidência: `twilio_2FA_recovery_code.txt` com código real versionado; `backend/qr.png` possivelmente com sessão WhatsApp ativa versionado; `.env` não versionado (positivo isolado).
- **8. Observability:** **Inexistente** (**Nota: 1/10**)
  Evidência: health endpoint presente; sem logging estruturado, error tracking, métricas ou alertas observados.
- **9. Security:** **Parcial/Deficiente** (**Nota: 4/10**)
  Evidência: CORS com whitelist e rejeição explícita; `assertProductionEnvironmentSafety` no boot; `ValidationPipe` global; `TestController` público sem autenticação registrado no módulo de produção; credenciais Twilio 2FA e QR WhatsApp versionados contradizem os controles de segurança.
- **10. Data:** **Parcial** (**Nota: 6/10**)
  Evidência: Prisma com migrações versionadas; BullMQ + Redis para processamento assíncrono robusto; sem evidência de backup/restore drill; R2 com fallback local (risco se não configurado em produção).
- **11. Release & Operations:** **Muito fraco** (**Nota: 2/10**)
  Evidência: scripts de smoke test e seed presentes; sem Dockerfile ou estratégia de release para os três projetos; sem runbook, SLO ou processo de incidente.
- **12. Compliance & Legal:** **Hipótese a validar** (**Nota: 2/10**)
  Evidência: processa conversas WhatsApp, leads, dados pessoais e billing; Stripe e Twilio exigem DPA; LGPD aplicável — não avaliado nesta rodada.

### 3) Pontos fortes
- CORS com whitelist e rejeição explícita — implementação mais robusta que todos os repos anteriores.
- `assertProductionEnvironmentSafety`: verificação proativa de flags inseguras no boot — padrão de maturidade relevante.
- 34 arquivos de teste no backend cobrindo os módulos mais críticos (engagement-engine, integrations).
- Arquitetura assíncrona com BullMQ + Redis — adequada para automação de WhatsApp em escala.
- `.env.example` mais completo e documentado dos três repositórios.
- Billing integrado com Stripe, incluindo checkout público e validação de webhook.

### 4) Deficiências técnicas

#### [Crítico] Código real de 2FA Twilio versionado em repositório público
- `twilio_2FA_recovery_code.txt` contém código de recuperação real da conta Twilio — repositório é público no GitHub. Permite acesso à conta em caso de comprometimento de senha. **Ação imediata: invalidar o código na console Twilio e remover do histórico git.**

#### [Crítico] QR code de sessão WhatsApp versionado
- `backend/qr.png` rastreado pelo git — hipótese de sessão WhatsApp ativa exposta. Requer verificação imediata e remoção do histórico.

#### [Crítico] Deploy ausente para três projetos em produção
- Nenhum Dockerfile, `railway.toml` ou configuração de hospedagem encontrada; `docker-compose` referencia `build: .` sem Dockerfile correspondente — pipeline de produção não funcional como observado.

#### Resumo dos demais achados por severidade
- **Alto:** 2 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco imediato de comprometimento da conta Twilio por código 2FA exposto publicamente.
- Risco de sessão WhatsApp ativa exposta via QR versionado.
- Risco de operação não funcional em produção por ausência de Dockerfile e configuração de deploy.
- Risco de atraso em roadmap por ausência de CI — suite de testes existente não é executada automaticamente.
- Risco de indisponibilidade silenciosa por ausência de observabilidade em produto com automação crítica (WhatsApp, billing, filas).

### 6) Impacto esperado
- Segurança: Crítico (ação imediata nos segredos expostos)
- Estabilidade operacional: Alto
- Velocidade de evolução: Médio

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **3,3 / 10**
- **Justificativa:** presença de testes, CORS robusto e verificação de segurança no boot demonstram maturidade de código acima da média do grupo — mas três achados críticos (segredos reais versionados e ausência de deploy funcional) derrubam a nota. O potencial técnico é o mais alto dos repositórios NestJS; a execução operacional é a mais preocupante.

---

---

## Repositório: orka-closer

### 1) Contexto do repositório
- Repositório full-stack único: Next.js 16 (App Router + API Routes) com Prisma 7 + PostgreSQL.
- Sem backend separado — lógica de servidor via API Routes e Server Actions do próprio Next.js.
- Produto: ferramenta de IA para corretores de imóveis analisarem conversas de WhatsApp; classifica leads por temperatura, estágio de funil, objeção principal e risco de perda; sugere respostas em múltiplas variantes (curta, consultiva, fechamento, áudio).
- Modelo de negócio: freemium com planos via Stripe (FREE, STARTER, CLOSER, PRO, TEAM).
- Infra inferida: Vercel para frontend/backend (`.vercel` presente no `.gitignore`); PostgreSQL externo gerenciado.
- Repositório público no GitHub (`felbranns-oss/orka-closer`).
- Nenhum segredo versionado encontrado — situação mais limpa entre os repositórios analisados até aqui.

### 2) Arquitetura atual (visão macro)
- Aplicação full-stack Next.js 16 (App Router): páginas, API Routes e middleware Edge no mesmo repositório.
- Prisma 7 com `pg` pool para PostgreSQL — acesso ao banco direto das API Routes.
- NextAuth v5 (Auth.js) com estratégia JWT + credentials; bcrypt para senhas.
- Middleware Edge protege rotas do dashboard com redirecionamento; prevenção de open redirect via `getSafeAuthRedirectPath`.
- Anti-abuso por design: fingerprint e IP armazenados exclusivamente como hashes (`FreeAnalysisUsage`, `UserSignupDevice`, `UserSignupIp`, `AnalysisRateLimit`) — minimização de PII no banco.
- Uso de OpenAI rastreado por requisição: modelo, tokens de entrada/saída e custo estimado registrados em `UsageLog`.
- Billing Stripe com enforcement server-side de cotas por plano (`checkUsageBeforeAnalysis`, `incrementUserAnalysesInTransaction`).
- shadcn/ui + Tailwind CSS + Recharts para o dashboard; Zod para validação de entrada nas APIs.

### 2.1) Avaliação lifecycle do repositório orka-closer (1-12)
- **1. Source Control & Collaboration:** **Deficiente** (**Nota: 4/10**)
  Evidência: commits informais ("links", "ajuste planos", "adm felbranns"); sem branch strategy, PR templates ou CODEOWNERS; `.gitignore` adequado.
- **2. Local Development:** **Parcial** (**Nota: 4/10**)
  Evidência: `.env` não versionado; ausência de `.env.example` — desenvolvedor novo não tem referência de variáveis necessárias; sem `docker-compose` para PostgreSQL local; sem `.nvmrc`.
- **3. Code Quality:** **Parcial** (**Nota: 4/10**)
  Evidência: TypeScript, ESLint e Zod em uso; validação de entrada nas APIs; sem testes unitários ou de integração (0 arquivos `.spec` ou `.test`).
- **4. CI:** **Muito fraco** (**Nota: 2/10**)
  Evidência: sem pipeline observado; ausência de `.github/workflows`.
- **5. CD:** **Parcial** (**Nota: 6/10**)
  Evidência: Next.js + Vercel inferido pelo `.gitignore`; `prisma migrate deploy` executado no script `build` — migrações acontecem no momento do build, não do deploy, criando risco de incompatibilidade entre schema e código em caso de falha parcial.
- **6. Infrastructure:** **Parcial** (**Nota: 6/10**)
  Evidência: Vercel adequado para Next.js full-stack; PostgreSQL gerenciado externo; sem Redis (processamento síncrono, adequado ao volume atual); sem IaC.
- **7. Configuration & Secrets:** **Parcial** (**Nota: 6/10**)
  Evidência: `.env*` coberto pelo `.gitignore`; nenhum segredo versionado; ausência de `.env.example` prejudica onboarding e documentação de variáveis necessárias.
- **8. Observability:** **Muito fraco** (**Nota: 2/10**)
  Evidência: `UsageLog` rastreia custos de IA por requisição — diferencial positivo; `console.log` com `userId` no middleware expõe PII em logs de produção; sem error tracking, métricas ou alertas.
- **9. Security:** **Parcial** (**Nota: 6/10**)
  Evidência: proteção de rotas via middleware Edge; prevenção de open redirect; hashing de fingerprint e IP (PII minimization); bcrypt + JWT; Zod nas APIs; `console.log` com `userId` no middleware — PII em logs (`middleware.ts:39-44`).
- **10. Data:** **Bom** (**Nota: 8/10**)
  Evidência: 4 migrações versionadas; hashing de dados sensíveis por design; `UsageLog` para rastreabilidade de custo; sem evidência de backup/restore drill.
- **11. Release & Operations:** **Parcial** (**Nota: 4/10**)
  Evidência: Vercel simplifica o deploy do Next.js; `prisma migrate deploy` no build — risco operacional; sem runbook, SLO ou processo de incidente.
- **12. Compliance & Legal:** **Parcial** (**Nota: 4/10**)
  Evidência: processa conversas de WhatsApp de terceiros (dados de clientes finais dos corretores); hashing de IP e fingerprint alinhado à minimização de PII da LGPD; avaliação completa não realizada nesta rodada.

### 3) Pontos fortes
- Nenhum segredo versionado — repositório mais limpo do grupo até aqui.
- Prevenção de open redirect implementada (`getSafeAuthRedirectPath`).
- PII minimização por design: fingerprint e IP armazenados apenas como hashes.
- Billing com enforcement server-side de cotas — controle de uso robusto.
- Rastreamento de custo de IA por requisição (`UsageLog` com tokens e custo estimado).
- Zod para validação de entrada em todas as APIs críticas.
- Stack coesa: Next.js full-stack elimina a complexidade de backend separado para o escopo do produto.

### 4) Deficiências técnicas

Nenhum achado classificado como Crítico neste repositório.

#### Resumo dos achados por severidade
- **Alto:** 2 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco de quebra silenciosa em produção por `prisma migrate deploy` no build — migração e código podem ficar dessincronizados em falha parcial.
- Risco de atraso em roadmap por ausência de testes — regressões chegam à produção sem barreira.
- Risco de vazamento de PII (userId) em logs de produção via `console.log` no middleware.
- Risco de onboarding lento de novos colaboradores por ausência de `.env.example`.

### 6) Impacto esperado
- Segurança: Médio
- Estabilidade operacional: Médio/Alto
- Velocidade de evolução: Médio/Alto

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **4,7 / 10**
- **Justificativa:** repositório com a maior maturidade de segurança do grupo — nenhum achado crítico, prevenção de open redirect, PII minimização por design e billing com enforcement server-side. Nota limitada pela ausência total de testes, pipeline de CI inexistente e risco operacional na estratégia de migração.

---

---

## Repositório: Leads-Orka

### 1) Contexto do repositório
- Repositório com backend e frontend separados em subpastas:
  - `backend`: Express.js (Node.js, JavaScript puro) com PostgreSQL via `pg` raw pool.
  - `frontend`: React 18 + Vite (JavaScript puro).
- Produto: ferramenta de geração de leads para imobiliárias — busca empresas próximas via Google Places API, pontua e desbloqueia dados de contato com sistema de créditos comprados via Stripe.
- Stack mais simples e antiga do grupo: sem TypeScript, sem ORM, sem framework de validação.
- Repositório público no GitHub (`felbranns-oss/Leads-Orka`).
- Múltiplos achados críticos de segurança — repositório com maior risco imediato do grupo.

### 2) Arquitetura atual (visão macro)
- Backend Express.js com rotas separadas por domínio (auth, admin, searches, credits, leads-unlock).
- Middleware de autenticação JWT manual (`middleware/auth.js`).
- Acesso ao banco via SQL raw (sem ORM); migrações manuais em `scripts/migrate.js`.
- Frontend React 18 SPA com React Router; sem TypeScript ou lint no backend.
- Stripe para compra de créditos; Google Places API para busca de leads.
- `vercel.json` no frontend — indica Vercel para hospedagem do frontend.
- Backend sem configuração de deploy observada; DATABASE_URL aponta para Railway.

### 2.1) Avaliação lifecycle do repositório Leads-Orka (1-12)
- **1. Source Control & Collaboration:** **Muito fraco** (**Nota: 2/10**)
  Evidência: `.gitignore` existe na raiz, mas `backend/.env` com credenciais reais está rastreado; `backend/node_modules/` rastreado; commits informais; sem branch strategy, PR templates ou CODEOWNERS.
- **2. Local Development:** **Muito fraco** (**Nota: 2/10**)
  Evidência: `.env.example` presente; `.env` real versionado (contradição); `node_modules` versionado; sem `docker-compose`; sem `.nvmrc`.
- **3. Code Quality:** **Muito fraco** (**Nota: 2/10**)
  Evidência: JavaScript puro em backend e frontend (sem TypeScript); sem lint no backend; sem testes; SQL raw sem ORM; validação de entrada ad hoc; erros internos expostos via `e.message` nas respostas 500.
- **4. CI:** **Inexistente** (**Nota: 1/10**)
  Evidência: sem pipeline de CI observado.
- **5. CD:** **Parcial/Deficiente** (**Nota: 4/10**)
  Evidência: `vercel.json` no frontend; backend sem configuração de deploy formal.
- **6. Infrastructure:** **Deficiente** (**Nota: 4/10**)
  Evidência: Railway para PostgreSQL (inferido pela DATABASE_URL); Vercel para frontend; backend sem configuração de hospedagem observada; sem IaC.
- **7. Configuration & Secrets:** **Crítico** (**Nota: 1/10**)
  Evidência: `backend/.env` com credenciais de produção reais versionado em repositório público — Google Places API Key, DATABASE_URL com senha e host do Railway, JWT secret previsível.
- **8. Observability:** **Inexistente** (**Nota: 1/10**)
  Evidência: health endpoint básico presente; sem logging estruturado, error tracking ou alertas.
- **9. Security:** **Crítico** (**Nota: 1/10**)
  Evidência: CORS completamente aberto (`app.use(cors())` sem origin whitelist); credenciais de banco de dados e API key reais expostas publicamente; JWT secret fraco e previsível (`leadscan2024_super_secreto...`); SSL com `rejectUnauthorized: false`; health endpoint expõe estado de configuração da API key.
- **10. Data:** **Deficiente** (**Nota: 4/10**)
  Evidência: migrações em SQL raw sem versionamento formal; banco de dados de produção acessível via credenciais expostas publicamente; sem evidência de backup/restore.
- **11. Release & Operations:** **Muito fraco** (**Nota: 2/10**)
  Evidência: sem runbook, SLO ou processo de incidente; deploy manual sem automação observada.
- **12. Compliance & Legal:** **Hipótese a validar** (**Nota: 2/10**)
  Evidência: coleta e armazena dados de empresas e contatos via Google Places; LGPD aplicável — não avaliado nesta rodada.

### 3) Pontos fortes
- `.env.example` presente — intenção de documentar variáveis existe.
- Bcrypt para hash de senhas — prática correta preservada mesmo na stack mais simples.
- Separação de rotas por domínio no backend — alguma organização estrutural.
- Stripe integrado com handler de webhook dedicado.

### 4) Deficiências técnicas

#### [Crítico] Credenciais reais de produção versionadas em repositório público
- `backend/.env` rastreado com Google Places API Key real (billable), DATABASE_URL com senha e host Railway e JWT secret fraco — qualquer pessoa com acesso ao repositório tem acesso direto ao banco de produção (`backend/.env`).

#### [Crítico] CORS completamente aberto
- `app.use(cors())` sem restrição de origin permite requisições de qualquer domínio a todas as rotas autenticadas (`backend/server.js:19`).

#### [Crítico] `node_modules` versionado
- `backend/node_modules/` rastreado pelo git — repositório inflado, drift de dependências e risco de supply chain via pacotes modificados localmente.

#### Resumo dos demais achados por severidade
- **Alto:** 2 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco imediato de acesso não autorizado ao banco de produção — credenciais públicas no GitHub.
- Risco de custo financeiro não controlado por abuso da Google Places API Key exposta.
- Risco de comprometimento de todas as sessões de usuários por JWT secret fraco e público.
- Risco de requisições cross-origin maliciosas por CORS irrestrito.
- Risco de drift e ataques de supply chain por `node_modules` versionado.

### 6) Impacto esperado
- Segurança: Crítico (ação imediata necessária)
- Estabilidade operacional: Alto
- Velocidade de evolução: Alto

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **2,2 / 10**
- **Justificativa:** repositório com menor maturidade técnica do grupo — stack sem TypeScript, sem ORM, sem testes e sem CI, combinado com três achados críticos ativos (credenciais reais públicas, CORS aberto, `node_modules` versionado). Evidencia um MVP construído rapidamente sem práticas de engenharia estabelecidas. O produto tem lógica de negócio funcional, mas a base técnica exige refatoração significativa.

---

---

## Repositório: orka-pages

### 1) Contexto do repositório
- Repositório full-stack único: Next.js 16 (App Router + API Routes) com Prisma 6 + PostgreSQL.
- Produto: plataforma de landing pages para empreendimentos imobiliários — cada landing captura leads, roteia para o corretor responsável via e-mail (Resend) e WhatsApp, e é gerenciada por um admin panel.
- Multi-tenant: cada landing pertence a um cliente (construtora) e opcionalmente a um corretor específico.
- Roteamento por domínio via middleware Edge: hostname → slug → página do empreendimento.
- Repositório público no GitHub (`felbranns-oss/orka-pages`).
- Nenhum segredo versionado — situação limpa, mesma qualidade do `orka-closer`.

### 2) Arquitetura atual (visão macro)
- Next.js 16 App Router: landing pages públicas em `app/[slug]`, painel admin em `app/admin`, API de leads em `app/api/leads`.
- Middleware Edge resolve hostname → slug para domínios customizados e protege rotas `/admin` com cookie de sessão.
- NextAuth v5 com credentials — autenticação restrita ao painel admin; usuários públicos não se autenticam.
- Prisma 6 para PostgreSQL: modelos `AdminUser`, `Client`, `Broker`, `Landing` e `Lead`.
- Resend para e-mail transacional; WhatsApp outbox para notificação de corretores.
- Pasta `supabase/` com schema SQL coexiste com Prisma — evidência de migração incompleta ou infraestrutura dual hipótese a validar.
- Mapa de domínios hardcoded em `lib/landings/domain-map.ts` — cada cliente novo exige edição manual de código.

### 2.1) Avaliação lifecycle do repositório orka-pages (1-12)
- **1. Source Control & Collaboration:** **Deficiente** (**Nota: 4/10**)
  Evidência: commits informais ("email enviado automatico no symphony", "ajuste casaluxo"); sem branch strategy, PR templates ou CODEOWNERS; `.gitignore` adequado.
- **2. Local Development:** **Deficiente** (**Nota: 4/10**)
  Evidência: `.env` não versionado; ausência de `.env.example`; sem `docker-compose` para PostgreSQL local; sem `.nvmrc`.
- **3. Code Quality:** **Parcial** (**Nota: 6/10**)
  Evidência: TypeScript em todo o projeto; ESLint configurado; validação de lead bem estruturada com clamping e sanitização; sem testes unitários ou de integração.
- **4. CI:** **Muito fraco** (**Nota: 2/10**)
  Evidência: sem pipeline observado; ausência de `.github/workflows`.
- **5. CD:** **Parcial** (**Nota: 4/10**)
  Evidência: Vercel inferido pelo `.gitignore` (`.vercel` ignorado); sem `vercel.json`, `railway.toml` ou Dockerfile observados; Next.js + Vercel é zero-config por natureza.
- **6. Infrastructure:** **Parcial** (**Nota: 6/10**)
  Evidência: Vercel adequado para Next.js; PostgreSQL externo; coexistência de Supabase schema e Prisma sem estratégia clara documentada.
- **7. Configuration & Secrets:** **Parcial** (**Nota: 6/10**)
  Evidência: `.env*` coberto pelo `.gitignore`; nenhum segredo versionado; ausência de `.env.example`; mapa de domínios de clientes hardcoded em código (`lib/landings/domain-map.ts`) em vez de configuração por ambiente.
- **8. Observability:** **Muito fraco** (**Nota: 2/10**)
  Evidência: `console.warn` e `console.error` em pontos de falha de notificação; sem logging estruturado, error tracking ou alertas.
- **9. Security:** **Parcial** (**Nota: 6/10**)
  Evidência: validação de lead com sanitização e clamping; prevenção de open redirect no admin middleware (`safeNext` com whitelist `/admin`); auth corretamente separada entre Edge e servidor; API pública de captura de leads (`POST /api/leads`) sem rate limiting — suscetível a spam e abuso.
- **10. Data:** **Parcial** (**Nota: 6/10**)
  Evidência: 1 migração versionada; schema Prisma bem definido; coexistência com schema SQL do Supabase — hipótese de dívida técnica de migração; sem evidência de backup/restore drill.
- **11. Release & Operations:** **Parcial** (**Nota: 4/10**)
  Evidência: Vercel simplifica o ciclo de deploy; sem runbook, SLO ou processo de incidente.
- **12. Compliance & Legal:** **Parcial** (**Nota: 4/10**)
  Evidência: coleta PII de leads (nome, telefone, e-mail); dados vinculados a empreendimentos e corretores; LGPD aplicável; avaliação completa não realizada nesta rodada.

### 3) Pontos fortes
- Nenhum segredo versionado — repositório limpo.
- Validação de lead bem estruturada: sanitização, clamping de campos e rejeição de slugs desconhecidos.
- Prevenção de open redirect no admin middleware.
- Auth corretamente separada entre Edge middleware (cookie) e servidor (NextAuth completo).
- Roteamento por domínio via middleware — arquitetura escalável para múltiplos clientes com domínios próprios.
- Resend para e-mail transacional — melhor que SMTP manual.

### 4) Deficiências técnicas

Nenhum achado classificado como Crítico neste repositório.

#### Resumo dos achados por severidade
- **Alto:** 2 achados
- **Médio:** 2 achados
- **Baixo:** 1 achado

### 5) Riscos de negócio e operação
- Risco de spam e abuso de leads por ausência de rate limiting na API pública de captura.
- Risco de erro operacional e atraso de onboarding por mapa de domínios hardcoded — cada cliente novo exige deploy de código.
- Risco de inconsistência de dados por coexistência não documentada de Supabase e Prisma.
- Risco de regressões silenciosas por ausência de testes e CI.

### 6) Impacto esperado
- Segurança: Médio
- Estabilidade operacional: Médio
- Velocidade de evolução: Médio/Alto

### 7) Nota de maturidade técnica (0-10)
- **Nota:** **4,5 / 10**
- **Justificativa:** repositório limpo e com boas práticas pontuais (validação, open redirect, auth segregada). Nota limitada pela ausência de testes, CI inexistente, endpoint público sem rate limiting e coexistência não resolvida entre Supabase e Prisma. Potencial de evolução alto dado o escopo do produto e a qualidade de código observada.

---

## Próximos repositórios (fila)
- [x] `ORKA` (mono-repo: orka-pro-backend + orka-pro-dashboard)
- [x] `orka-home`
- [x] `orka-save`
- [x] `orka-closer`
- [x] `Leads-Orka`
- [x] `orka-pages`

## Notas de atualização
- Este documento deve ser evoluído incrementalmente a cada novo repositório.
- Regras editoriais vigentes em `dossie_prompt.md`.
