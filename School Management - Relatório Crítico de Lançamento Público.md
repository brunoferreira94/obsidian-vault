# School Management — Relatório Crítico de Lançamento Público

> Data: 2026-06-20  
> Perfil: founder / C-level  
> Veredito: **não lançar publicamente ainda**. O produto tem núcleo funcional relevante, mas ainda existem bloqueadores de estabilidade, segurança, billing, self-service, qualidade automatizada e operação que tornam um lançamento público amplo arriscado.

## 1. Veredito executivo

**Nota de prontidão para lançamento público: 5,8 / 10**

O School Management já entrega valor real em áreas centrais: gestão escolar, autenticação, permissões, LGPD básica, termos de uso, Owner Dashboard, relatórios, assinaturas e uma primeira integração de checkout com Asaas. Porém, para lançamento público, o produto ainda parece mais próximo de um **piloto assistido / beta fechado** do que de um SaaS pronto para aquisição autônoma.

A decisão recomendada é:

1. **não abrir cadastro público amplo agora**;
2. liberar apenas **piloto fechado com onboarding manual**;
3. resolver os bloqueadores P0 antes de marketing, checkout público e expansão comercial.

## 2. O que já está bom

### Produto e funcionalidades

- Base de gestão escolar funcional.
- Owner Dashboard implementado e validado via API/UI.
- Teacher Availability implementado em service/tests, com integração ao scheduler ainda a validar.
- Relatórios críticos funcionando:
  - API `200 OK`;
  - componente UI gera relatório com `series.length = 2`.
- Termos de uso e cookie consent aparecem implementados em backend e frontend.
- Planos `Free`, `Premium` e `Enterprise` já existem como modelo de monetização.
- Primeira camada de checkout Asaas existe.

### Engenharia

- Build backend local passou.
- Build frontend passou.
- Lint frontend passou.
- OpenSpec já é usado como fonte de planejamento.
- Docker Compose dev/prod existe.
- CI/CD já tem pipeline com backend, frontend, E2E e deploy.

## 3. Bloqueadores críticos — P0

Estes itens impedem lançamento público amplo.

### 3.1 Estabilidade da API em desenvolvimento/produtização

Houve instabilidade em validação manual:

- API local encerrou com `exit_code=-15`;
- `AuditLogCleanupService.cs:22` lançou `TaskCanceledException` e parou o host;
- erro de persistência em `checkInsertTargets` indicou incompatibilidade modelo/banco ou migration pendente.

**Risco founder:** se a API trava em ambiente controlado, vai travar em cliente real. Isso destrói confiança no piloto.

### 3.2 Testes automatizados não são confiáveis como gate de release

Evidências:

- suite .NET completa teve falhas relevantes;
- teste de roleGuard não roda localmente por ausência de Chrome/Chromium;
- CI permite `continue-on-error: true` em lint e testes frontend;
- testes críticos de Termos de Uso e Asaas falharam em execução direcionada:
  - `TermsOfUseAccept_ShouldPersistAndReturnAcceptedStatus`;
  - `TermsOfUseStatus_ShouldRequireReaccept_WhenVersionChanges`;
  - `AsaasCheckoutServiceTests.CreateCheckoutIntentAsync_WithBoleto_ReturnsCheckoutUrl`.

**Risco founder:** release depende mais de validação manual do que de CI confiável.

### 3.3 Billing/checkout não está pronto para cobrança pública

Existe integração Asaas, mas faltam evidências de produção:

- credenciais sandbox/prod segregadas;
- webhook validado com provider real;
- idempotência e reconciliação financeira validadas;
- painel de conciliação;
- fluxo de falha, estorno, cancelamento e retry;
- decisão comercial sobre trial, desconto anual, upgrade/downgrade.

**Risco founder:** vender `Premium` sem billing confiável gera churn, suporte manual e risco financeiro.

### 3.4 Portal do Responsável / Self-service ainda não fecha a promessa principal

O portal familiar é peça central da proposta de valor do `Premium`, mas ainda faltam:

- fluxo completo validado de login, MFA/SSO e escopo;
- dashboard do responsável com primeiro valor em até 10 minutos;
- boletos, notas, frequência e comunicados validados ponta a ponta;
- acessibilidade/mobile/PWA;
- testes E2E reais.

**Risco founder:** se o `Premium` é vendido como portal + financeiro + analytics, o cliente precisa perceber valor rápido. Sem isso, o preço não se sustenta.

### 3.5 LGPD e governança jurídica ainda não estão completas

Há termos de uso e cookie consent implementados, mas ainda faltam:

- texto jurídico final;
- DPA/contrato B2B por tenant;
- governança documental;
- processo de exportação/correção/exclusão de dados;
- política de retenção/anonimização para analytics.

**Risco founder:** SaaS escolar lida com dados sensíveis de menores. Erro aqui é muito mais caro que uma feature atrasada.

## 4. Riscos altos — P1

### 4.1 Infraestrutura de produção

Pontos de atenção:

- `docker-compose.prod.yml` expõe API/UI/Postgres em Docker Compose simples;
- não há evidência de backup automatizado;
- não há plano de rollback;
- não há health check público robusto;
- não há estratégia clara de migrações em produção;
- TLS depende de configuração manual no Nginx;
- secrets ainda dependem de variáveis de ambiente.

### 4.2 CI/CD inconsistente

- `.github/workflows/ci-cd.yml` usa `.NET 8.0.x`;
- o projeto roda `net10.0` com pacotes `10.0.0`;
- isso pode quebrar CI antes mesmo de validar produto.

### 4.3 Observabilidade e operação

Apesar de OpenTelemetry existir, ainda faltam:

- dashboards de negócio e saúde;
- alertas de erro crítico;
- runbooks de incidentes;
- rastreamento de falhas de checkout;
- monitoramento de filas/notificações.

### 4.4 Experiência de onboarding

Falta um onboarding guiado que leve uma escola de:

1. cadastro;
2. convite de responsáveis;
3. importação de alunos;
4. primeiro boleto/comunicado;
5. primeiro login de responsável;
6. primeiro valor percebido.

Sem isso, o produto depende de suporte manual.

## 5. Melhorias importantes — P2

- Refinar pricing page e FAQ.
- Criar landing pública com CTA claro.
- Melhorar analytics avançados, mas sem transformar em bloqueador de MVP.
- Aprimorar Owner Dashboard com métricas de ativação, MRR, churn e TTV.
- Melhorar documentação para administradores e escolas.
- Criar material de suporte e SLA mínimo.

## 6. Roadmap recomendado

### Antes de lançamento público — P0

1. Estabilizar API e migrations.
2. Corrigir testes críticos de Termos de Uso e Asaas.
3. Garantir CI verde sem `continue-on-error` em gates críticos.
4. Validar billing com sandbox real e webhook.
5. Fechar portal do responsável com fluxo ponta a ponta.
6. Finalizar LGPD/DPA/termos.
7. Criar runbook de deploy, backup e rollback.

### Piloto fechado — P1

1. Onboarding assistido de 3 a 5 escolas.
2. Medir tempo até primeiro valor.
3. Validar cobrança em ambiente controlado.
4. Coletar evidências de suporte e fricção.
5. Ajustar pricing e narrativa.

### Lançamento público — P2

1. Landing pública.
2. Checkout self-service.
3. Suporte escalável.
4. Analytics de negócio.
5. Expansão comercial gradual.

## 7. Nota final

**Conclusão:** o produto tem base forte, mas ainda não está pronto para lançamento público amplo. A melhor estratégia é transformar o próximo ciclo em uma fase de **piloto fechado com critérios de saída objetivos**.

**Critérios mínimos para sair do piloto:**

- API estável em ambiente prod-like;
- CI verde e confiável;
- billing validado com provider real;
- portal do responsável validado ponta a ponta;
- LGPD e contratos finalizados;
- onboarding documentado;
- suporte operacional preparado.

## 8. OpenSpec change relacionado

- `openspec/changes/finalize-public-launch-readiness/`
