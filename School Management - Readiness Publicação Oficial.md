# School Management — Readiness para Publicação Oficial Aberto

> Data: 2026-06-23  
> Perfil: founder / C-level  
> **Veredito: NÃO PRONTO para cadastro público aberto**

## Gaps Críticos Identificados

### P0 - Bloqueiam cadastro público aberto

| Item | Status | Gap real |
|---|---|---|
| Portal Responsável TTV | ❌ | Sem validação real com escola piloto |
| Billing automation | ⚠️ | Sandbox validada, prod real pendente |
| LGPD completo | ⚠️ | Termos/DPA existem, processos não testados |
| CI confiável | ❌ | Arquivos .bak indicam suite quebrada |
| Onboarding self-service | ❌ | Dependente de suporte manual |

### P1 - Riscos altos se publicação acontecer

| Item | Gap |
|---|---|
| Credenciais prod/sandbox | Segregação incompleta |
| Testes E2E reais | Sem evidência de browser automation |
| Backup automatizado | Apenas manual |
| Rollback documentado | Não testado |

### P2 - Melhoria esperável

| Item | Gap |
|---|---|
| Pricing page | Não validado com billing real |
| Landing pública | Existe mas sem integração checkout |
| Suporte escalável | Precisa do portal piloto primeiro |

---

## Recomendações Founder

### Para abrir cadastro público ABERTO:

1. **Validar Portal Responsável** com 3 escolas piloto reais
2. **Garantir billing prod** (credenciais segregadas + teste em sandbox real)
3. **Arquivar arquivos .bak** e corrigir suite de testes quebradas
4. **Testar runbook de deploy** em ambiente real
5. **Resolver CI/CD** - remover continue-on-error e garantir .NET 10 fiel

### Não fazer antes de resolver P0:

- Abrir cadastro público amplo
- Marketing de "self-service imediato"
- Preços enterprise sem billing testado