# School Management - Features Criativas Sugeridas

## Sobre
Sugestões de funcionalidades para facilitar a rotina escolar, geradas pela auditoria automática em 13/06/2026.

## Features Implementadas ✅
- **Rate limiting** - Proteção contra brute force e spam nas APIs
- **CAPTCHA** - Verificação humanizada nos formulários
- **Health check** - Monitoramento de sistema `/health`

## Features em Desenvolvimento ⏭
- **Security headers middleware** - CSP, HSTS, X-Frame-Options

## Novas Sugestões 💡

### Comunicação & Interatividade
- [[Chatbot para dúvidas]] - IA respondendo sobre BNCC, notas, faltas dos alunos
- [[Notificações push proativas]] - Alertas automáticos para pais/professores

### Mobile & Acessibilidade
- [[App mobile PWA]] - Instalar no celular dos pais diretamente do navegador
- [[Reconhecimento de voz]] - Digitar notas e ocorrências por áudio
- [[Integração calendário]] - Google/Outlook para eventos escolares

### Gestão Acadêmica
- [[Digitalização de provas]] - Corrigir com OCR e feedback instantâneo
- [[Ranking de notas]] - Competição saudável entre turmas
- [[Gestão de bolsas]] - Automatizar bolsas de estudo baseado em desempenho
- [[Ficha de ocorrências]] - Sistema de disciplina integrado

### Operacional
- [[Rastreamento de ônibus]] - Pais acompanham trajeto em tempo real
- [[Cardápio semanal]] - Alunos veem refeições do dia
- [[Biblioteca digital]] - Livros interativos online com empréstimo

---

*Gerado automaticamente pela auditoria de segurança - cron job a cada 12h*

## Análise Técnica Atual (Junho/2026)

### Arquitetura
- Monorepo com submodule `school-management-ui`
- Frontend: Angular 22 + Tailwind v4 + Signals (sem Angular Material)
- Backend: .NET 10 + LiteBus CQRS + PostgreSQL

### Telas de Setup Refatoradas
- **`/setup/wizard`** - Assistente de configuração inicial (salas, cursos, turmas)
- **`/security`** - Foco em auditoria com link direto para configuração
- **`/enrollments`** - FormArray dinâmico para CPFs e IDs de turmas
- **`/communications/engine`** - Campos específicos por tipo de evento (PaymentDue, AttendanceLow, GradePosted)

### Sugestões Técnicas 💡

#### Setup Wizard
- [ ] Validação inline de CPF/CNPJ no campo de unidade escolar
- [ ] Importação em lote via CSV/Excel para turmas e cursos
- [ ] Tooltips explicativos nos campos (ex: "Código da sala: SIGE, Lab Info, etc")
- [ ] Validação de capacidade mínima de turmas (10-40 alunos)
- [ ] Template de exemplo para download (exemplo-turmas.csv)

#### Experiência do Usuário
- [ ] Skeleton loading nas listagens (tabelas de alunos, professores)
- [ ] Busca instantânea com debounce (500ms) em tabelas grandes
- [ ] Filtros salvos no localStorage (últimos filtros usados)
- [ ] Atalhos de teclado (Ctrl+K para busca global, Esc para fechar modais)

#### Segurança
- [ ] Autologout após inatividade (configurável: 15/30/60 min)
- [ ] Confirmação de senha para ações críticas (excluir turma)
- [ ] Histórico de mudanças no setup wizard (quem alterou o quê)

#### Mobile/PWA
- [ ] Indicador visual "instalável" na tela inicial
- [ ] Notificações push para eventos críticos
- [ ] Modo offline para consulta de notas/faltas