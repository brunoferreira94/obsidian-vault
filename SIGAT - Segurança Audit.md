# Mapa de Riscos e Requisitos de Segurança - SIGAT v3

## Contexto
SIGAT (Sistema Integrado de Gestão da Arrecadação Tributária) - migração de monolito WebForms para arquitetura de microserviços .NET 10.

## Ameaças Identificadas

### AMEACA-001: Ausência de Autenticação e Autorização
**Severidade:** CRÍTICA
- APIs .NET expõem endpoints públicos sem JWT, API Key ou Authorization header
- Todos os endpoints estão abertos sem validação de identidade
- Gateway não implementa middleware de auth

### AMEACA-002: Credenciais Fixas em docker-compose.yml
**Severidade:** CRÍTICA
- Senhas `sigat123` expostas em texto plano no repositório
- Credenciais do PostgreSQL e RabbitMQ não-externalizadas
- Risco de deploy acidental com credenciais de desenvolvimento

### AMEACA-003: Swagger Exposto em Produção
**Severidade:** ALTA
- Swagger habilitado sem restrição por ambiente
- Exposição de todos os contratos de API
- Facilita enumeração de endpoints para atacantes

### AMEACA-004: Injeção de SQL via ProcedureExecutionService
**Severidade:** ALTA
- `ProcedureExecutionService` aceita qualquer procedureName validada apenas contra HashSet
- Mock results não sanitizam parâmetros de entrada
- Risco se substituído por repositório real sem validação

### AMEACA-005: Transferência de Dados Não Encriptada
**Severidade:** MÉDIA
- Comunicação HTTP (não HTTPS) entre services
- Credenciais/passaportes via query/header não protegidos
- Nginx proxy não inclui headers de segurança

### AMEACA-006: Falta de Rate Limiting
**Severidade:** MÉDIA
- Nenhum limite de requisições por IP/usuário
- Endpoints críticos podem ser abusados
- Sem proteção contra brute force ou DoS

### AMEACA-007: InMemory Repository não Persistente
**Severidade:** MÉDIA
- Dados sensíveis em memória não são persistidos com segurança
- Reinício de API causa perda de dados
- Logs podem conter informações sensíveis sem trilha de auditoria

### AMEACA-008: CORS Não Configurado
**Severidade:** MÉDIA
- APIs não declararam política de CORS
- Qualquer origem pode fazer chamadas cross-origin
- Risco de CSRF em operações sensíveis

### AMEACA-009: Headers de Segurança Ausentes
**Severidade:** BAIXA
- Falta X-Content-Type-Options, X-Frame-Options, Content-Security-Policy
- APIs respondem sem headers de proteção
- Vulnerável a clickjacking e XSS

### AMEACA-010: Falta de Validação de Entrada Robusta
**Severidade:** ALTA
- DTOs não têm validação fluent ou data annotations
- CNPJ/CPF não validados rigorosamente
- Valores monetários sem range checks

## Controles Exigidos

### CONT-001: Autenticação JWT
- Implementar middleware JWT Bearer em todos os APIs
- Claims mapping para permissões por contexto
- Validação de issuer/audience configurada

### CONT-002: Authorization por Contexto
- Claims-based authorization para bounded contexts
- Roles: Admin, Operador, Consulta
- Políticas: `RequireAdmin`, `RequireOperador`, `AllowAnonymousRead`

### CONT-003: Secret Externalization
- Migrar todas as credenciais para `.env` ou Azure Key Vault
- Connection strings via variáveis de ambiente
- Nunca versionar secrets no repositório

### CONT-004: HTTPS/TLS
- Habilitar HTTPS nos Dockerfiles
- Redirects HTTP para HTTPS
- Certificados de desenvolvimento para local

### CONT-005: Rate Limiting
- Implementar AspNetCoreRateLimiting
- Limite: 100 req/min por IP para endpoints GET
- Limite: 20 req/min para endpoints POST críticos
- Whitelist para IPs internos

### CONT-006: Security Headers Middleware
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- Content-Security-Policy para APIs restringindo origens

### CONT-007: CORS Restrito
- Origins whitelist: domínios oficiais SEFAZ
- Métodos: GET, POST, PUT, DELETE conforme operação
- Headers: Authorization, Content-Type

### CONT-008: Input Validation
- Data Annotations em todos Request DTOs
- FluentValidation para regras complexas
- CNPJ/CPF validators customizados

### CONT-009: LGPD Compliance
- Endpoint `/me/export` para exportação de dados pessoais
- Campos de compliance: `ContractDocumentId`, `ContractSignedAt`
- Anonymization workflow via `/me` endpoints

### CONT-010: Security Logging
- Serilog com structured logging
- Audit trail para operações sensíveis
- CorrelationId em todos os requests

## Requisitos de Segurança

### REQ-001: Autenticação
**Critério de Aceitação:** Todas as APIs rejeitam requisições sem token JWT válido.

### REQ-002: Autorização
**Critério de Aceitação:** Endpoints `/api/credito/pafs/confirmar-geracao` verificam role `OperadorCredito`.

### REQ-003: Secrets Management
**Critério de Aceitação:** `docker-compose.yml` usa variáveis de ambiente `${POSTGRES_PASSWORD}`.

### REQ-004: HTTPS
**Critério de Aceitação:** APIs respondem apenas em HTTPS em produção.

### REQ-005: Rate Limiting
**Critério de Aceitação:** Requisições excessivas retornam 429 Too Many Requests.

### REQ-006: Security Headers
**Critério de Aceitação:** Todas respostas incluem headers de segurança via middleware.

### REQ-007: Audit Trail
**Critério de Aceitação:** Todas operações de emissão/alteração têm log estruturado.

### REQ-008: LGPD Rights
**Critério de Aceitação:** Usuário pode exportar, corrigir e anonimizar seus dados via `/me/*` endpoints.

## Priorização (P0/P1/P2)

| Item | Prioridade | Justificativa |
|------|------------|---------------|
| REQ-001 | P0 | APIs instauradas sem QUALQUER auth - risco imediato |
| REQ-002 | P0 | Conflito de acesso a operações de crédito tributário |
| REQ-003 | P0 | Credenciais de produção não podem ficar no repo |
| REQ-004 | P1 | TLS é default para APIs financeiras |
| REQ-005 | P1 | Proteção contra abuso de endpoints públicos |
| REQ-006 | P2 | Headers de segurança mitigam ataques auxiliares |
| REQ-007 | P1 | Trilha de auditoria obrigatória para arrecadação |
| REQ-008 | P2 | LGPD compliance escalonado via iteração |

## Próximos Passos

1. **Imediato (P0):** Wireframe security middleware em todos APIs
2. **Curto prazo (P1):** Implementar JWT auth, rate limiting, audit logging
3. **Médio prazo (P2):** CORS, security headers, LGPD endpoints

---
*Mapeamento criado em 2026-06-23 como parte da auditoria de segurança SIGAT*